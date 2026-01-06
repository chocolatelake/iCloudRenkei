了解です。先ほどの内容を、そのままファイルとして保存できるMarkdown形式のコードブロックにまとめました。
これをコピーして、`MacServerManual.md` などの名前で保存しておくと便利です。

```markdown
# Mac mini サーバー運用マニュアル

**最終更新日:** 2026/01/04
**サーバー概要:** 電源ONで全サービス自動起動（ヘッドレス運用対応）

---

## 1. 自動起動するサービス一覧

Macにログインすると、以下のサービスがバックグラウンドで静かに起動します。

| サービス名 | 役割 | 状態確認 | ポート |
| :--- | :--- | :--- | :--- |
| **TToDo Bot** | Discord Bot + Webサーバー | Discordで反応があるか | 5000 |
| **ngrok** | 外部公開トンネル | Web管理画面が見れるか | - |
| **Sunshine** | リモートデスクトップ | スマホ(Moonlight)から繋がるか | 47989他 |

### 🌐 アクセス情報
* **Web管理画面:** [https://oxydasic-unransomable-mellie.ngrok-free.dev](https://oxydasic-unransomable-mellie.ngrok-free.dev)
* **Botコマンド:** Discord上で `!ttodo` など

---

## 2. ⚠️ 重要設定（ポート競合対策）

Botは **ポート5000** を使用します。Mac標準機能とケンカするため、以下の設定は **常にOFF** にしてください。

* **設定場所:** システム設定 ＞ 一般 ＞ AirDropとHandoff
* **項目:** **「AirPlayレシーバー」**
* **設定値:** **OFF** (これが入っているとBotが `Address already in use` で死にます)

---

## 3. トラブルシューティング

「Botが返事をしない」「サイトが開かない」ときは、ターミナルでログを確認します。

### 📜 ログの確認コマンド
エラー原因の9割はこれで分かります。

```bash
# Botのエラーログを見る
cat /tmp/ttodo.error.log

# ngrokのエラーログを見る
cat /tmp/ngrok.error.log

```

---

## 4. サービスの再起動・停止

調子が悪い時や、設定を変えた時はコマンドで再起動します。

### 🔄 Bot (TToDo) の再起動

```bash
launchctl unload ~/Library/LaunchAgents/com.ttodo.bot.plist
launchctl load ~/Library/LaunchAgents/com.ttodo.bot.plist

```

### 🔄 ngrok の再起動

```bash
launchctl unload ~/Library/LaunchAgents/com.ttodo.ngrok.plist
launchctl load ~/Library/LaunchAgents/com.ttodo.ngrok.plist

```

---

## 5. Botのアップデート手順

ソースコードを修正した後の反映手順です。

1. **VS Codeで修正・保存**
2. **Publish（最適化ビルド）を実行**
```bash
# Botのフォルダで実行
dotnet publish -c Release -r osx-arm64 --self-contained false

```


3. **Botサービスを再起動**
```bash
launchctl unload ~/Library/LaunchAgents/com.ttodo.bot.plist
launchctl load ~/Library/LaunchAgents/com.ttodo.bot.plist

```


※ 自動起動設定は「Publishされたファイル」を見るようになっているため、ビルドして再起動するだけで中身が入れ替わります。

```

```