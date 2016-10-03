<properties
	pageTitle="手順 5: Machine Learning Web サービスをデプロイする | Microsoft Azure"
	description="予測ソリューションの開発チュートリアルの 5 番目の手順: Machine Learning Studio で予測実験を Web サービスとしてデプロイする。"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="garye"/>


# チュートリアル手順 5: Azure Machine Learning Web サービスをデプロイする

これは、「[チュートリアル: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する](machine-learning-walkthrough-develop-predictive-solution.md)」の 5 番目の手順です。


1.	[Machine Learning ワークスペースの作成](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[既存のデータをアップロードする](machine-learning-walkthrough-2-upload-data.md)
3.	[新しい実験を作成する](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[モデルをトレーニングして評価する](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	**Web サービスをデプロイする**
6.	[Web サービスにアクセスする](machine-learning-walkthrough-6-access-web-service.md)

----------

このチュートリアルで作成した予測モデルを他のユーザーが使用できるように、Web サービスとして Azure にデプロイします。

これまでは、モデルのトレーニングを実験してきました。ただし、デプロイするサービスのトレーニングはこれ以上行われません。サービスは、モデルに基づいてユーザーの入力をスコア付けすることで予測を生成します。このため、この実験を***トレーニング***実験から***予測***実験に変換するための準備をします。

これは 2 段階のプロセスです。

1. 作成した*トレーニング実験*を*予測実験*に変換する
2. Web サービスとして予測実験をデプロイする

最初に、この実験を少しスリム化する必要があります。現在、実験には 2 つの異なるモデルがありますが、Web サービスとしてデプロイするサービスでは、モデルは 1 つだけ使用します。

使用にあたってはブースト ツリー モデルの方が優れていると判断したとします。最初に、[2 クラス サポート ベクター マシン][two-class-support-vector-machine] モジュールとそのトレーニングの使用したモジュールを削除します。まずは実験キャンバスの下にある **[名前を付けて保存]** をクリックして実験のコピーを作成します。

次のモジュールを削除する必要があります。

- [2 クラス サポート ベクター マシン][two-class-support-vector-machine]
- 接続されている[モデルのトレーニング][train-model] モジュールと[モデルのスコア付け][score-model]モジュール
- [データの正規化][normalize-data] \(両方)
- [モデルの評価][evaluate-model]

モジュールを選択して Delete キーを押すか、モジュールを右クリックして **[削除]**を選択します。

これで、[2 クラスのブースト デシジョン ツリー][two-class-boosted-decision-tree]を使用するモデルをデプロイする準備ができました。

## トレーニング実験を予測実験に変換する

予測実験に変換するには、次の 3 つの手順を実行する必要があります。

1. トレーニングしたモデルを保存し、トレーニング モジュールを置き換えます。
2. 実験をトリミングしてトレーニングのためのみに必要だったモジュールを削除します。
3. Web サービスが入力を受け取る場所と出力を生成する場所を定義します。

幸いにも、この 3 つの手順はすべて実験キャンバスの下にある **[Web サービスの設定]** をクリックするだけで完了できます (**[予測 Web サービス]** オプションを選択します)。

**[Web サービスの設定]** をクリックすると、次の動作が行われます。

- トレーニングしたモデルは、1 つの**トレーニング済みのモデル** モジュールとして、実験キャンバスの左側にあるモジュール パレットに保存されます (**[トレーニング済みのモデル]** の下にあります)。
- トレーニングに使用したモジュールは削除されます。具体的には次の処理が行われます。
  - [2クラスのブースト デシジョン ツリー][two-class-boosted-decision-tree]
  - [モデルのトレーニング][train-model]
  - [データの分割][split]
  - テスト データに使用した 2 番目の [R スクリプトの実行][execute-r-script]モジュール
- 保存したトレーニング済みのモデルは実験に追加されます。
- **Web サービスの入力** と **Web サービスの出力**モジュールが追加されます。

> [AZURE.NOTE] 実験は、実験キャンバスの上部に追加された 2 つのタブに保存されています。トレーニング実験は **[トレーニング実験]** タブに、新しく作成された予測実験は **[予測実験]** タブに保存されています。

この特定の実験では、手順を 1 つ追加で実行する必要があります。トレーニング用とテスト用のデータに重み関数を提供するために、2 つの [R スクリプトの実行][execute-r-script]モジュールが追加されています。最終モデルでは、これを行う必要がありません。1 つの [R スクリプトの実行][execute-r-script]モジュールは、Machine Learning Studio が[分割][split]モジュールを削除するときに削除されるため、残りの 1 つを削除し、[メタデータ エディター][metadata-editor]を[スコア モデル][score-model]に直接接続できます。

現在、実験は以下のようになっています。

![Scoring the trained model][4]


> [AZURE.NOTE] UCI のドイツ語のクレジット カード データ データセットはどうして予測実験に残されたか疑問に思われるでしょう。このサービスでは、元のデータセットではなくユーザーのデータを使用します。それにもかかわらずなぜ元のデータセットを残すのでしょうか?
>
>サービスで元のクレジット カード データを必要としないのは本当です。しかし、列がいくつあり、どの列が数値型であるかなどの情報を含むこのデータのスキーマが必要になります。このスキーマ情報は、ユーザーのデータを解釈するうえで必要になります。そこで、これらのコンポーネントを接続したままにして、サービスが実行されているときにスコア付けモジュールがデータセット スキーマを利用できるようにしています。データは使用されません。スキーマだけが使用されます。

最後にもう一度実験を実行します (**[実行]** をクリックします)。モデルがまだ稼動していることを確認するには、[モデルのスコア付け][score-model]モジュールをクリックし、**[結果の表示]** を選択します。元のデータが、信用リスク値 ("スコア付けラベル") とスコア付け確率値 ("スコア付け確率") と共に表示されます。

## Web サービスをデプロイする

Azure Resource Managerに基づく新しい Web サービスか、従来の Web サービスのどちらかとして実験をデプロイできます。

### 従来の Web サービスとしてデプロイする ###

実験から派生する従来の Web サービスをデプロイするには、キャンバスの下にある **[Web サービスのデプロイ]** をクリックし、**[Deploy Web Service [New]\(Web サービスのデプロイ [新規])]** を選択します。Machine Learning Studio によって実験が Web サービスとしてデプロイされ、その Web サービス用のダッシュボードに移動します。そこから、実験に戻って (**[スナップショットの表示]** または **[最新の表示]** をクリックします)、Web サービスの簡単なテストを実行できます (**[テスト]** ボタンをクリックします。次の「**Web サービスをテストする**」セクションを参照してください )。この Web サービスにアクセスできるアプリケーションを作成するための情報も表示されます (詳細はこのチュートリアルの次の手順を参照してください)。

![Web サービス ダッシュボード][6]

サービスを構成するには、**[構成]** タブをクリックします。このタブでは、サービス名 (既定で実験名が付けられます) を変更したり、説明を追加したりできます。また、入力列と出力列にわかりやすいラベルを付けることもできます。

![Web サービスを構成する][5]

### 新しい Web サービスとしてデプロイする

実験から派生する新しい Web サービスをデプロイするには、キャンバスの下にある **[Web サービスのデプロイ]**、**[Deploy Web Service [New]\(Web サービスのデプロイ [新規])]** の順にクリックします。Machine Learning Studio から Azure Machine Learning Web サービスの [Deploy Experiment (実験のデプロイ)] ページに転送されます。

Web サービスの名前を入力し、料金プランを選択します。既存の料金プランがある場合はそのプランを選択できます。ない場合は、サービス用に新しい料金プランを作成する必要があります。

1.	**[Price Plan (料金プラン)]** ドロップダウンで、既存のプランを選択するか、**[Select new plan (新しいプランを選択する)]** オプションを選択します。
2.	**[プラン名]** に、請求書でプランを識別する名前を入力します。
3.	**[Monthly Plan Tiers (月額プラン レベル)]** のいずれか 1 つを選択します。プラン レベルは既定では既定のリージョンのプランになり、Web サービスはそのリージョンにデプロイされることにご注意ください。

**[デプロイ]** をクリックすると、Web サービスの **[クイック スタート]** ページが開きます。

サービスを構成するには、**[構成]** メニュー オプションをクリックします。このタブでは、サービス名 (既定で実験名が付けられます) を変更したり、説明を追加したりできます。エラー ログと、サービスをテストするために使用できるサンプル データを有効または無効にすることもできます。

Web サービスをテストするには、**[テスト]** メニュー オプションを選択します (以下の「**Web サービスをテストする**」を参照してください)。この Web サービスにアクセスできるアプリケーションを作成するための情報については、**[Consume (使用)]** メニュー オプションをクリックしてください (詳細はこのチュートリアルの次の手順を参照してください)。


> [AZURE.TIP] Web サービスをデプロイした後で更新できます。たとえばモデルを変更する場合、トレーニング実験だけ編集し、モデルのパラメーターを調整して **[Web サービスのデプロイ]** をクリックします。**[Deploy Web Service [Classic]\(Web サービスのデプロイ [従来])]** または **[Deploy Web Service [New]\(Web サービスのデプロイ [新規])]** を選択します。もう一度実験をデプロイすると、Web サービスが置き換えられ、更新済みのモデルが使用されるようになります。


## Web サービスをテストする

**従来の Web サービスをテストする** **[ダッシュボード]** ページで、**[既定のエンドポイント]** の **[テスト]** ボタンをクリックします。サービスの入力データを要求するダイアログが表示されます。これらは、元のドイツの信用リスク データセットに含まれるのと同じ列です。

データのセットを入力し、**[OK]** をクリックします。

Web サービスでは、データは **Web サービスの入力**モジュールから[メタデータ エディター][metadata-editor] モジュールを経由して[モデルのスコア付け][score-model] モジュールに入力され、そこでスコア付けされます。結果は、Web サービスから **Web サービスの出力**を通じて出力されます。

**新しい Web サービスをテストする**

Azure Machine Learning Web サービス ポータルで、ページ上部の **[テスト]** メニュー オプションをクリックします。[テスト] ページが開かれ、サービスのデータを入力することができます。表示される入力フィールドは、元のドイツの信用リスク データセットに含まれる列に対応しています。

データのセットを入力し、**[Test Request-Response (要求応答のテスト)]** をクリックします。

要求応答サービスのテストに使用できるサンプル データを有効にすることもできます。Machine Learning Studio で Web サービスを作成した場合は、サンプル データはモデルのトレーニングに使用したデータから取得されます。

テストの結果は、ページの右側の出力列に表示されます。

> [AZURE.TIP] 予測実験の構成方法に従って、[モデルのスコア付け][score-model]モジュールから結果全体が返されます。これには、すべての入力データに加え、信用リスク値とスコア付け確率が含まれます。これとは別の結果を戻す場合、たとえば信用リスク値のみを戻す場合は、[列の投影][project-columns]モジュールを[モデルのスコア付け][score-model]と **Web サービスの出力**の間に挿入して、Web サービスから返さない列を排除します。

## Web サービスを管理する

**従来の Web サービスを管理する**

デプロイした従来の Web サービスは、[Azure クラシック ポータル](https://manage.windowsazure.com)から管理できます。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)にサインインします。
2. Microsoft Azure サービス パネルで、**[MACHINE LEARNING]** をクリックします。
3. ワークスペースをクリックします。
4. **[Web サービス]** タブをクリックします。
5. 先ほど作成した Web サービスをクリックします。
6. "既定の" エンドポイントをクリックします。

そこから、Web サービスの動作の監視や、サービスが処理できる同時呼び出し数の変更によるパフォーマンスの微調整などを実行できます。Web サービスを Azure Marketplace に発行することもできます。

詳細については、次のリンクを参照してください。

- [エンドポイントを作成する](machine-learning-create-endpoint.md)
- [Web サービスのスケーリング](machine-learning-scaling-webservice.md)
- [Azure Marketplace への Azure Machine Learning Web サービスの発行](machine-learning-publish-web-service-to-azure-marketplace.md)

**新しい Web サービスを管理する**

デプロイした新しい Web サービスは、[Azure Machine Learning Web サービス ポータル](https://servics.azureml.net)から管理できます。

Web サービスのパフォーマンスを監視するには:
1. [Azure Machine Learning Web サービス ポータル](https://servics.azureml.net)にサインインします。
2. **[Web サービス]** をクリックします。
3. Web サービスをクリックします。
4. **[ダッシュボード]** をクリックします。

----------

**次: [Web サービスにアクセスする](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/ja-JP/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/

<!---HONumber=AcomDC_0921_2016-->