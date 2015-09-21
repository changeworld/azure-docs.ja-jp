<properties
	pageTitle="Machine Learning Web サービスをデプロイする | Microsoft Azure"
	description="トレーニング実験を予測実験に変換する方法、デプロイメントの準備をする方法、Azure Machine Learning Web サービスとしてデプロイする方法について説明します。"
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
	ms.topic="article"
	ms.date="09/09/2015"
	ms.author="garye"/>

# Azure Machine Learning Web サービスをデプロイする

Azure Machine Learning では、予測分析ソリューションをビルド、テスト、およびデプロイできます。

大まかに次の 3 つの手順で行われます。

- **[トレーニング実験を作成する]** - Azure Machine Learning Studio は、供給するトレーニング データを活用した予測分析モデルの学習とテストに使用できる、コラボレーションと視覚化に対応した開発環境です。
- **[予測実験に変換する]** - 既存のデータでモデルが学習され、それを使用して新しいデータをスコア付けする準備ができると、スコア付け用に実験を用意し、合理化します。
- **[Web サービスとしてデプロイする]** - 1 クリックで、Azure の Web サービスとしてスコア付け実験を発行できます。ユーザーはご利用のモデルにデータを送信し、モデルの予測を受信できます。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Web サービスを発行すると、次を実行できます。

- Web サービス API からの**[アクセス]**
- Microsoft Azure 管理ポータルでの**[管理]**
- モデルが変更された際の**[更新]**

[トレーニング実験を作成する]: #create-a-training-experiment
[予測実験に変換する]: #convert-the-training-experiment-to-a-predictive-experiment
[Web サービスとしてデプロイする]: #deploy-the-predictive-experiment-as-a-web-service
[アクセス]: #access-the-web-service
[管理]: #manage-the-web-service-in-the-azure-management-portal
[更新]: #update-the-web-service


## トレーニング実験を作成する

予測分析モデルを学習するには、Azure Machine Learning Studio を使用してトレーニング実験を作成します。ここではトレーニング データを読み込み、必要に応じてデータを用意し、機械学習アルゴリズムに適用し、結果を評価するさまざまなモジュールを含めることができます。実験を繰り返し、別の機械学習アルゴリズムを試して結果を比較したり評価したりできます。

トレーニング実験を作成したり管理したりするプロセスはあらゆる場所でより包括的に使用できます。詳細と例については、次の記事をご覧ください。

- [Azure Machine Learning Studio での簡単な実験の作成](machine-learning-create-experiment.md)
- [Azure Machine Learning を使用した予測ソリューションの開発](machine-learning-walkthrough-develop-predictive-solution.md)
- [Azure Machine Learning Studio への学習データのインポート](machine-learning-import-data.md)
- [Azure Machine Learning Studio での繰り返し学習の管理](machine-learning-manage-experiment-iterations.md)

## トレーニング実験を予測実験に変換する

モデルをトレーニングすると、新しいデータをスコア付けで使用できるようになります。そのためには、トレーニング実験を予測実験に変換します。予測実験に変換することで、トレーニングされたモデルをスコア付けの Web サービスとして公開できるようになります。Web サービスのユーザーがモデルに入力データを送信し、モデルは予測結果を返します。したがって、予測実験を変換した場合は、モデルが他のユーザーに使用されることを想定しておいてください。

トレーニング実験を予測実験に変換するには、実験キャンバスの一番下にある **[実行]** をクリックし、**[Web サービスの設定]** をクリックします。

![スコア付け実験に変換する](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

この変換を実行する方法の詳細については、「[Machine Learning トレーニング実験を予測実験に変換する](machine-learning-convert-training-experiment-to-scoring-experiment.md)」をご覧ください。


## Web サービスとして予測実験をデプロイする

予測実験の準備を十分に実行したので、Azure Web サービスとして発行できます。Web サービスを使用してユーザーはデータをモデルに送信でき、モデルは予測を返します。

予測実験を発行するには、実験キャンバスの下部で **[実行]** をクリックし、**[WEB サービスのデプロイ]** をクリックします。Web サービスがセットアップされ、Web サービスのダッシュ ボードに配置されます。

![Web サービスをデプロイする](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

Web サービスをテストするには、Web サービス ダッシュボードで **[テスト]** リンクをクリックします。ダイアログ ボックスが表示され、サービスへのデータの入力が促されます。これらはスコア付け実験で想定される列になります。データのセットを入力し、**[OK]** をクリックします。Web サービスによって生成された結果がダッシュボードの下部に表示されます。

![Web サービスをテストする](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

**[構成]** タブで、サービスの表示名を変更したり、説明を付けたりできます。Web サービスを管理する Microsoft Azure 管理ポータルで名前と説明が表示されます。**INPUT SCHEMA**、**OUTPUT SCHEMA**、**WEB SERVICE PARAMETER** の各列に文字列を入力し、入力データ、出力データ、Web サービス パラメーターの説明を追加できます。これらの説明は、Web サービスのサンプル コードのドキュメントで使用されます。また、ログ記録を有効にすれば、Web サービスのアクセスで発生するすべてのエラーを診断できます。

詳細については、「[Enable logging for Machine Learning web services (Machine Learning Web サービスのログ記録を有効にする)](machine-learning-web-services-logging.md)」をご覧ください。

![Web サービスを構成する](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)


## Web サービスにアクセスする

Machine Learning Studio から Web サービスをデプロイすると、サービスにデータを送信し、プログラムからの応答が得られます。

ダッシュ ボードでは、Web サービスへのアクセスが必要なすべての情報が提供されます。たとえば、サービスへの承認済みアクセスを許可する API キーや、コードの記述に役立つ API のヘルプ ページが提供されます。

Machine Learning Web サービスにアクセスする詳細については、「[How to consume a published Azure Machine Learning web service (発行済み Azure Machine Web サービスを使用する方法)](machine-learning-consume-web-services.md)」をご覧ください。


## Microsoft Azure 管理ポータルで Web サービスを管理する

Microsoft Azure 管理ポータルでは、**[Machine Learning]** サービスをクリックして Machine Learning ワークスペースを開き、その後 **[WEB SERVICES]** タブから Web サービスを開くことで Web サービスを管理できます。このページから Web サービスの監視、更新、削除ができます。Web サービスを発行するときに作成される既定のエンドポイントに加えて 2 つ目のエンドポイントを追加することもできます。

詳細については、「[Azure Machine Learning ワークスペースの管理](machine-learning-manage-workspace.md)」をご覧ください。<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->


## Web サービスを更新する

別のトレーニング データでのモデルの更新など、Web サービスを変更したり、元の Web サービスを上書きしてもう一度発行したりできます。

Web サービスを更新するには、Web サービスのデプロイに使用した元の予測実験を開き、**[名前を付けて保存]** をクリックして編集可能なコピーを作成します。必要な変更を加えて **[WEB サービスのデプロイ]** をクリックします。この実験は以前に公開しているため、Machine Learning Studio で既存のサービスを上書きするかどうかの確認が求められます。**[はい]** をクリックすると既存の Web サービスが停止し、新しい予測実験が代わりに発行されます。

> [AZURE.NOTE]たとえば元の Web サービスの構成に変更を加えた場合や新しい表示名または説明を入力した場合、それらの値をもう一度入力する必要が生じます。

Web サービスを更新するオプションの 1 つに、モデルをプログラムによって再トレーニングする方法があります。詳細については、「[プログラムによる Machine Learning のモデルの再トレーニング](machine-learning-retrain-models-programmatically.md)」をご覧ください。

<!---HONumber=Sept15_HO2-->