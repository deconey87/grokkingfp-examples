# Dev Container使用ガイド

このリポジトリは、VS Code Dev Containersを使用してScala 3.6.4の完全な開発環境を提供します。

## 前提条件

- **Docker Desktop**がインストールされ、起動していること
- **Visual Studio Code**がインストールされていること
- **Dev Containers拡張機能**がインストールされていること
  - VS Codeで拡張機能ID: `ms-vscode-remote.remote-containers` を検索してインストール

## セットアップ手順

### 初回起動

1. このプロジェクトをVS Codeで開く

2. コマンドパレットを開く（macOS: `Cmd+Shift+P`, Windows/Linux: `Ctrl+Shift+P`）

3. 「**Dev Containers: Reopen in Container**」を選択

4. 初回は以下の処理が行われます（5-10分程度）：
   - Dockerイメージのダウンロード（約1GB）
   - コンテナの起動
   - VS Code拡張機能のインストール（Scala Metals等）
   - `sbt compile`による依存関係のダウンロード

5. ステータスバーに「Dev Container: Grokking Functional Programming - Scala 3」と表示されたら完了

### 2回目以降の起動

- プロジェクトを開くと自動的にコンテナに接続されます
- キャッシュが効いているため、起動は30秒程度に短縮されます

## 使用方法

### 書籍に沿った学習（第1-11章）

1. VS Code内蔵ターミナルを開く（`` Ctrl+` ``）

2. `sbt console`を実行してScala REPLを起動
   ```bash
   sbt console
   ```

3. 書籍のコードスニペットを入力して実行
   ```scala
   scala> val numbers = List(1, 2, 3, 4, 5)
   scala> numbers.map(_ * 2)
   ```

4. 各章の開始時に`:reset`でREPLをリセット
   ```scala
   scala> :reset
   ```

5. 既存のファイルをインポート
   ```scala
   scala> :load src/main/scala/ch01_IntroScala.scala
   ```

### IDE機能の活用

- **IntelliSense**: コード補完と型情報がリアルタイムで表示されます
- **Go to Definition**: `Cmd+クリック`（macOS）で定義元にジャンプ
- **型情報の表示**: カーソルを合わせると型が表示されます
- **エラー表示**: コンパイルエラーが即座にハイライトされます

### テスト駆動学習（第12章）

1. テストファイルを開く: [src/test/scala/ch12_TravelGuideTest.scala](src/test/scala/ch12_TravelGuideTest.scala)

2. 書籍の指示に従って既存のテストを削除（または別の場所に移動）

3. [src/test/scala/ch12_BasicTest.scala](src/test/scala/ch12_BasicTest.scala)で新しいテストを記述

4. ターミナルでテストを実行
   ```bash
   sbt test
   ```

5. 特定のテストのみ実行
   ```bash
   sbt "testOnly ch12_TravelGuideTest"
   ```

### 全サンプルの実行

```bash
sbt runAll
```

### 特定の章を実行

```bash
sbt "runMain ch08_CastingDie"
```

## トラブルシューティング

### Metalsが正しく動作しない場合

1. コマンドパレットで「**Metals: Restart Build Server**」を実行
2. 「**Metals: Import Build**」を実行

### コンテナを再ビルドする場合

1. コマンドパレットで「**Dev Containers: Rebuild Container**」を実行
2. すべてのキャッシュをクリアしたい場合は「**Dev Containers: Rebuild Container Without Cache**」

### メモリ不足エラーが発生する場合

[.devcontainer/devcontainer.json](.devcontainer/devcontainer.json)の`runArgs`セクションでメモリを増やす：

```json
"runArgs": [
  "--memory=8g",  // 4g → 8gに変更
  "--cpus=2"
]
```

### ポート3030が既に使用されている場合

Apache Jena Fuseki（第11-12章）がポート3030を使用します。競合する場合は：

1. [.devcontainer/devcontainer.json](.devcontainer/devcontainer.json)で別のポートに変更
2. または、ホスト側のサービスを停止

## Dev Container環境の詳細

### 含まれるツール

- **Scala**: 3.6.4（書籍で使用されているバージョン）
- **sbt**: 1.10.11（Scala Build Tool）
- **JDK**: Eclipse Temurin 21
- **Scala Metals**: 最新スナップショット（言語サーバー）

### ライブラリ（自動ダウンロード）

[build.sbt](build.sbt)で定義されているライブラリ：
- cats-effect 3.6.0（第8-12章）
- fs2 3.11.0（第9-10章）
- scalatest 3.2.19（第12章）
- Apache Jena 5.3.0（第11-12章のWikidata連携）

### 永続化されるキャッシュ

以下のディレクトリはDockerボリュームとして永続化され、コンテナを再起動してもキャッシュが保持されます：

- `~/.sbt` - sbtグローバル設定
- `~/.ivy2` - Ivy依存関係キャッシュ
- `~/.cache/coursier` - Coursier依存関係キャッシュ

## ローカル環境への影響

Dev Containerを使用することで、以下のメリットがあります：

- ✅ macOSのローカル環境にScala/sbtをインストール不要
- ✅ プロジェクト固有の依存関係がホストOSを汚さない
- ✅ チームメンバーと完全に同一の環境を共有可能
- ✅ 不要になったらコンテナを削除するだけでクリーンアップ完了

## 参考リンク

- [Grokking Functional Programming - 書籍Webページ](https://michalplachta.com/book)
- [VS Code Dev Containers ドキュメント](https://code.visualstudio.com/docs/devcontainers/containers)
- [Scala Metals ドキュメント](https://scalameta.org/metals/docs/editors/vscode/)
- [sbt公式ドキュメント](https://www.scala-sbt.org/1.x/docs/)

## 質問・問題が発生した場合

1. まず[元のREADME.md](README.md)の「What to do when you face problems?」セクションを確認
2. [書籍Webページ](https://michalplachta.com/book)で最新情報を確認
3. Dev Container固有の問題は[VS Code Dev Containersトラブルシューティング](https://code.visualstudio.com/docs/devcontainers/troubleshooting)を参照

楽しい関数型プログラミング学習を！
