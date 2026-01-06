外出先とのこと、承知いたしました。
帰宅後にPCとPixel 5を前にして、上から順に実行すれば構築が完了する「完全手順書（README.md形式）」を作成しました。
以下のテキストをコピーして、Notionやメモ帳に保存しておいてください。
# Pixel 5 × Mac mini : Googleフォト完全自動化システム構築手順書

## 概要
Mac miniの特定フォルダに画像を保存すると、USB接続されたPixel 5へ自動転送され、Googleフォトへ無制限画質でアップロードされます。アップロード完了後、Pixel 5内のデータは自動削除されます。

## 前提条件
* **Mac mini (Host):** 24時間稼働、画像保存元。
* **Pixel 5 (Client):** Googleフォト無制限対象機。
* **ケーブル:** 高品質なUSB-Cケーブル（データ通信対応）。

---

## Phase 1: Pixel 5 の環境構築 (Root化 & バッテリー保護)
**目的:** 長期間の連続稼働に耐えるバッテリー制御と、自動操作権限の確立。

### 1. Bootloader Unlock & Root化
※ 既存データは全て消去されます。
1.  **開発者オプション有効化:** 「設定」>「デバイス情報」>「ビルド番号」を7回タップ。
2.  **設定変更:** 「システム」>「開発者向けオプション」で以下をON。
    * OEMロック解除
    * USBデバッグ
3.  **Unlock:** PCと接続し、`adb reboot bootloader` -> `fastboot flashing unlock` を実行。
4.  **Magisk導入:** 純正 `boot.img` をMagiskアプリでパッチし、PCから `fastboot flash boot magisk_patched.img` で書き込み。
    * *ここまでは一般的なRoot化手順のため、詳細は「Pixel 5 Root Magisk」で検索してください。*

### 2. バッテリー膨張対策 (最重要)
常時USB接続による過充電・発火を防ぎます。
1.  **Magiskアプリ** を開く。
2.  **モジュール検索:** 右下のパズルアイコンから検索バーで `ACC` (Advanced Charging Controller) を検索しインストール。再起動。
3.  **設定アプリ (AccAなど):** Playストアにはないため、F-Droid等から「AccA」などのGUIアプリを入れるか、ターミナルアプリ(Termux)で以下を実行して設定。
    ```bash
    su
    acc 60 55
    # 解説: 60%で充電停止、55%まで減ったら充電再開
    ```
4.  これで24時間繋ぎっぱなしでもバッテリーは60%付近を維持します。

---

## Phase 2: Mac mini の環境構築 (転送ツール)
**目的:** 差分のみを効率よくPixel 5へ送り込む環境を作る。

### 1. ADBコマンドの導入
ターミナルを開き、Homebrewでインストールします。
```bash
brew install android-platform-tools

2. adb-sync の導入
Google製の「変更があったファイルだけ転送するツール」を導入します。
# 任意のツール置き場へ移動
cd /usr/local/bin

# ツールをダウンロード（Google社員作成のOSS）
sudo curl -O [https://raw.githubusercontent.com/google/adb-sync/master/adb-sync](https://raw.githubusercontent.com/google/adb-sync/master/adb-sync)

# 実行権限を付与
sudo chmod +x adb-sync

※ もし adb-sync が動かない場合（Python環境依存）、後述のスクリプト内で単純な adb push に切り替えますが、基本はこれが推奨です。
Phase 3: 自動転送スクリプトの作成
目的: Macの画像をPixelへ送り、Googleフォトに「新しい画像が来たぞ」と認識させる。
1. スクリプトファイルの作成
ユーザーフォルダ直下などに作成します。
場所例: /Users/あなたのユーザー名/Scripts/sync_photos.sh
#!/bin/bash

# =================設定エリア=================
# Mac側の写真置き場 (末尾にスラッシュをつけない)
SOURCE_DIR="/Users/your_name/Pictures/NAS_Upload"

# Pixel 5側の保存先
DEST_DIR="/sdcard/DCIM/Transfer"
# ===========================================

# adbのパスを通す（cron実行用）
export PATH=$PATH:/opt/homebrew/bin:/usr/local/bin

# Pixel 5が接続されているかチェック
if ! adb get-state 1>/dev/null 2>&1; then
    echo "$(date): Pixel 5 not found." >> /tmp/photo_sync.log
    exit 1
fi

# 転送処理 (adb-syncを使用)
# --delete は付けない（Pixel側でアップロード待ちの間に消されるのを防ぐため）
echo "$(date): Start Syncing..." >> /tmp/photo_sync.log

# Pixel側にフォルダがなければ作成
adb shell mkdir -p "$DEST_DIR"

# 同期実行
# adb-syncが使えない場合は 'adb push "$SOURCE_DIR/." "$DEST_DIR"' に書き換える
adb-sync "$SOURCE_DIR" "$DEST_DIR" >> /tmp/photo_sync.log 2>&1

# 【重要】メディアスキャン
# これを実行しないとGoogleフォトが画像を検知しません
echo "$(date): Broadcasting Media Scan..." >> /tmp/photo_sync.log
adb shell am broadcast -a android.intent.action.MEDIA_SCANNER_SCAN_FILE -d file://"$DEST_DIR"

echo "$(date): Done." >> /tmp/photo_sync.log

2. 実行権限の付与
chmod +x /Users/あなたのユーザー名/Scripts/sync_photos.sh

3. 定期実行 (Cron) の設定
ターミナルで crontab -e を入力し、以下を追記します（例：1時間に1回実行）。
0 * * * * /Users/あなたのユーザー名/Scripts/sync_photos.sh

Phase 4: Pixel 5 自動掃除設定 (MacroDroid)
目的: アップロードが終わったファイルを削除し、ストレージ満杯を防ぐ。
1. MacroDroidのインストール
Playストアからインストールし、Root権限を許可してください。また、アクセシビリティ権限やUI操作権限も全て許可します。
2. マクロの作成
「マクロを追加」から以下を設定します。
 * トリガー (いつやるか):
   * 「指定時刻」 -> 毎日 04:00 (Macからの転送が終わっていそうな深夜)
 * アクション (何をするか):
   * アプリを起動: Google フォト
   * 待機: 5秒 (起動待ち)
   * UI画面操作 (クリック):
     * 「アプリで自動確認」を選択 -> Googleフォトの右上のユーザーアイコンをタップするように座標指定。
   * 待機: 2秒
   * UI画面操作 (テキスト):
     * 「テキストを含むコンテンツをクリック」 -> 空き容量を増やす と入力。
   * 待機: 2秒
   * UI画面操作 (テキスト):
     * 「テキストを含むコンテンツをクリック」 -> 空き容量を増やす (確認ダイアログのボタン) と入力。
     * ※または MB GB などの文字を含むボタンをクリックさせる。
 * 条件 (念の為):
   * 「電源接続時」 (常に接続されていますが、安全策として)
運用フロー確認
 * Macの NAS_Upload フォルダ に写真を数枚入れる。
 * ターミナルで手動でスクリプトを叩いてみる。
   /Users/あなたのユーザー名/Scripts/sync_photos.sh
 * Pixel 5の画面を見て、画像が増えているか確認。
 * Googleフォトアプリを開き、**「バックアップ中」**になるか確認。
 * バックアップ完了後、MacroDroidの「マクロを試行」ボタンを押し、自動で「空き容量を増やす」処理が走るか確認。
以上で構築完了です。

