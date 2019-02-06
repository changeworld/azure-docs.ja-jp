---
title: Machine Learning Studio に関してよく寄せられる質問 (FAQ)
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio:効率的な予測モデリングのためのクラウド サービスについて、その課金、機能、制限についての疑問点に答える FAQ です。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 8c381ca02dcfb194b2807735415ef894d72f74fa
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462281"
---
# <a name="azure-machine-learning-studio-faq-capabilities-and-limitations"></a>Azure Machine Learning Studio FAQ: 機能と制限
ここでは、Web サービスを使用して予測モデルの作成とソリューションの運用を可能にするクラウド サービスである Azure Machine Learning についてよく寄せられる質問とその回答をいくつか紹介します。 

## <a name="general-questions"></a>一般的な質問
**Machine Learning Studio とは**

Machine Learning Studio は、Web ブラウザーを使用してアクセスするドラッグ アンド ドロップ キャンバス環境です。 Machine Learning Studio は、視覚的な構成インターフェイスにモジュールのパレットを備えており、実験の形式でエンド ツー エンドのデータサイエンス ワークフローを容易に構築できます。

Machine Learning Studio の詳細については、「 [Machine Learning Studio とは](what-is-ml-studio.md)」をご覧ください。

**Machine Learning API サービスとは**

Machine Learning API サービスを使用すると、Machine Learning Studio に組み込まれているものと同様の予測モデルを、スケーラブルでフォールト トレランスに優れた Web サービスとしてデプロイできます。 Machine Learning API サービスによって作成される Web サービスは、外部アプリケーションと予測分析モデルの間の通信用インターフェイスを提供する REST API として機能します。

詳しくは、「[Azure Machine Learning Web サービスを使用する方法](consume-web-services.md)」をご覧ください。

**クラシック Web サービスの一覧はどこで確認できますか?新しい Azure Resource Manager ベースの Web サービスの一覧はどこで確認できますか?**

