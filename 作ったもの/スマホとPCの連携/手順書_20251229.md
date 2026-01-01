**

これまでの会話（Switch自動化、C#アプリ、Tailscale環境）を踏まえ、あなたのプロジェクトを完遂させるための**「Switch自動化リモート環境（Sunshine × Moonlight）」完全構築マニュアル**を作成しました。

最新情報を確認し、最もトラブルが少なく、かつ外出先からも安定してアクセスできる手順に整理しています。

# 

---

🎮 Switch自動化リモート環境 構築マニュアル

## 📌 構成のゴール

- ホスト: 自宅のWindows PC（OBSでSwitch画面を表示中）
    
- クライアント: 外出先のスマホやPC
    
- 通信: Tailscale（VPN）経由で安全に接続
    
- 目的: 外出先からSwitchの自動化状況を確認し、自作C#ツールで操作介入する
    

## 

---

ステップ1：ホストPCの準備（Sunshine）

まずは映像と操作を送る側の設定です。ViGEmBusの導入が最重要ポイントです。

### 1-1. ViGEmBus（必須ドライバ）のインストール

これがないと操作できません。先ほどのエラーの原因です。

1. [GitHub: ViGEmBus Releases](https://www.google.com/search?q=https://github.com/nefarius/ViGEmBus/releases/latest) にアクセス。
    
2. Assets 内の ViGEmBus_Setup_x.x.x.exe をダウンロードしてインストール。
    
3. 【重要】PCを再起動する。
    

### 1-2. Sunshineのインストール

1. [GitHub: LizardByte/Sunshine](https://www.google.com/search?q=https://github.com/LizardByte/Sunshine/releases/latest) にアクセス。
    
2. sunshine-windows-installer.exe をダウンロードしてインストール。
    

### 1-3. Sunshineの初期設定

1. インストール完了後、Sunshineが起動するとブラウザが開きます。
    

- URL: https://localhost:47990
    
- ⚠️ 警告が出る場合: 「保護されていません」等の警告が出ますが、「詳細設定」→「localhostに進む」で無視してOKです（自分自身のPCへの接続なので安全です）。
    

2. ユーザー名/パスワード設定: 任意のものを設定（忘れないように）。
    
3. 日本語化: 上部メニュー Configuration → General → Language を Japanese にして Save → Apply。
    

## 

---

ステップ2：アプリケーション設定（OBS登録）

デスクトップ全体を映すより、OBS（Switch画面）を直接指定した方がスマホでの視認性が上がります。

1. Sunshine管理画面の 「Applications（アプリケーション）」 タブを開く。
    
2. 「+ Add New（追加）」 をクリック。
    
3. 以下の通りに入力：
    

- Application Name: Switch Remote（任意）
    
- Command: C:\Program Files\obs-studio\bin\64bit\obs64.exe
    

- ※OBSのインストール場所に合わせてください。
    

- Working Directory: OBSのフォルダ（例: C:\Program Files\obs-studio\bin\64bit）
    

4. 「Save（保存）」 をクリック。
    

- ※これでスマホ側のリストに「Switch Remote」というアイコンが登場します。
    

## 

---

ステップ3：クライアント接続（Moonlight）

まずは**自宅のWi-Fi（ローカル環境）**で接続テストを行います。

1. スマホ側: ストアから「Moonlight Game Streaming」をインストール。
    
2. 起動すると、同じWi-Fi内のPC（Sunshine）が自動表示されます。タップします。
    
3. PINコード（4桁の数字）が表示されます。
    
4. PC側: Sunshine管理画面の上部メニュー 「PIN」 をクリック。
    
5. スマホに出ている数字を入力して Send。
    
6. ペアリング成功。「Switch Remote」をタップしてOBSが映れば成功です！
    

## 

---

ステップ4：外出先からの接続（Tailscale連携）

ここが本番です。Tailscaleを使って外から繋ぎます。

### 4-1. IPアドレスの確認

1. PC側: タスクバーのTailscaleアイコンから、PCのIPアドレス（100.x.x.x）を確認。
    
2. スマホ側: Tailscaleアプリを開き、左上の「Active」を確認（VPNオンの状態にする）。
    

### 4-2. 手動登録（Moonlight）

Tailscale経由では自動検出されないため、手動で登録します。

1. スマホのMoonlightを開く。
    
2. 右上の 「＋」 または 「Add Host Manually」 をタップ。
    
3. 先ほど確認した 100.x.x.x （PCのTailscale IP）を入力。
    
4. これでPCアイコンが追加されます。タップして接続できるか確認してください。
    

## 

---

ステップ5：画質と通信の最適化（重要設定）

外出先（4G/5G）でもカクつかせないための設定です。スマホのMoonlight設定画面で行います。

|   |   |   |   |
|---|---|---|---|
|設定項目|外出先（4G/5G）推奨|自宅（Wi-Fi）推奨|理由|
|Video Bitrate|3.0 Mbps 〜 5.0 Mbps|20.0 Mbps 〜|外出先はアップロード速度の制限を受けやすいため、低めに設定して安定性を確保。|
|Resolution|720p (1280x720)|1080p または Native|スマホ画面なら720pで十分綺麗。文字も読めます。|
|Frame Rate|60 FPS|60 FPS|30FPSだと操作の反応が悪く感じます。|
|Video Codec|HEVC (H.265) オン|どちらでも|HEVCの方が圧縮率が高く、低ビットレートでも画質が良いです（スマホが対応していれば）。|

## 

---

💡 運用のアドバイス（Switch自動化特化）

### 1. モニター電源OFF対策

PCのモニター電源を切るとGPUが映像を作らなくなり、Moonlightが真っ暗になることがあります。

- 解決策: Amazon等で「HDMI ダミープラグ（数百円）」を買い、GPUに挿しておくと、モニターオフでも「画面がある」と騙して配信し続けられます。長時間の自動化放置には必須級アイテムです。
    

### 2. 操作パネル（C#アプリ）の配置

OBSの上に、あなたが作ったC#アプリのウィンドウを「最前面」で配置しておいてください。

Moonlightは「マウス操作モード（画面をなぞってポインタ移動）」と「タッチモード（タップした場所をクリック）」があります。

- ボタンが小さいC#アプリなら 「マウス操作モード」 が誤爆しにくいです。
    
- ボタンが大きい専用UIなら 「タッチモード」 が直感的です。
    

これで手順は完璧です。まずは**ステップ1（ViGEmBusのインストール）**から進めてみてください！

**