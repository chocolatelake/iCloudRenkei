# 開発・ビルド・実行フロー

## 1. コード修正
`MainWindow.axaml.cs` などのソースコードを修正・保存します。

## 2. デバッグ実行
修正内容をすぐに確認する場合に使用します。

```bash
dotnet run
```

## 3. リリースビルド
配布用または本番用の単体実行ファイルを作成します。
（M1/M2/M3 Macなどの Apple Silicon 搭載機向け）

```bash
dotnet publish -c Release -r osx-arm64 --self-contained
```

## 4. リリース実行
ビルドされたアプリケーションをターミナルから直接起動します。

```bash
./bin/Release/net10.0/osx-arm64/publish/SwitchRemoteMac
```