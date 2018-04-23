---
title: Azure Machine Learning の新機能
description: このドキュメントでは、Azure Machine Learning の更新について詳しく説明します。
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.service: machine-learning
ms.workload: data-services
ms.topic: reference
ms.date: 03/28/2018
ms.openlocfilehash: e30943426ad68171e1464f828a9c8672b06c975a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="whats-new-in-azure-machine-learning"></a>Azure Machine Learning の新機能

この記事では、[Azure Machine Learning Services](../service/overview-what-is-azure-ml.md) の新しいリリースについて説明します。 

## <a name="2018-03-sprint-4"></a>2018-03 (スプリント 4)
**バージョン番号**: 0.1.1801.24353  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([バージョンの検索](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))


Azure Machine Learning Workbench の 5 回目の更新へようこそ。 以下の更新の多くは、お客様のフィードバックの直接の結果として行われました。 引き続きフィードバックをお寄せください。

**注目すべき新機能と変更**

- リモート Docker ベースでの実行に加えて、リモート Ubuntu VM 上にあるスクリプトを独自の環境でネイティブに実行できるようになりました。
- CLI ベースのエクスペリエンスに加えて用意された Workbench アプリ内での新しい環境エクスペリエンスにより、コンピューティング ターゲットを作成し、構成を実行できます。
![環境タブ](media/azure-machine-learning-release-notes/environment-page.png)
- カスタマイズ可能な実行履歴レポート ![新しい実行履歴レポートの画像](media/azure-machine-learning-release-notes/new-run-history-reports.png)

**詳細な更新**

以下は、このスプリントでの Azure Machine Learning の各コンポーネント領域内の詳細な更新の一覧です。

### <a name="workbench-ui"></a>Workbench UI
- カスタマイズ可能な実行履歴レポート
  - 実行履歴レポート用のグラフ構成の改良
    - 使用されたエントリポイントを変更可能
    - 最上位レベルのフィルターを追加および変更可能 ![フィルターの追加](media/azure-machine-learning-release-notes/add-filters.jpg)
    - グラフと統計情報を追加および変更可能 (ドラッグ アンド ドロップで再配置)
    ![新しいグラフの作成](media/azure-machine-learning-release-notes/configure-charts.png)

  - 実行履歴レポートの CRUD
  - 既存の実行履歴リスト ビューの構成をすべてサーバー側のレポートに移動しました。これは、選択したエントリ ポイントからの実行でパイプラインのように機能します。

- 環境タブ
  - 新しいコンピューティング ターゲットを簡単に追加し、構成ファイルをプロジェクトに対して実行 ![新しいコンピューティング ターゲット](media/azure-machine-learning-release-notes/add-new-environments.png)
  - 単純なフォームベースの UX を使用して、構成ファイルを管理、更新
  - 実行環境を準備するための新しいボタン

- サイド バー内のファイルの一覧のパフォーマンスの向上

### <a name="data-preparation"></a>データの準備 
- Azure Machine Learning Workbench を使用して、既知の列名で列を検索できるようになりました。


### <a name="experimentation"></a>実験
- Azure Machine Learning Workbench で、独自の Python または PySpark 環境でのネイティブなスクリプト実行がサポートされました。 この機能によって、ユーザーは独自の環境をリモート VM 上で作成、管理できるようになります。また、Azure Machine Learning Workbench を使用してターゲットに対してスクリプトを実行できます。 「[Azure Machine Learning 実験サービスの構成](experimentation-service-configuration.md)」を参照してください。 

### <a name="model-management"></a>モデル管理
- デプロイされたコンテナーのカスタマイズのサポート: apt-get などを使用して外部ライブラリのインストールを許可することで、コンテナー イメージをカスタマイズできます。pip によるインストールが可能なライブラリに限らず実施できるようになりました。 詳細については、この[ドキュメント](model-management-custom-container.md)をご覧ください。
  - マニフェスト、画像、サービスの作成コマンドで `--docker-file myDockerStepsFilename` フラグとファイル名を使用します。
  - 基本イメージが Ubuntu であり、変更できない場合があることに注意してください。
  - コマンドの例: 
  
      ```shell
      $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
      ```



