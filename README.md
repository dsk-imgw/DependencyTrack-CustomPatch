# DependencyTrack-CustomPatch

Dependency Track のカスタム パッチ集です。公式に採用されていないものなので、動作保証はありません（少なくとも、作成者の環境では問題なく動作していますが・・・）。

## apiserver 配下のモノ

### **dependency-track-4.12.0_trivy_rpm+deb.patch**
### **dependency-track-4.11.7_trivy_rpm+deb.patch**
- RPM や DEB のパッケージ管理システムにおいて Trivy で作成した SBOM を、Dependency Track に登録し Trivy Analyser を使って分析すると、誤検知が発生します。原因は、Dependency Track に登録したソフトウェアで Epoch が使用されている場合、Dependency Track 内で Trivy API にスキャン リスクエストを生成する際に、問題があるためです（具体的には、バージョンに Epoch が含まれている & リリース番号を送信していないです）。
- このパッチは、それを解消するものです。ただし、Epoch を使用するパッケージ管理システムは、RPM と DEB のみしか知らないので、これらの場合にのみ適用されるような修正にとどめています。
- 一応、公式の Isuue (https://github.com/DependencyTrack/dependency-track/issues/3774) に問題の報告とパッチを添付しましたが、1 か月くらい進展がなかったので該当 Issue はクローズし、パッチは公式に採用されていません。
- 最近、同様と思われる Issue (https://github.com/DependencyTrack/dependency-track/issues/4164) が投稿されているようです。v4.12.1 または v4.13.0 で修正されるようなので、その際は本パッチは用済みになります。

### （AND 条件のポリシー違反検知の改善）
- 現状、なんとなく動くレベルのものを作成。
- 仕様変更は以下のとおり。
  - 【現行】
    - AND 条件（N 個あるとする）でポリシー（名称を P とする）を作成した場合、条件を満たした場合にポリシー P が N 個検知され表示される。
  - 【変更（案）】
    - 条件を満たした場合にポリシー P が 1 個に集約されて検知され、表示される。
- ただし、変更範囲が広域にわたりそうなので、正直実用性は低そう。現状ではパッチ公開はしない予定。
  - ポリシー違反の種類の問題・・・現状、1 条件に対してポリシー違反が検知されるので、ポリシー違反の種類＝条件の種類（Operational/License/Security）となっておりわかりやすいが、異なる種類の条件を集約した場合、どの種類を割り振るかが問題。新たに種類を設けることもできるが、以下の「メトリクス」や「通知」時に問題が起きそう。
  - メトリクスや通知への影響問題・・・件数のカウントやダッシュボード内表示、通知時の個数や内容に影響（エラー発生の可能性）が出そう。

## frontend 配下のモノ
### **i18n-ja/***
- v4.11.0 以降、日本語を含む多言語翻訳が公式リリ－スに含まれていますが、一部日本語訳が不自然なため、独自の日本語訳メッセージ ファイルをここで公開しています。
- v4.11.x については、4.11.4 用のものが流用できます。
- v4.12.x については、気が向いたらリリースします（「英語」表示で特段不自由を感じない & 日本語化の労力に見合わない）

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
