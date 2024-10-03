# DependencyTrack-CustomPatch

Dependency Track のカスタム パッチ集です。公式に採用されていないものなので、動作保証はありません（少なくとも、作成者の環境では問題なく動作していますが・・・）。

## apiserver 配下のモノ

### **dependency-track-4.12.0_trivy_rpm+deb.patch**
  - RPM や DEB のパッケージ管理システムにおいて Trivy で作成した SBOM を、Dependency Track に登録し Trivy Analyser を使って分析すると、誤検知が発生します。原因は、Dependency Track に登録したソフトウェアで Epoch が使用されている場合、Dependency Track 内で Trivy API にスキャン リスクエストを生成する際に、問題があるためです（具体的には、バージョンに Epoch が含まれている & リリース番号を送信していないです）。
  - このパッチは、それを解消するものです。ただし、Epoch を使用するパッケージ管理システムは、RPM と DEB のみしか知らないので、これらの場合にのみ適用されるような修正にとどめています。
  - 一応、公式の Isuue (https://github.com/DependencyTrack/dependency-track/issues/3774) に問題の報告とパッチを添付しましたが、1 か月くらい進展がなかったので該当 Issue はクローズし、パッチは公式に採用されていません。
  - 最近、同様と思われる Issue (https://github.com/DependencyTrack/dependency-track/issues/4164) が投稿されているようですw。

## frontend 配下のモノ
### **i18n-ja/***
- v4.11.0 以降、日本語を含む多言語翻訳が公式リリ－スに含まれていますが、一部日本語訳が不自然なため、独自の日本語訳メッセージ ファイルをここで公開しています。

#### 利用方法

1. 利用中の Dependency-Track API サーバーに対応した [Dependency-Track Frontend](https://github.com/DependencyTrack/frontend) のソースコード (*1) をダウンロードします。
2. Dependency-Track Frontend のバージョンと一致するバージョンのソース コード (*2) をこのリポジトリからダウンロードします。
3.  (*1) を解凍したあとに (*2) を上書き展開します。
4.  ターミナルを開き、以下のコマンドを実行します。
```
$ cd /path/to/Dependency-Track-Frontend/
$ npm install
$ npm run build
```
5. ```dist/``` 配下のファイル・ディレクトリ群を Dependency-Track Frontend サーバーのドキュメント ルートにコピーします。
6. あるいは、以下のコマンドで独自に Docker イメージを作成して、作成したイメージをロードするように docker-compose.yml を編集しデプロイする方法でも可。
```
$ docker build -f docker/Dockerfile.alpine -t dependencytrack/frontend:local .
```
7. v4.11.0 未満の場合、Web ブラウザの使用言語の順番で日本語が最優先されるように設定を構成して、Dependency-Track Frontend サーバーにアクセスします。v4.11.0 以降の場合は、ログイン後の右上メニューから日本語を選択します。