## <a name="2018-01-sprint-3"></a>2018-01 (スプリント 3) 
**バージョン番号**: 0.1.1712.18263  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([バージョンの検索](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

このスプリントでの更新と機能強化は次のとおりです。 これらの更新の多くは、ユーザー フィードバックの直接の結果として行われました。 


以下は、このスプリントでの Azure Machine Learning の各コンポーネント領域内の詳細な更新の一覧です。

- 認証スタックへの更新がスタートアップ時のログインとアカウント選択に強制されます。

### <a name="workbench"></a>ワークベンチ
- [プログラムの追加と削除] からアプリをインストール/アンインストールできるようになりました。
- 認証スタックへの更新がスタートアップ時のログインとアカウント選択に強制されます。
- Windows のシングル サインオン (SSO) エクスペリエンスが改善されました。
- 異なる資格情報の複数のテナントに属しているユーザーが Workbench にサインインできるようになります。

### <a name="ui"></a>UI
- 全般的な機能強化とバグの修正

### <a name="notebooks"></a>Notebook
- 全般的な機能強化とバグの修正

### <a name="data-preparation"></a>データの準備 
- [例による] 変換実行中の自動提案が機能強化されました。
- パターン頻度インスペクターのアルゴリズムが強化されました。
- [例による] 変換実行中にサンプル データとフィードバックを送信できるようになりました。![派生列変換のフィードバック送信リンクのイメージ](media/azure-machine-learning-release-notes/SendFeedbackFromDeriveColumn.png)
- Spark ランタイムが機能強化されました。
- Pyspark が Scala に置き換えられました。
- 時系列インスペクターの適用外データを終了できない問題が修正されました。 
- HDI のデータ準備を実行したときに反応しなくなる問題を修正されました。

### <a name="model-management-cli-updates"></a>モデル管理 CLI の更新 
  - リソースのプロビジョニングでサブスクリプションの所有権が不要になりました。 デプロイ環境の設定は、リソース グループへの共同作成者のアクセスで十分になります。
  - Free サブスクリプションでローカル環境のセットアップが有効になりました。 

## <a name="2017-12-sprint-2-qfe"></a>2017-12 (スプリント 2 QFE) 
**バージョン番号**:  0.1.1711.15323  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([バージョンの検索](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

これは、マイナー リリースである QFE (Quick Fix Engineering) リリースです。 テレメトリに関するいくつかの問題に対処しているため、製品チームは製品の使用状況をより詳しく把握できます。 そのナレッジは、製品のエクスペリエンスを向上させる取り組みにおいて将来的に役立てられます。 

また、重要な更新プログラムが 2 つあります。

- データ準備パッケージで、時系列インスペクターが表示されないというデータ準備上のバグを修正しました。
- コマンドライン ツールで、Machine Learning の ACS コンピューティング クラスターをプロビジョニングするために、Azure サブスクリプションの所有者である必要がなくなりました。 

## <a name="2017-12-sprint-2"></a>2017-12 (スプリント 2)
**バージョン番号**: 0.1.1711.15263  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([バージョンの検索](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Azure Machine Learning の 3 回目の更新へようこそ。 この更新には、Workbench アプリ、コマンド ライン インターフェイス (CLI)、およびバックエンド サービスの改良が含まれます。 当社に長所と欠点を送信していただき誠にありがとうございます。 以下の更新の多くは、お客様のフィードバックの直接の結果として行われました。 

**注目すべき新機能**
- [データ ソースとしての SQL Server および Azure SQL DB のサポート](data-prep-appendix2-supported-data-sources.md#types) 
- [MMLSpark を使用した GPU サポートのある Spark でのディープ ラーニング](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [デプロイの時点で Azure IoT Edge デバイスと互換性のあるすべての AML コンテナー (余分な手順は不要)](http://aka.ms/aml-iot-edge-blog)
- Azure Portal で利用可能な登録済みモデル リストと詳細ビュー
- ユーザー名/パスワード ベースのアクセスに加えて、SSH キーに基づく認証を使用したコンピューティング ターゲットへのアクセス。 
- データ準備エクスペリエンスでの新しいパターン頻度インスペクター。 

**更新の詳細** 以下は、このスプリントでの Azure Machine Learning の各コンポーネント領域内の詳細な更新の一覧です。

### <a name="installer"></a>インストーラー
- インストーラーは自動的に更新するので、ユーザーが再インストールしなくてもバグ修正と新機能をサポートできます

### <a name="workbench-authentication"></a>Workbench の認証
- 認証システムに関して複数の修正が行われました。 ログインでまだ問題が発生する場合はお知らせください。
- UI が変更されて、Proxy Manager の設定を見つけやすくなりました。

### <a name="workbench"></a>ワークベンチ
- 読み取り専用のファイル ビューの背景が明るい青色になりました
- [編集] ボタンが右側に移動されてわかりやすくなりました。
- "dsource"、"dprep"、"ipynb" のファイル形式を、生テキスト形式でレンダリングできるようになりました
- Workbench の編集エクスペリエンスが新しくなり、スクリプトの編集には外部の IDE を使い、Workbench はリッチな編集エクスペリエンスを持つファイル形式 (Notebook、データ ソース、データ準備パッケージなど) の編集だけに使うように案内されるようになりました
- ユーザーがアクセス権を持つワークスペースとプロジェクトの一覧の読み込みが、大幅に高速化されました

### <a name="data-preparation"></a>データの準備 
- パターン頻度インスペクターに、列の文字列パターンが表示されます。 これらのパターンを使って、データをフィルター処理することもできます。 値のカウント インスペクターと似たビューが表示されます。 違いは、パターン頻度では、一意のデータの数ではなく、データの一意のパターンの数が示されることです。 また、特定のパターンに一致するすべての行を含めたり、除外したりすることもできます。

![製品数でのパターン頻度インスペクターの画像](media/azure-machine-learning-release-notes/pattern-inspector-product-number.png)

- "例による列の派生" 変換でのレビューのためのエッジ ケース推奨の間のパフォーマンスの向上

- [データ ソースとしての SQL Server および Azure SQL DB のサポート](data-prep-appendix2-supported-data-sources.md#types) 

![新しい SQL Server データ ソースの作成の画像](media/azure-machine-learning-release-notes/sql-server-data-source.png)

- 行と列の数の "概略" ビューが有効になりました

![行と列の数の概略の画像](media/azure-machine-learning-release-notes/row-col-count.png)

- データの準備がすべてのコンピューティング コンテキストで有効になりました
- SQL Server データベースを使うデータ ソースが、すべてのコンピューティング コンテキストで 有効になりました
- データ準備のグリッド列をデータ型でフィルター処理できます
- 複数の列を日付に変換するときの問題を修正しました
- ユーザーが詳細モードで出力列の名前を変更した場合、ユーザーが例による列の派生でソースとして出力列を選択できた問題を修正しました。

### <a name="job-execution"></a>ジョブの実行
次の手順により、SSH キー ベースの認証を使って remotedocker 型またはクラスター型のコンピューティング ターゲットを作成してアクセスできるようになりました。
- CLI で次のコマンドを使ってコンピューティング ターゲットをアタッチします

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
>[!NOTE]
>コマンドの -k (または --use-azureml-ssh-key) オプションは、SSH キーを生成して使うことを指定します。

- Azure ML Workbench が公開キーを生成してコンソールに出力します。 同じユーザー名を使ってコンピューティング ターゲットにログインし、この公開キーで ~/.ssh/authorized_keys ファイルを追加します。

- このコンピューティング ターゲットを準備して実行に使うことができ、Azure ML Workbench は認証にこのキーを使います。  

コンピューティング ターゲットの作成について詳しくは、「[Azure Machine Learning 実験サービスの構成](experimentation-service-configuration.md)」をご覧ください。

### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools for AI
- [Visual Studio Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017) のサポートが追加されました。 

### <a name="command-line-interface-cli"></a>コマンド ライン インターフェイス (CLI)
- 追加された `az ml datasource create` コマンドを使って、コマンド ラインからデータ ソース ファイルを作成できます

### <a name="model-management-and-operationalization"></a>モデルの管理と運用化
- [すべての AML コンテナーは、運用化の時点で Azure IoT Edge デバイスと互換性があります (余分な手順は不要)](http://aka.ms/aml-iot-edge-blog) 
- O16n CLI でエラー メッセージの改良
- モデル管理ポータル UX のバグ修正  
- 詳細ページのモデル管理属性での一貫性のある大文字と小文字の使い分け
- 60 秒に設定されたリアルタイム スコアリング呼び出しのタイムアウト
- Azure Portal で利用可能な登録済みモデル リストと詳細ビュー

![ポータルでのモデルの詳細](media/azure-machine-learning-release-notes/model-list.jpg)

![ポータルでのモデルの概要](media/azure-machine-learning-release-notes/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- [GPU がサポート](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)された Spark でのディープ ラーニング
- 容易なリソース デプロイのための Resource Manager テンプレートのサポート
- SparklyR エコシステムのサポート
- [AZTK の統合](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>サンプル プロジェクト
- [Iris](https://github.com/Azure/MachineLearningSamples-Iris) および [MMLSpark](https://github.com/Azure/mmlspark) サンプルが、新しい Azure ML SDK バージョンで更新されました

### <a name="breaking-changes"></a>重大な変更
- `az ml computetarget attach` の `--type` スイッチがサブコマンドに引き上げられました。 

    - `az ml computetarget attach --type remotedocker` は `az ml computetarget attach remotedocker` になりました
    - `az ml computetarget attach --type cluster` は `az ml computetarget attach cluster` になりました

## <a name="2017-11-sprint-1"></a>2017-11 (スプリント 1) 
**バージョン番号**: 0.1.1710.31013  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([バージョンの検索](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

このリリースで当社は、Workbench アプリ、CLI、およびバックエンド サービス レイヤーにおけるセキュリティ、安定性、保守容易性に関して機能強化を行っています。 当社に長所と欠点を送信していただき誠にありがとうございます。 以下の更新の多くは、お客様のフィードバックの直接の結果として行われました。 引き続きフィードバックをお寄せください。

### <a name="notable-new-features"></a>注目すべき新機能
- Azure ML は現在、**西ヨーロッパ**と**東南アジア**の 2 つの新しい Azure リージョンで使用できます。 これらは**米国東部 2**、**米国西中部**、および**オーストラリア東部**の以前のリージョンに追加され、デプロイされたリージョンの総数は 5 になります。
- Python ソース コードの読み取りおよび編集を簡単にするために、Workbench アプリでの Python コード構文の強調表示を有効にしました。 
- お気に入りの IDE をプロジェクト全体からではなく、ファイルから直接起動できるようになりました。  Workbench でファイルを開いてから [編集] をクリックすると、現在のファイルとプロジェクトに IDE (現在 VS Code と PyCharm がサポートされています) が起動されます。  [編集] ボタンの横にある矢印をクリックして、Workbench テキスト エディターでファイルを編集することもできます。  [編集] をクリックするまでファイルは読み取り専用であるため、不注意による変更が防止されます。
- Workbench アプリには、人気のあるプロット ライブラリ `matplotlib` バージョン 2.1.0 が付属するようになりました。
- データ準備エンジン用に、.NET Core バージョンを 2.0 にアップグレードしました。 これにより、macOS でのアプリ インストール中の brew-install openssl の要件はなくなりました。 また、近い将来、より魅力的なデータ準備機能が提供される道も開かれます。 
- バージョン固有のアプリ ホームページを有効にしたため、現在のアプリ バージョンに基づいて、より関連したリリース ノートおよび更新プロンプトが取得されます。
- ローカル ユーザー名にスペースが含まれている場合でも、アプリケーションを正常にインストールできるようになりました。 

### <a name="detailed-updates"></a>詳細な更新
以下は、このスプリントでの Azure Machine Learning の各コンポーネント領域内の詳細な更新の一覧です。

#### <a name="installer"></a>インストーラー
- アプリ インストーラーは現在、古いバージョンのアプリによって作成されたインストール ディレクトリをクリーンアップします。
- macOS High Sierra で 100% でインストーラーがスタックするバグを修正しました。
- 現在、インストールが失敗した場合、ユーザーがインストーラーのログを確認するためのインストーラー ディレクトリへの直接リンクが存在します。
- ユーザー名にスペースが含まれるユーザーでもインストールが機能するようになりました。

#### <a name="workbench-authentication"></a>Workbench の認証
- Proxy Manager での認証のサポート。
- ユーザーがファイアウォールの背後にいる場合でもログインが成功するようになりました。 
- ユーザーが複数の Azure リージョン内に実験アカウントを保有している場合は、あるリージョンが使用できなくなっても、アプリはハングアップしなくなりました。
- 認証がまだ完了せず、認証ダイアログ ボックスがまだ表示されている場合、アプリはローカル キャッシュからワークスペースを読み込もうとはしなくなりました。

#### <a name="workbench-app"></a>Workbench アプリ
- テキスト エディターで Python コード構文の強調表示が有効になっています。
- テキスト エディターの [編集] ボタンを使用すると、IDE (VS Code と PyCharm がサポートされています) または組み込みのテキスト エディターのどちらでもファイルを編集できます。
- テキスト エディターは、既定では読み取り専用モードになっています。 
- 現在のファイルが保存された後、[保存] ボタンの表示状態は無効に変更されるようになったため、ダーティではなくなりました。
- Workbench は、ユーザーが実行を開始したとき、保存されていない_すべての_ファイルを保存します。
- Workbench はローカル コンピューター上の最後に使用されたワークスペースを記憶しているため、それが自動的に開きます。
- Workbench の 1 つのインスタンスだけが実行を許可されるようになりました。 以前は、複数のインスタンスを起動できたため、同じプロジェクトで動作しているときに問題が発生しました。
- [ファイル] メニューの [Open Project...] \(プロジェクトを開く...) は [Add Existing Folder as Project...] \(既存のフォルダーをプロジェクトとして追加する...) に名前が変更されました。 
- タブの切り替えがきわめて迅速になりました。
- [Configuring IDE]\ (IDE の構成) ダイアログ ボックスにヘルプ リンクが追加されました。
- フィードバック フォームが最後に入力された電子メール アドレスを記憶するようになりました。
- 長所と欠点のフォームはテキスト領域が大きくなったため、より多くのフィードバックを送信できます。 
- `az ml workspace create` 内の `--owner` スイッチのヘルプ テキストが修正されました。
- ユーザーがアプリのバージョン番号を簡単に表示したりコピーしたりできるようにするために、[バージョン情報] ダイアログ ボックスを追加しました。
- [ヘルプ] メニューに [Suggest a feature] (機能の提案) メニュー項目が追加されました。
- 実験アカウント名が、アプリのタイトル バー内のアプリ名 "Azure Machine Learning Workbench" の前に表示されるようになりました。
- 検出されたアプリのバージョンに基づいて、バージョン固有のアプリ ホームページが表示されるようになりました。

#### <a name="data-preparation"></a>データの準備 
- 潜在的なセキュリティの問題を防止するために、Map Inspector から外部の Web サイトを読み込めなくなりました。
- [ヒストグラム] および [Value Count] (値のカウント) インスペクターに、グラフを対数目盛で表示するオプションが追加されました。
- 計算が進行中のときは、データ品質バーに "計算" の状態を知らせるための別の色が表示されるようになりました。
- 列メトリックがカテゴリ値列の統計を示すようになりました。
- データ ソース名の最後の文字が切り捨てられなくなりました。
- タブの切り替え時にデータ準備パッケージが開かれたままになったため、パフォーマンスが大幅に向上しました。
- データ ソースで、データ ビューとメトリック ビューの切り替え時に列の順序が変更されなくなりました。
- 無効な `.dprep` または `.dsource` ファイルを開いても、Workbench がクラッシュしなくなりました。
- データ準備パッケージが _[CSV への書き込み]_ 変換で出力に相対パスを使用できるようになりました。
- _[列の保持]_ 変換で、ユーザーは編集時に列を追加できるようになりました。
- _[こちらに置き換えてください]_ メニューが、実際には _[Replace Value] (値の置き換え)_ ダイアログ ボックスを起動するようになりました。
- _[Replace Value] (値の置き換え)_ 変換が、エラーをスローせず期待どおりに機能するようになりました。
- データ準備パッケージは、プロジェクト フォルダーの外部にあるデータ ファイルの参照時に絶対パスを使用するようになったため、データ ファイルへの絶対パスを使用してパッケージをローカル コンテキストで実行することが可能になりました。
- Azure BLOB をデータ ソースとして使用するとき、サンプリング方式としての _[ファイル全体]_ がサポートされるようになりました。
- (データ準備パッケージから) 生成された Python コードは CR と LF の両方を含むようになったため、Windows で扱いやすくなりました。
- データ ビューに切り替えたとき、_[メトリックの選択]_ ドロップダウンにプロパティが表示されなくなりました。
- Python ランタイムを使用している場合でも、Workbench が parquet ファイルを処理できるようになりました。 以前は、parquet ファイルを処理するときは Spark しか使用できませんでした。 
- _日付_データ型を含む列内の値をフィルターで除外しても、データ準備エンジンがクラッシュしなくなりました。
- メトリック ビューがサンプリング方式の更新に従うようになりました。
- リモート サンプリング ジョブが正しく機能するようになりました。

#### <a name="job-execution"></a>ジョブの実行
- 実行履歴レコードに引数が含まれるようになりました。
- CLI で開始されたジョブが [実行履歴] ジョブ パネルに自動的に表示されるようになりました。
- ジョブ パネルに、Azure AD テナントに追加されたゲスト ユーザーによって作成されたジョブが表示されるようになりました。
- ジョブ パネルの取り消しおよび削除アクションの安定性が向上しました。
- [実行] ボタンをクリックしたとき、構成ファイルの形式が正しくない場合はエラー メッセージがトリガーされるようになりました。
- アプリを終了しても、CLI で開始されたジョブに悪影響を与えることがなくなりました。
- CLI で開始されたジョブが、実行後 1 時間経過しても、標準出力を引き続き出力するようになりました。
- Python/PySpark でデータ準備パッケージの実行が失敗したとき、より適切なエラー メッセージが表示されます。
- `az ml experiment clean` がリモート VM でも Docker イメージをクリーンアップするようになりました。
- `az ml experiment clean` が macOS 上のローカル ターゲットに対して正しく機能するようになりました。
- ローカルまたはリモート Docker 実行を対象とするエラー メッセージがクリーンアップされ、読みやすくなりました。
- 実行ターゲットとしての接続時に HDInsight クラスター ヘッド ノード名が正しく書式設定されていないとき、より適切なエラー メッセージが表示されます。
- 資格情報サービスでシークレットが見つからないとき、より適切なエラー メッセージが表示されます。 
- MMLSpark ライブラリが Apache Spark 2.2 をサポートするようにアップグレードされました。
- MMLSpark に、医療ドキュメントのための件名エンコーディング変換 (メッシュ エンコーディング) が含まれるようになりました。
- `matplotlib` バージョン 2.1.0 が Workbench に標準で付属するようになりました。

#### <a name="jupyter-notebook"></a>Jupyter Notebook
- Notebook 名の検索が [Notebook] ビューで正しく機能するようになりました。
- [Notebook] ビューで Notebook を削除できるようになりました。
- Notebook 実行環境で生成されたファイルを実行履歴データ ストアにアップロードするために、新しいマジック `%upload_artifact` が追加されました。
- デバッグを容易にするために、Notebook ジョブの状態にカーネル エラーが表示されるようになりました。
- ユーザーがアプリからログアウトしたとき、Jupyter サーバーが正しくシャットダウンするようになりました。

#### <a name="azure-portal"></a>Azure ポータル
- 西ヨーロッパと東南アジアの 2 つの新しい Azure リージョンで実験アカウントとモデル管理アカウントを作成できるようになりました。
- モデル管理アカウントの DevTest プランは、それがこのサブスクリプションで作成される最初のものである場合にのみ使用可能になりました。 
- Azure Portal のヘルプ リンクが正しいドキュメント ページを指すように更新されました。
- [説明] フィールドは、適用できないために Docker イメージの詳細ページから削除されました。
- AppInsights や自動スケール設定を含む詳細が Web サービスの詳細ページに追加されました。
- ブラウザーでサード パーティ Cookie が無効になっている場合でも、[モデル管理] ページが表示されるようになりました。 

#### <a name="operationalization"></a>運用化
- 名前に "score" が含まれる Web サービスが失敗しなくなりました。
- ユーザーは、Azure リソース グループまたはサブスクリプションに対する [共同作成者] アクセス権のみを含むデプロイメント環境を作成できるようになりました。 サブスクリプション全体に対する [所有者] アクセス権は必要なくなりました。
- 運用化 CLI が Linux でタブのオートコンプリートを利用するようになりました。
- イメージ構築サービスが Azure IoT サービス/デバイスのためのイメージの構築をサポートするようになりました。

#### <a name="sample-projects"></a>サンプル プロジェクト
- ["_[あやめの分類]_"](./tutorial-classifying-iris-part-1.md) サンプル プロジェクト:
    - `iris_pyspark.py` は `iris_spark.py` に変更されました。
    - `iris_score.py` は `score_iris.py` に変更されました。
    - `iris.dprep` と `iris.dsource` がデータ準備エンジンの最新の更新を反映するように更新されました。
    - `iris.ipynb` Notebook が HDInsight クラスターで機能するように修正されました。
    - `iris.ipynb` Notebook セルで実行履歴が有効になりました。
- ["_自転車シェア データを使用した詳細データの準備_"](./tutorial-bikeshare-dataprep.md) のサンプル プロジェクトのエラー値の処理の手順が修正されました。
- ["_成人の国勢調査データでの MMLSpark_"](https://github.com/Azure/MachineLearningSamples-mmlspark) のサンプル プロジェクトの `docker.runconfig` 形式が JSON から YAML に更新されました。
- ["_ハイパーパラメーターの分散チューニング_"](./scenario-distributed-tuning-of-hyperparameters.md) のサンプル プロジェクトの `docker.runconfig` 形式が JSON から YAML に更新されました。
- 新しいサンプル プロジェクト ["_CNTK を使用した画像分類_"](./scenario-image-classification-using-cntk.md)。


## <a name="2017-10-sprint-0"></a>2017-10 (スプリント 0) 
**バージョン番号**: 0.1.1710.31013  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([バージョンの検索](../service/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Microsoft Ignite 2017 カンファレンスでの最初のパブリック プレビューに続いて、Azure Machine Learning Workbench の初回の更新が実施されています。 今回のリリースでの主要な更新プログラムは、信頼性と安定化の修正です。  対処した重大な問題には、以下が含まれます。

### <a name="new-features"></a>新機能
- macOS High Sierra がサポートされるようになりました。

### <a name="bug-fixes"></a>バグの修正
#### <a name="workbench-experience"></a>Workbench のエクスペリエンス
- Workbench にファイルをドラッグ アンド ドロップすると、Workbench がクラッシュします。
- Workbench の IDE として構成された VS Code 内のターミナル ウィンドウでは、_az ml_ コマンドは認識されません。

#### <a name="workbench-authentication"></a>Workbench の認証
報告されたさまざまなログインと認証に関する問題を改善するために、多数の更新を実施しました。
- 認証ウィンドウはポップ アップ状態を保ちます (特にインターネット接続が安定しない場合)。
- 認証トークンの有効期限に関する信頼性の問題を改善しました。
- 場合によっては、認証ウィンドウが 2 回表示されます。
- 認証プロセスが終了しており、ポップアップ ダイアログ ボックスが既に閉じている場合でも、Workbench のメイン ウィンドウに [認証中] メッセージが引き続き表示されます。
- インターネット接続がない場合、認証ダイアログは空白の画面でポップアップ表示されます。

#### <a name="data-preparation"></a>データの準備 
- 特定の値をフィルター処理すると、エラーと欠落値も除外されます。
- サンプリング方法を変更すると、後続の既存の結合操作が削除されます。
- 欠落値を置き換える変換では、NaN は考慮されません。
- 日付型の推定では、null 値が検出されたときに例外をスローします。

#### <a name="job-execution"></a>ジョブの実行
- ジョブの実行でプロジェクト フォルダーがサイズの制限を超えたためにアップロードできない場合、明確なエラー メッセージはありません。
- ユーザーの Python スクリプトで作業ディレクトリが変更された場合、出力フォルダーに書き込まれるファイルは追跡されません。 
- アクティブな Azure サブスクリプションが現在のプロジェクトが属しているものと異なる場合、ジョブの送信によって 403 エラーが発生します。
- Docker が存在しないときに実行対象として Docker を使用しようとした場合、明確なエラー メッセージは返されません。
- _実行_ボタンをクリックしても、.runconfig ファイルは自動的に保存されません。

#### <a name="jupyter-notebook"></a>Jupyter Notebook
- ユーザーが特定のログインの種類を使用した場合、Notebook サーバーを開始できません。
- Notebook サーバーのエラー メッセージは、ユーザーが表示できるログには記録されません。

#### <a name="azure-portal"></a>Azure ポータル
- Azure ポータルのダーク テーマを選択すると、[モデル管理] ブレードがブラック ボックスで表示されます。

#### <a name="operationalization"></a>運用化
- Web サービスを更新するためにマニフェストを再利用すると、新しい Docker イメージがランダムな名前で作成されます。
- Kubernetes クラスターから Web サービスのログを取得することはできません。
- モデル管理アカウントまたは ML コンピューティング アカウントを作成しようとすると、誤解を招くエラー メッセージが出力され、アクセス許可問題が発生します。

## <a name="next-steps"></a>次の手順

[Azure Machine Learning](../service/overview-what-is-azure-ml.md) の概要をご覧ください。