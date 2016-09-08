<properties
	pageTitle="Azure Machine Learning Web サービス: デプロイと使用 | Microsoft Azure"
	description="Web サービスをデプロイおよび使用するためのリソース。"
	services="machine-learning"
	documentationCenter=""
	authors="vDonGlover"
	manager="raymondl"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="v-donglo"/>

# Azure Machine Learning Web サービス: デプロイと使用

Azure Machine Learning (Azure ML) により、Machine Learning のワークフローとモデルを Web サービスとしてデプロイできます。この Web サービスを使用すると、インターネット経由で ML モデルをアプリケーションから呼び出して、リアルタイムまたはバッチ モードで予測することができます。RESTfull であるため、Web サービスは、さまざまなプログラミング言語やプラットフォーム (.NET、Java など)、およびアプリケーション (Excel など) から呼び出すことができます。

次のセクションでは、こうした手順について説明し、使用開始に役立つコードとドキュメントへのリンクを紹介します。

## デプロイ

### Azure ML Studio の使用

Machine Learning Studio と Microsoft Azure Machine Learning Web サービス ポータルを使用すると、コードを記述せずに、Web サービスをデプロイして管理することができます。

新しい Web サービスのデプロイ プロセスに関する一般的な情報を入手できるリンクを次に示します。

* Azure Resource Manager に基づいた新しい Web サービスのデプロイの概要については、「[新しい Web サービスのデプロイ](machine-learning-webservice-deploy-a-web-service.md)」をご覧ください。
* Web サービスのデプロイのチュートリアルについては、「[Azure Machine Learning Web サービスをデプロイする](machine-learning-publish-a-machine-learning-web-service.md)」をご覧ください。

Web サービスを作成してデプロイする方法の詳細なチュートリアルについては、「[チュートリアル手順 1: Machine Learning ワークスペースの作成](machine-learning-walkthrough-1-create-ml-workspace.md)」をご覧ください。

Web サービス デプロイの具体的な例については、次をご覧ください。

* [チュートリアル手順 5: Azure Machine Learning Web サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)
* [複数のリージョンに Web サービスをデプロイする方法](machine-learning-how-to-deploy-to-multiple-regions.md)

### Web サービス リソース プロバイダー API の使用 (Azure Resource Manager API)

Web サービス用の Azure ML リソース プロバイダーでは、REST API を使用して Web サービスをデプロイし、管理することができます。詳細については、次の記事とサンプル コードをご覧ください。

* MSDN の [Machine Learning Web サービス (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) リファレンス。


### PowerShell コマンドレットの使用

Web サービス用の Azure ML リソース プロバイダーでは、PowerShell コマンドレットを使用して Web サービスをデプロイし、管理することができます。

コマンドレットを使用するには、最初に [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) コマンドレットを使用して、PowerShell 環境から Azure アカウントにログインする必要があります。Resource Manager に基づいた PowerShell コマンドの呼び出しについて詳しくない場合は、「[Azure Resource Manager での Azure PowerShell の使用](../powershell-azure-resource-manager.md#login-to-your-azure-account)」をご覧ください。

予測実験をエクスポートするには、こちらの[サンプル コード](https://github.com/ritwik20/AzureML-WebServices)を使用します。コードから実行可能ファイルを作成したら、次のように入力します。

	C:<folder>\GetWSD <experiment-url> <workspace-auth-token>

アプリケーションを実行すると、Web サービスの JSON テンプレートが作成されます。テンプレートを使用して Web サービスをデプロイするには、次の情報を追加する必要があります。

* ストレージ アカウントの名前とキー
	* ストレージ アカウントの名前とキーは、新しい Azure ポータルまたは従来の Azure ポータルから取得できます。
* コミットメント プラン ID
	* プラン ID を取得するには、[Azure Machine Learning Web サービス](https://services.azureml.net) ポータルにログインし、プラン名をクリックします。

この情報を、*MachineLearningWorkspace* ノードと同じレベルで、*Properties* ノードの子として JSON テンプレートに追加します。

以下の例をご覧ください。

	"StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
	},
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

詳細については、次の記事とサンプル コードをご覧ください。

* MSDN の [Azure Machine Learning コマンドレット](https://msdn.microsoft.com/library/azure/mt767952.aspx) リファレンス
* GitHub のサンプルの[チュートリアル](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt)。

## Web サービスの使用

### Azure ML Web サービスの UI から (テスト)

Azure ML Web サービス ポータルから Web サービスをテストできます。これには、RRS および BES インターフェイスのテストが含まれます。

* [新しい Web サービスのデプロイ](machine-learning-webservice-deploy-a-web-service.md)
* [Azure Machine Learning Web サービスをデプロイする](machine-learning-publish-a-machine-learning-web-service.md)
* [チュートリアル手順 5: Azure Machine Learning Web サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)

### Excel から

Excel テンプレートをダウンロードできます。このテンプレートにより Web サービスを簡単に使用できます。

* [Excel からの Azure Machine Learning Web サービスの使用](machine-learning-consuming-from-excel.md)
* [Azure Machine Learning Web サービスの Excel アドイン](machine-learning-excel-add-in-for-web-services.md)


### REST に基づいたクライアントの使用

Azure ML Web サービスは、RESTful API です。この API は、.NET、Python、R、Java など、さまざまなプラットフォームから使用できます。[Microsoft Azure Machine Learning Web サービス ポータル](https://services.azureml.net)の Web サービスの使用に関するページには、開始するときに役立つサンプル コードが用意されています。

* [Machine Learning の実験からデプロイされた Azure Machine Learning Web サービスを使用する方法](machine-learning-consume-web-services.md)

<!---HONumber=AcomDC_0824_2016-->