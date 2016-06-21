<properties
	pageTitle="Azure Machine Learning FAQ | Microsoft Azure"
	description="Azure Machine Learning の概要: 効率的な予測モデリングのためのクラウド サービスについて、その課金、機能、制限についての疑問点に答える FAQ です。"
	keywords="機械の概要、予測モデリング、機械学習とは"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/13/2016"
	ms.author="garye"/>

# Azure Machine Learning に関してよく寄せられる質問 (FAQ): 課金、機能、制限、サポート

この FAQ では、Web サービスを使用して予測モデルの作成とソリューションの運用を可能にするクラウド サービスである Azure Machine Learning についての疑問に答えます。サービスの課金モデル、機能、制限、サポートなど、その使用に関して多く寄せられる質問を取り上げています。

## 一般的な質問

**Azure Machine Learning とは**

Azure Machine Learning は、クラウドで予測分析ソリューションを作成、テスト、操作、管理するための、十分に管理されたサービスです。ブラウザーさえあれば、サインアップ、データのアップロード、機械学習の実験をすぐに始めることができます。ドラッグ アンド ドロップによる予測モデリング、モジュールの大きなパレット、開始用テンプレートのライブラリによって、一般的な機械学習のタスクがシンプルになり、作業時間が短縮されます。詳細については、「[Azure Machine Learning サービスの概要](https://azure.microsoft.com/services/machine-learning/)」に関するページをご覧ください。機械学習の重要な用語や概念など基本事項については、[Azure Machine Learning の概要](machine-learning-what-is-machine-learning.md)に関するページを参照してください。


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Machine Learning Studio とは**

Machine Learning Studio は、Web ブラウザーからアクセスできるワークベンチ環境です。Machine Learning Studio は、視覚的な構成のインターフェイスを持つモジュールのパレットをホストし、実験の形式でエンド ツー エンドのデータ技術ワークフローを容易に構築できます。

Machine Learning Studio の詳細については、「[Machine Learning Studio とは](machine-learning-what-is-ml-studio.md)」をご覧ください。

**Machine Learning API サービスとは**

Machine Learning API サービスでは、Machine Learning Studio に組み込まれた予測モデルなどを、拡張性の高い、フォールト トレランスに優れた Web サービスとしてデプロイできます。Machine Learning API サービスによって作成された Web サービスが REST API です。REST API は、外部アプリケーションと予測分析モデルの間の通信インターフェイスを提供します。

詳細については、「[Machine Learning Web サービスへの接続](machine-learning-connect-to-azure-machine-learning-web-service.md)」をご覧ください。


## 課金に関する質問

**Machine Learning ではどのように請求が行われますか。**

課金と料金の詳細については、「[Machine Learning 料金](https://azure.microsoft.com/pricing/details/machine-learning/)」をご覧ください。

**Azure Machine Learning に無料試用版はありますか。**

 Azure Machine Learning には、無料サブスクリプションのオプション (詳細については、「[Machine Learning の価格](https://azure.microsoft.com/pricing/details/machine-learning/)」を参照) が用意されており、Machine Learning Studio では、8 時間の簡易評価用の試用版が利用できます (この試用版を利用するには、[Machine Learning Studio](https://studio.azureml.net/?selectAccess=true&o=2) にログインしてください)。
 
 さらに、Azure の無料試用版にサインアップすると、1 か月間すべての Azure サービスを試すことができます。Azure 無料試用版の詳細については、「[Azure 無料試用版の FAQ](/pricing/free-trial-faq/)」を参照してください。

## Machine Learning Studio に関する質問

### 実験の作成

**実験グラフ用のバージョン管理や Git 統合はありますか。**

いいえ。ただし、Machine Learning Studio は、他のユーザーによって変更できない実験の各イテレーションを保持します。詳細については、「[Machine Learning Studio での実験イテレーションの管理](machine-learning-manage-experiment-iterations.md)」を参照してください。

### Machine Learning 用データのインポートとエクスポート

**Machine Learning はどのようなデータ ソースをサポートしていますか。**

Machine Learning Studio の実験にデータを読み込むには、3 種類の方法があります。データセットとしてのローカル ファイルのアップロード、モジュールを使用したクラウド データ サービスからのデータのインポート、または別の実験から保存されたデータセットのインポートです。サポートされているファイル形式の詳細については、「[Import training data into Machine Learning Studio (Machine Learning Studio へのトレーニング データのインポート)](machine-learning-data-science-import-data.md)」をご覧ください。


#### <a id="ModuleLimit"></a>モジュールのデータ セットの大きさはどの程度まで許容されますか。

Machine Learning Studio のモジュールは、一般的に、最大 10 GB の高密度数値データのデータセットをサポートしています。モジュールが 1 つ以上の入力を受け取る場合は、10 GB はすべての入力サイズの合計です。インジェストの前に、Hive または Azure SQL Database のクエリ経由で、またはカウントによる学習の前処理によって、より大きなデータセットをサンプリングすることもできます。

次の種類のデータは、特徴の正規化の際により大きなデータセットに展開でき、10 GB 未満に制限されています。

- スパース
- カテゴリ
- 文字列
- バイナリ データ

次のモジュールは、10 GB 未満のデータセットに制限されています。

- 推奨モジュール
- SMOTE モジュール
- スクリプト モジュール: R、Python、SQL
- 出力データ サイズが入力データ サイズを超えるモジュール ("結合"、"特徴ハッシュ" など)。
- イテレーションの数が非常に大きい場合のクロス検証、調整モデル ハイパーパラメーター、順序回帰、一対全多クラス。

数 GB を超えるデータセットの場合は、ローカル ファイルから直接アップロードするのではなく、Azure Storage または Azure SQL Database にデータをアップロードするか、HDInsight を使用する必要があります。


####<a id="UploadLimit"></a>データのアップロードの制限は何ですか。
数 GB を超えるデータセットの場合は、ローカル ファイルから直接アップロードするのではなく、Azure Storage または Azure SQL Database にデータをアップロードするか、HDInsight を使用する必要があります。

**Amazon S3 からデータを読み取ることはできますか。**

少量のデータがあり、http URL 経由でこれを公開する場合は、[データのインポート][import-data] モジュールを使用できます。大量のデータの場合は、まず Azure Storage に転送してから、[データのインポート][import-data] モジュールを使用して実験に取り込みます。
<!--
<SEE CLOUD DS PROCESS>
-->

**組み込みイメージの入力機能はありますか。**

イメージの入力機能については、「[Import Images (イメージのインポート)][image-reader]」を参照してください。

### モジュール

**探しているアルゴリズム、データ ソース、データ形式、データ変換操作が Azure Machine Learning Studio で見つかりません。どうすればいいでしょうか?**

[ユーザー フィードバック フォーラム](http://go.microsoft.com/fwlink/?LinkId=404231)にアクセスすると、機能に関する追跡中の要望をご覧いただけます。探している機能が既に要求されている場合は、その要求に投票を追加できます。探している機能が存在しない場合は、新しい要求を作成します。このフォーラムでの要求の状態も参照できます。この一覧はこまめに追跡され、機能の提供状況は頻繁に更新されます。さらに、R と Python の組み込みのサポートを使用して、必要に応じてカスタム変換を作成できます。


**既存のコードを Machine Learning Studio に取り込むことはできますか?**

はい。Machine Learning Studio に既存の R コードまたは Python コードを取り込み、それを Azure Machine Learning の学習ツールと同じ実験で実行し、Azure Machine Learning 経由で Web サービスとしてソリューションをデプロイすることができます。詳細については、「[R を使用した実験の拡張](machine-learning-extend-your-experiment-with-r.md)」および「[Azure Machine Learning Studio での Python Machine Learning スクリプトの実行](machine-learning-execute-python-scripts.md)」を参照してください。

**[PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) などを使用してモデルを定義することはできますか。**

いいえ。サポートされていません。ただし、カスタムの R と Python コードを使用してモジュールを定義できます。

**実験でいくつのモジュールを並列実行できますか。**

実験では最大 4 つのモジュールを並列実行できます。


### データ処理

**実験内で対話的にデータを視覚化する (R の視覚化以上) 機能はありますか。**

モジュールの出力をクリックすると、データを視覚化し、統計情報を取得できます。

**ブラウザーで結果やデータをプレビューするときに、行数と列数が制限されているのはなぜですか。**

データがブラウザーに送信されると大きくなる可能性があるため、Machine Learning Studio のパフォーマンスが低下しないように、データ サイズが制限されています。すべてのデータ/結果を表示するには、データをダウンロードし、Excel などのツールを使用することをお勧めします。

### アルゴリズム

**Machine Learning Studio では既存のどの ML アルゴリズムがサポートされていますか。**

Machine Learning Studio は、拡張性の高い強化された意思決定ツリー、ベイズの推薦システム、ディープ ニューラル ネットワーク、Microsoft Research で開発された意思決定のジャングルなど、最先端のアルゴリズムを提供します。Vowpal Wabbit のような拡張性が高いオープン ソース機械学習パッケージも含まれます。Machine Learning Studio は、複数クラスと二項分類、回帰、クラスタリングで、機械学習アルゴリズムをサポートします。「[Machine Learning のモジュール][machine-learning-modules]」の完全な一覧をご覧ください。

**データを使用するための正しい Machine Learning アルゴリズムを自動的に提案していますか。**

いいえ。ただし、Machine Learning Studio には、各アルゴリズムの結果を比較して問題に合ったアルゴリズムを決定できるさまざまな方法があります。

**提供されているアルゴリズムから 1 つのアルゴリズムを選ぶ際のガイドラインはありますか。** 「[アルゴリズムを選択する方法](machine-learning-algorithm-choice.md)」をご覧ください。

**提供されているアルゴリズムは、R または Python で記述されていますか。**

いいえ。これらのアルゴリズムは、多くの場合、より高いパフォーマンスを提供するためにコンパイル済みの言語で記述されています。

**アルゴリズムの詳細は提供されていますか。**

ドキュメントでアルゴリズムに関する情報を提供しています。使用するアルゴリズムを最適化するために、チューニング用に提供されているパラメーターについて説明しています。

**オンライン ラーニングの任意のサポートはありますか。**

いいえ。現在はプログラムによる再トレーニングのみがサポートされています。

**組み込みのモジュールを使用してニューラル ネットワーク モデルのレイヤーを視覚化できますか。**

番号

**C# または他の言語で独自のモジュールを作成できますか。**

現在、新しいカスタム モジュールは R でのみ作成できます。

### R モジュール

**Machine Learning Studio で使用できるのはどの R パッケージですか。**

Machine Learning Studio では現在、400 を超える CRAN R パッケージがサポートされています。対象となるすべてのパッケージが記載された最新の一覧は[こちら](http://az754797.vo.msecnd.net/docs/RPackages.xlsx)です。また、この一覧を自身で取得する方法については、「[R を使用した実験の拡張](machine-learning-extend-your-experiment-with-r.md)」を参照してください。必要なパッケージがこの一覧にない場合は、[ユーザー フィードバック フォーラム](http://go.microsoft.com/fwlink/?LinkId=404231)でパッケージの名前を指定してください。

**カスタム R モジュールを構築することはできますか。**

はい。詳細については、「[Azure Machine Learning における作成者カスタム R モジュール](machine-learning-custom-r-modules.md)」をご覧ください。

**R 用の REPL 環境はありますか。**

いいえ。Studio には R 用の REPL 環境はありません。

### Python モジュール

**カスタム Python モジュールを構築することはできますか。**

現在はできません。ただし、1 つまたは複数の [Python スクリプトの実行][python]モジュールを使用して、同じ結果を得ることができます。

**Python 用の REPL 環境はありますか。**

Machine Learning Studio では Jupyter Notebooks を使用できます。詳細については、「[Introducing Jupyter Notebooks in Azure Machine Learning Studio (Azure Machine Learning Studio に Jupyter Notebooks を導入する)](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)」を参照してください。

## Web サービス

###モデルをプログラムによって再トレーニングする

**プログラムで Machine Learning のモデルを再トレーニングするにはどうすればよいですか?**

再トレーニング API を使用します。詳細については、「[プログラムによる Machine Learning のモデルの再トレーニング](machine-learning-retrain-models-programmatically.md)」を参照してください。サンプル コードは、「[Microsoft Azure Maching Learning Retraining Demo (Microsoft Azure Maching Learning 再トレーニング デモ)](https://azuremlretrain.codeplex.com/)」にもあります。

### 作成

**ローカルまたはインターネットに接続していないアプリケーションでモデルをデプロイすることはできますか。**

番号


**すべての Web サービスで予測される基本的な遅延はありますか。**

「[Azure サブスクリプションの制限](../azure-subscription-service-limits.md)」をご覧ください。

### 最初の起動時にドメインに参加しているマシンになるように VM をプロビジョニングするには、

**バッチ実行サービスと要求応答サービスとして予測モデルを実行するのはいつがいいですか。**

要求応答サービス (RRS) は、待ち時間が短く、拡張性の高い Web サービスで、実験環境で作成、デプロイされたステートレスなモデルへのインターフェイスを提供するために使用されます。バッチ実行サービス (BES) は、データ レコードのバッチに対して非同期でスコアを付けるためのサービスです。BES への入力は、RRS で使用されるデータ入力と似ています。主な違いは、BES が、BLOB サービス、Azure の Table サービス、Azure SQL Database、HDInsight (Hive Query)、HTTP ソースなどのさまざまなソースからレコードのブロックを読み取ることです。詳細については、「[How to consume Machine Learning web services (Machine Learning Web サービスを使用する方法)](machine-learning-consume-web-services.md)」をご覧ください。

**Web サービスにデプロイされたモデルはどのように更新できますか。**

既にデプロイされたサービスの予測モデルの更新は、トレーニング済みのモデルの作成および保存に使用される実験の変更および再実行と同じくらい簡単です。新しいバージョンのトレーニング済みのモデルが使用できるようになると、Machine Learning Studio は Web サービスを更新するかどうかをたずねます。デプロイされた Web サービスを更新する方法の詳細については、「[Machine Learning Web サービスをデプロイする](machine-learning-publish-a-machine-learning-web-service.md)」をご覧ください。

Retraining API を使用することもできます。詳細については、「[プログラムによる Machine Learning のモデルの再トレーニング](machine-learning-retrain-models-programmatically.md)」を参照してください。サンプル コードは、「[Microsoft Azure Maching Learning Retraining Demo (Microsoft Azure Maching Learning 再トレーニング デモ)](https://azuremlretrain.codeplex.com/)」にもあります。

**運用環境にデプロイされた Web サービスはどのように監視できますか?**

予測モデルがデプロイされると、Azure クラシック ポータルから監視できます。デプロイされた各サービスには専用のダッシュボードがあり、ここでそのサービスについての監視情報を参照できます。デプロイされた Web サービスの管理方法の詳細については、「[Azure Machine Learning ワークスペースの管理](machine-learning-manage-workspace.md)」を参照してください。

**RRS/BES の出力を確認できまる場所はありますか。**

RRS の場合は通常、Web サービスの応答に結果が表示されます。また、Azure Blob Storage に書き込むこともできます。BES の場合、既定では BLOB に出力が書き込まれます。また、[データのエクスポート][export-data] モジュールを使用してデータベースやテーブルに出力を書き込むこともできます。

**Web サービスを作成できるのは Machine Learning Studio で作成されたモデルからのみですか?**

いいえ。Jupyter Notebooks、および RStudio から直接 Web サービスを作成することもできます。

**エラー コードの詳細はどこで入手できますか?**

エラー コードと説明の一覧は、「[Machine Learning Module Error Codes (Machine Learning モジュールのエラー コード)](https://msdn.microsoft.com/library/azure/dn905910.aspx)」を参照してください。

## 拡張性

**Web サービスのスケーラビリティはどれくらいですか。**

現時点では、既定のエンドポイントは 1 つのエンドポイントにつき 20 件の同時 RRS 要求でプロビジョニングされます。これは、エンドポイントごとに 200 同時要求まで拡張でき、「[API エンドポイントのスケーリング](machine-learning-scaling-endpoints.md)」で説明されているように Web サービスごとのエンドポイント数を 10,000 まで拡張できます。BES の場合、各エンドポイントは一度に 40 件の要求を処理でき、40 件を超えた要求はキューに登録されます。これらのキューに登録された要求は、キューから放出されると自動的に実行されます。


**R のジョブはノード間に分散されますか。**

番号


**トレーニングに使用できるデータの量はどれくらいですか?**

Machine Learning Studio のモジュールは、一般的に、最大 10 GB の高密度数値データのデータセットをサポートしています。モジュールが 1 つ以上の入力を受け取る場合、10 GB はすべての入力の合計です。インジェストの前に、Hive または Azure SQL Database のクエリ経由で、または[カウントを使用した学習][counts]モジュールで前処理することにより、さらに大きなデータセットをサンプリングすることもできます。

次の種類のデータは、特徴の正規化の際により大きなデータセットに展開でき、10 GB 未満に制限されています。

- スパース
- カテゴリ
- 文字列
- バイナリ データ

次のモジュールは、10 GB 未満のデータセットに制限されています。

- 推奨モジュール
- SMOTE モジュール
- スクリプト モジュール: R、Python、SQL
- 出力データ サイズが入力データ サイズを超えるモジュール ("結合"、"特徴ハッシュ" など)。
- イテレーションの数が非常に大きい場合のクロス検証、調整モデル ハイパーパラメーター、順序回帰、一対全多クラス。

数 GB を超えるデータセットの場合は、ローカル ファイルから直接アップロードするのではなく、Azure Storage または Azure SQL Database にデータをアップロードするか、HDInsight を使用する必要があります。


**ベクターのサイズに制限はありますか。**

行と列は、それぞれ .NET 制限の相互作用数の上限 2,147,483,647 に制限されています。

**Web サービスの実行に使用されている仮想マシンのサイズを調整することができますか?**

番号

## セキュリティと可用性

**既定で Web サービスの http エンドポイントにはだれがアクセスできますか? エンドポイントへのアクセスはどのようにして制限できますか?**

Web サービスがデプロイされた後に、そのサービスに対して既定のエンドポイントが作成されます。既定のエンドポイントは、その API キーを使用して呼び出すことができます。追加のエンドポイントは、独自のキーを使用して Azure クラシック ポータルから、または Web Service Management API を使用してプログラムにより追加することができます。Web サービスを呼び出すには、アクセス キーが必要です。詳細については、「[Machine Learning Web サービスへの接続](machine-learning-connect-to-azure-machine-learning-web-service.md)」をご覧ください。


**Azure ストレージ アカウントが見つからない場合はどうなりますか?**

Machine Learning Studio は、ワークフローを実行する際に中間データを保存する、ユーザーが指定した Azure ストレージ アカウントに依存しています。このストレージ アカウントは、ワークスペースの作成時に Machine Learning Studio に提供されます。ワークスペースの作成後にストレージ アカウントが削除され、見つけることができない場合は、ワークスペースが機能しなくなり、そのワークスペースのすべての実験が失敗します。

誤ってストレージ アカウントを削除した場合に回復する唯一の方法は、削除されたストレージ アカウンと同じリージョンに同じ名前のストレージ アカウントを再作成することです。その後、アクセス キーを再同期します。


**ストレージ アカウントのアクセス キーの同期が失われてしまった場合はどうなりますか?**

Machine Learning Studio は、ワークフローを実行する際に中間データを保存する、ユーザーが指定した Azure ストレージ アカウントに依存しています。このストレージ アカウントは、ワークスペースの作成時に Machine Learning Studio に提供され、アクセス キーは、そのワークスペースに関連付けられます。ワークスペースの作成後にアクセス キーが変更され、ワークスペースがストレージ アカウントにアクセスできない場合は、ワークスペースが機能しなくなり、そのワークスペースのすべての実験が失敗します。

ストレージ アカウントのアクセス キーを変更した場合は、Azure クラシック ポータルを使用してワークスペースの設定でアクセス キーを再同期します。


## Azure Marketplace

[Machine Learning Marketplace でのアプリの公開と使用に関する FAQ](machine-learning-marketplace-faq.md) のページを参照してください。

## サポートとトレーニング

**どこで Azure Machine Learning のトレーニングを受講できますか?**

[Azure Machine Learning ドキュメント センター](https://azure.microsoft.com/services/machine-learning/)に、ハウツー ガイドやビデオ チュートリアルが用意されています。順を追ったハウツー ガイドでは、サービスの概要に加え、データのインポートから、データのクリーニング、予測モデルの構築、Azure Machine Learning を使用した運用環境へのデプロイまで、データ技術のライフ サイクルを説明します。

Machine Learning Center には継続的に新しい資料が追加されます。Machine Learning Center に追加の学習用資料が必要な場合は、[ユーザー フィードバック フォーラム](https://windowsazure.uservoice.com/forums/257792-machine-learning)でご要望をご投稿いただけます。

[Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning) でトレーニングを検索することもできます。

**どのようにして Azure Machine Learning のサポートを受けることができますか。**

Azure Machine Learning のテクニカル サポートを受けるには、「[Azure のサポート](/support/options/)」に移動し、**[Machine Learning]** を選択します。

Azure Machine Learning については、MSDN にコミュニティ フォーラムがあり、ここで、Azure Machine Learning に関連する質問をすることができます。このフォーラムは Azure Machine Learning チームが監視しています。[Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning)にアクセスしてください。


<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx

<!---HONumber=AcomDC_0615_2016-->