クラシック デプロイ モデルを使用して作成された Web サービスと新しい Azure Resource Manager デプロイ モデルを使用して作成された Web サービスは、[Microsoft Azure Machine Learning Web サービス](https://services.azureml.net/) ポータルに一覧表示されます。

クラシック Web サービスは、[Machine Learning Studio](http://studio.azureml.net) の **[Web サービス]** タブにも一覧表示されます。

## <a name="azure-machine-learning-questions"></a>Azure Machine Learning に関する質問
**Azure Machine Learning Web サービスとは何ですか?**

Machine Learning Web サービスは、アプリケーションと Machine Learning ワークフローのスコア付けモデルの間のインターフェイスを提供します。 外部のアプリケーションは Azure Machine Learning を使用して Machine Learning ワークフローのスコア付けモデルとリアルタイムで通信できます。 Machine Learning Web サービスを呼び出すと、予測結果が外部のアプリケーションに返されます。 Web サービスの呼び出しを実行するには、Web サービスのデプロイ時に作成された API キーを渡します。 Machine Learning Web サービスは、Web プログラミング プロジェクトで広く使われているアーキテクチャの REST に基づいています。

Azure Machine Learning には、2 種類の Web サービスがあります。

* 要求応答サービス (RRS):待ち時間が短くスケーラブルなサービス。Machine Learning Studio を使用して作成およびデプロイされたステートレスなモデルへのインターフェイスを提供します。
* バッチ実行サービス (BES):データ レコードのバッチをスコア付けする非同期のサービス。

REST API を使用して Web サービスにアクセスするには、いくつかの方法があります。 たとえば、Web サービスをデプロイしたときに生成されたサンプル コードを使用して、C#、R、または Python でアプリケーションを記述できます。

サンプル コードは以下のページで入手できます。
- Azure Machine Learning Web サービス ポータルの Web サービスの使用に関するページ
- Machine Learning Studio の Web サービス ダッシュボードの API ヘルプ ページ

作成済みのサンプル Microsoft Excel ブックを使用することもできます。これも Machine Learning Studio の Web サービス ダッシュボードで入手できます。

**Azure Machine Learning の主な更新内容を教えてください。**

最新の更新内容については、「[Azure Machine Learning の新機能](../../active-directory/fundamentals/whats-new.md)」をご覧ください。

## <a name="import-and-export-data-for-machine-learning"></a>Machine Learning 用データのインポートとエクスポート
**Machine Learning はどのようなデータ ソースをサポートしていますか。**

Machine Learning Studio の実験にデータをダウンロードする方法は 3 つあります。

- ローカル ファイルをデータセットとしてアップロードする
- モジュールを使用して、クラウド データ サービスからデータをインポートする
- 別の実験から保存されたデータセットをインポートする

サポートされているファイル形式の詳細については、[Machine Learning Studio へのトレーニング データのインポート](import-data.md)に関する記事を参照してください。

### <a id="ModuleLimit"></a>モジュールのデータ セットの大きさはどの程度まで許容されますか。
Machine Learning Studio のモジュールは、一般的に、最大 10 GB の高密度数値データのデータセットをサポートしています。 モジュールが 1 つ以上の入力を受け取る場合、10 GB という値はすべての入力サイズの合計です。 Hive または Azure SQL Database のクエリを使用して、さらに大きなデータセットをサンプリングしたり、インジェストの前に、前処理としてカウントによる学習を使用したりすることもできます。  

次の種類のデータは、特徴の正規化の際に、より大きなデータセットに展開できますが、10 GB 未満に制限されています。

* スパース
* Categorical
* 文字列
* バイナリ データ

次のモジュールは、10 GB 未満のデータセットに制限されています。

* 推奨モジュール
* Synthetic Minority Oversampling Technique (SMOTE) モジュール
* スクリプト モジュール:R、Python、SQL
* 出力データ サイズが入力データ サイズを超えるモジュール ("結合"、"特徴ハッシュ" など)
* イテレーションの数が非常に大きい場合のクロス検証、調整モデル ハイパーパラメーター、順序回帰、一対全多クラス

### <a id="UploadLimit"></a>データのアップロードの制限は何ですか。
数 GB を超えるデータセットの場合は、ローカル ファイルから直接アップロードするのではなく、Azure Storage または Azure SQL Database にデータをアップロードするか、Azure HDInsight を使用する必要があります。

**Amazon S3 からデータを読み取ることはできますか。**

少量のデータがあり、HTTP URL 経由でこれを公開する場合は、[データのインポート][import-data] モジュールを使用できます。 大量のデータの場合は、まず Azure Storage に転送してから、[データのインポート][import-data] モジュールを使用して実験に取り込みます。
<!--

<SEE CLOUD DS PROCESS>
-->

**組み込みイメージの入力機能はありますか。**

イメージの入力機能については、「[Import Images (イメージのインポート)][image-reader]」を参照してください。

## <a name="modules"></a>モジュール
**探しているアルゴリズム、データ ソース、データ形式、データ変換操作が Azure Machine Learning Studio にありません。どうすればいいでしょうか?**

[ユーザー フィードバック フォーラム](https://go.microsoft.com/fwlink/?LinkId=404231)にアクセスして、機能に関する追跡中の要望をご覧いただけます。 探している機能が既に要求されている場合は、その要求に投票を追加できます。 探している機能が存在しない場合は、新しい要求を作成してください。 作成した要求の状態もフォーラムで確認できます。 この一覧はこまめに追跡され、機能の提供状況は頻繁に更新されます。 また、R と Python に対する組み込みのサポートを使用すれば、必要に応じてカスタム変換を作成できます。

**既存のコードを Machine Learning Studio に取り込むことはできますか?**

はい。Machine Learning Studio に既存の R コードまたは Python コードを取り込み、それを Azure Machine Learning の学習ツールと同じ実験で実行し、Azure Machine Learning 経由で Web サービスとしてソリューションをデプロイすることができます。 詳細については、「[R を使用した実験の拡張](extend-your-experiment-with-r.md)」と「[Azure Machine Learning Studio での Python Machine Learning スクリプトの実行](execute-python-scripts.md)」を参照してください。

**[PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) などを使用してモデルを定義することはできますか。**

いいえ、PMML (Predictive Model Markup Language) はサポートされていません。 ただし、カスタムの R と Python コードを使用してモジュールを定義できます。

**実験でいくつのモジュールを並列実行できますか。**  

実験では最大 4 つのモジュールを並列実行できます。

## <a name="data-processing"></a>データ処理
**実験内で対話的にデータを視覚化する (R の視覚化以上) 機能はありますか。**

モジュールの出力をクリックすると、データを視覚化し、統計情報を取得できます。

**ブラウザーで結果やデータをプレビューする際に行数と列数が制限されています。なぜですか?**

大量のデータがブラウザーに送信される可能性があるため、Machine Learning Studio のパフォーマンスが低下しないように、データ サイズが制限されています。 すべてのデータ/結果を表示するには、データをダウンロードし、Excel などのツールを使用することをお勧めします。

## <a name="algorithms"></a>アルゴリズム
**Machine Learning Studio では既存のどの ML アルゴリズムがサポートされていますか。**

Machine Learning Studio は、拡張性の高い強化された意思決定ツリー、ベイズの推薦システム、ディープ ニューラル ネットワーク、Microsoft Research で開発された意思決定のジャングルなど、最先端のアルゴリズムを提供します。 Vowpal Wabbit のようなスケーラブルなオープン ソース機械学習パッケージも含まれます。 Machine Learning Studio は、複数クラスと二項分類、回帰、クラスタリングで、機械学習アルゴリズムをサポートします。 「[Machine Learning Modules (Machine Learning のモジュール)][machine-learning-modules]」の完全な一覧をご覧ください。

**データを使用するための正しい Machine Learning アルゴリズムを自動的に提案していますか。**

いいえ。ただし、Machine Learning Studio には、各アルゴリズムの結果を比較して問題に合ったアルゴリズムを決定できるさまざまな方法があります。

**提供されているアルゴリズムから 1 つのアルゴリズムを選ぶ際のガイドラインはありますか。**

[アルゴリズムの選択方法](algorithm-choice.md)」に関するページをご覧ください。

**提供されているアルゴリズムは、R または Python で記述されていますか。**

いいえ。これらのアルゴリズムは、多くの場合、より優れたパフォーマンスを提供するためにコンパイル済みの言語で記述されています。

**アルゴリズムの詳細は提供されていますか。**

ドキュメントでアルゴリズムに関する情報を提供しています。使用するアルゴリズムを最適化するために、チューニング用のパラメーターについて説明しています。  

**オンライン ラーニングの任意のサポートはありますか。**

いいえ。現在はプログラムによる再トレーニングのみがサポートされています。

**組み込みのモジュールを使用してニューラル ネットワーク モデルのレイヤーを視覚化できますか。**

いいえ。

**C# または他の言語で独自のモジュールを作成できますか。**

現在、新しいカスタム モジュールは R でのみ作成できます。

## <a name="r-module"></a>R モジュール
**Machine Learning Studio で使用できるのはどの R パッケージですか。**

Machine Learning Studio では現在、400 を超える CRAN R パッケージがサポートされています。対象となるすべてのパッケージが記載された最新の一覧については、[こちら](http://az754797.vo.msecnd.net/docs/RPackages.xlsx)を参照してください。 また、この一覧を自身で取得する方法については、「[R を使用した実験の拡張](extend-your-experiment-with-r.md)」を参照してください。 必要なパッケージがこの一覧にない場合は、[ユーザー フィードバック フォーラム](https://go.microsoft.com/fwlink/?LinkId=404231)でパッケージの名前を指定してください。

**カスタム R モジュールを構築することはできますか。**

はい。詳細については、「[Azure Machine Learning でカスタム R モジュールを作成する](custom-r-modules.md)」をご覧ください。

**R 用の REPL 環境はありますか。**

いいえ。Studio には R 用の REPL (Read-Eval-Print-Loop) 環境はありません。

## <a name="python-module"></a>Python モジュール
**カスタム Python モジュールを構築することはできますか。**

現在はできません。ただし、1 つまたは複数の [Python スクリプトの実行][python]モジュールを使用して、同じ結果を得ることができます。

**Python 用の REPL 環境はありますか。**

Machine Learning Studio では Jupyter Notebooks を使用できます。 詳細については、 [Azure Machine Learning Studio への Jupyter Notebooks の導入](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)に関するブログ記事を参照してください。

## <a name="web-service"></a>Web サービス

**プログラムで Machine Learning のモデルを再トレーニングするにはどうすればよいですか?**

再トレーニング API を使用してください。 詳細については、「 [プログラムによる Machine Learning のモデルの再トレーニング](retrain-models-programmatically.md)」をご覧ください。 サンプル コードは、「 [Microsoft Azure Maching Learning Retraining Demo (Microsoft Azure Maching Learning 再トレーニング デモ)](https://azuremlretrain.codeplex.com/)」にもあります。

**ローカルまたはインターネットに接続していないアプリケーションでモデルをデプロイすることはできますか?**

いいえ。

**すべての Web サービスで予測される基本的な遅延はありますか。**

[Azure サブスクリプションの制限](../../azure-subscription-service-limits.md)に関するページをご覧ください。

**バッチ実行サービスと要求応答サービスとして予測モデルを実行するのはいつがいいですか。**

要求応答サービス (RRS) は、待ち時間が短く、拡張性の高い Web サービスで、実験環境で作成、デプロイされたステートレスなモデルへのインターフェイスを提供するために使用されます。 バッチ実行サービス (BES) は、データ レコードのバッチに対して非同期でスコアを付けるサービスです。 BES への入力は、RRS で使用されるデータ入力と似ています。 主な違いは、BES が、Azure Blob Storage、Azure Table Storage、Azure SQL Database、HDInsight (Hive クエリ)、HTTP ソースなど、さまざまなソースからレコードのブロックを読み取る点です。 詳しくは、「[Azure Machine Learning Web サービスを使用する方法](consume-web-services.md)」をご覧ください。

**Web サービスにデプロイされたモデルはどのように更新できますか。**

既にデプロイされたサービスの予測モデルを更新するには、トレーニング済みのモデルの作成および保存に使用した実験を変更し、再実行します。 新しいバージョンのトレーニング済みモデルが使用できるようになると、Machine Learning Studio は Web サービスを更新するかどうかをたずねます。 デプロイされた Web サービスを更新する方法の詳細については、[Machine Learning Web サービスのデプロイ](publish-a-machine-learning-web-service.md)に関するページをご覧ください。

Retraining API を使用することもできます。
詳細については、「 [プログラムによる Machine Learning のモデルの再トレーニング](retrain-models-programmatically.md)」をご覧ください。 サンプル コードは、「 [Microsoft Azure Maching Learning Retraining Demo (Microsoft Azure Maching Learning 再トレーニング デモ)](https://azuremlretrain.codeplex.com/)」にもあります。

**運用環境にデプロイされた Web サービスはどのように監視できますか?**

予測モデルは、デプロイ後、Azure Machine Learning Web サービス ポータルから監視できます。 デプロイされた各サービスには専用のダッシュボードがあり、ここでそのサービスについての監視情報を参照できます。 デプロイされた Web サービスの管理方法の詳細については、「[Azure Machine Learning Web サービス ポータルを使用して Web サービスを管理する](manage-new-webservice.md)」および「[Azure Machine Learning ワークスペースの管理](manage-workspace.md)」を参照してください。

**RRS/BES の出力を確認できまる場所はありますか。**

RRS の場合は通常、Web サービスの応答に結果が表示されます。 また、Azure Blob Storage に書き込むこともできます。 BES の場合、既定では BLOB に出力が書き込まれます。 また、[データのエクスポート][export-data] モジュールを使用してデータベースやテーブルに出力を書き込むこともできます。

**Web サービスを作成できるのは Machine Learning Studio で作成されたモデルからのみですか?**

いいえ。Jupyter Notebooks と RStudio から直接 Web サービスを作成することもできます。

**エラー コードの詳細はどこで入手できますか?**

エラー コードと説明の一覧は、「 [Machine Learning Module Error Codes (Machine Learning モジュールのエラー コード)](https://msdn.microsoft.com/library/azure/dn905910.aspx) 」を参照してください。

**Web サービスのスケーラビリティはどれくらいですか。**

現時点では、既定のエンドポイントは 1 つのエンドポイントにつき 20 件の同時 RRS 要求でプロビジョニングされます。 これは、[Web サービスのスケーリング](scaling-webservice.md)に関するページで説明されているように、エンドポイントあたりの同時要求を 200 件まで拡張でき、Web サービスごとのエンドポイント数は 10,000 個まで拡張できます。 BES の場合、各エンドポイントは一度に 40 件の要求を処理でき、40 件を超えた要求はキューに登録されます。 これらのキューに登録された要求は、キューから放出されると自動的に実行されます。

**R のジョブはノード間に分散されますか。**

いいえ。  

**トレーニングに使用できるデータの量はどれくらいですか?**

Machine Learning Studio のモジュールは、一般的に、最大 10 GB の高密度数値データのデータセットをサポートしています。 モジュールが 1 つ以上の入力を受け取る場合、10 GB はすべての入力の合計です。 インジェストの前に、Hive クエリ、Azure SQL Database クエリ、または[カウントを使用した学習][counts]モジュールによる前処理を使用して、さらに大きなデータセットをサンプリングすることもできます。  

次の種類のデータは、特徴の正規化の際に、より大きなデータセットに展開できますが、10 GB 未満に制限されています。

* スパース
* Categorical
* 文字列
* バイナリ データ

次のモジュールは、10 GB 未満のデータセットに制限されています。

* 推奨モジュール
* Synthetic Minority Oversampling Technique (SMOTE) モジュール
* スクリプト モジュール:R、Python、SQL
* 出力データ サイズが入力データ サイズを超えるモジュール ("結合"、"特徴ハッシュ" など)
* イテレーションの数が非常に大きい場合のクロス検証、調整モデル ハイパーパラメーター、順序回帰、一対全多クラス

数 GB を超えるデータセットの場合は、ローカル ファイルから直接アップロードするのではなく、Azure Storage または Azure SQL Database にデータをアップロードするか、HDInsight を使用してください。

**ベクターのサイズに制限はありますか。**

行と列は、それぞれ .NET 制限の相互作用数の上限2,147,483,647 に制限されています。

**Web サービスを実行する仮想マシンのサイズは調整できますか?**

いいえ。  

## <a name="security-and-availability"></a>セキュリティと可用性
**既定では Web サービスの http エンドポイントにだれがアクセスできますか?エンドポイントへのアクセスはどのようにして制限できますか?**

Web サービスがデプロイされた後に、そのサービスに対して既定のエンドポイントが作成されます。 既定のエンドポイントは、その API キーを使用して呼び出すことができます。 エンドポイントの追加は、Web サービス ポータルからそれぞれのキーを使用して行うか、Web Service Management API を使用してプログラムで行うことができます。 Web サービスを呼び出すには、アクセス キーが必要です。 詳しくは、「[Azure Machine Learning Web サービスを使用する方法](consume-web-services.md)」をご覧ください。

**Azure ストレージ アカウントが見つからない場合はどうなりますか?**

Machine Learning Studio は、ワークフローの実行時に中間データを保存する際、ユーザーが指定した Azure ストレージ アカウントを使用します。 このストレージ アカウントは、ワークスペースの作成時に Machine Learning Studio に提供されます。 ワークスペースの作成後にストレージ アカウントが削除され、見つけることができない場合は、ワークスペースが機能しなくなり、そのワークスペースのすべての実験が失敗します。

誤ってストレージ アカウントを削除した場合は、削除されたストレージ アカウンと同じリージョンに同じ名前のストレージ アカウントを再作成します。 その後、アクセス キーを再同期します。

**ストレージ アカウントのアクセス キーの同期が失われてしまった場合はどうなりますか?**

Machine Learning Studio は、ワークフローの実行時に中間データを格納する際、ユーザーが指定した Azure ストレージ アカウントを使用します。 このストレージ アカウントは、ワークスペースの作成時に Machine Learning Studio に提供され、アクセス キーはそのワークスペースに関連付けられます。 ワークスペースの作成後にアクセス キーが変更されると、ワークスペースはストレージ アカウントにアクセスできなくなります。 その場合、機能が停止し、そのワークスペース内のすべての実験が失敗します。

ストレージ アカウントのアクセス キーを変更した場合は、Azure Portal を使用してワークスペースのアクセス キーを再同期します。  


## <a name="billing-questions"></a>課金に関する質問

課金と料金の詳細については、「[Machine Learning の価格](https://azure.microsoft.com/pricing/details/machine-learning/)」を参照してください。


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
