# DependencyTrack-CustomPatch

Dependency Track のカスタム パッチ集です。公式に採用されていないものなので、動作保証はありません（少なくとも、作成者の環境では問題なく動作していますが・・・）。

## apiserver 配下のモノ

- **dependency-track-4.12.0_trivy_rpm+deb.patch**
  - RPM や DEB のパッケージ管理システムにおいて Trivy で作成した SBOM を、Dependency Track に登録し Trivy Analyser を使って分析すると、誤検知が発生します。原因は、Dependency Track に登録したソフトウェアで Epoch が使用されている場合、Dependency Track 内で Trivy API にスキャン リスクエストを生成する際に、問題があるためです（具体的には、バージョンに Epoch が含まれている & リリース番号を送信していないです）。
  - このパッチは、それを解消するものです。ただし、Epoch を使用するパッケージ管理システムは、RPM と DEB のみしか知らないので、これらの場合にのみ適用されるような修正にとどめています。
  - 一応、公式の Isuue (https://github.com/DependencyTrack/dependency-track/issues/3774) に問題の報告とパッチを添付しましたが、1 か月くらい進展がなかったので該当 Issue はクローズし、パッチは公式に採用されていません。
  - 最近、同様と思われる Issue (https://github.com/DependencyTrack/dependency-track/issues/4164) が投稿されているようですw。
