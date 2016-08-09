<properties
	pageTitle="Machine Learning 新機能 | Microsoft Azure"
	description="Azure Machine Learning で利用できる新しい機能。"
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
	ms.date="07/15/2016"
	ms.author="v-donglo"/>

# Azure Machine Learning の新機能

2016年 7 月、Microsoft Azure Machine Learning (ML) の更新プログラムで次の機能が提供されました。

* Web サービスは Azure のリソースとして [Azure Resource Manager](../resource-group-overview.md) インターフェイスから管理されるようになり、次の拡張機能を使用できるようになりました。
	* Resource Manager ベースの Web サービスをデプロイおよび管理するための新しい [REST API](https://msdn.microsoft.com/library/azure/Dn950030.aspx)
	* Web サービスのすべての側面を管理するための 1 つの場所を提供する新しい [Microsoft Azure Machine Learning Web サービス](https://services.azureml.net/) ポータル
* Web サービスの Resource Manager のリソース プロバイダーを活用する Resource Manager ベースの API を使用する、新しい、サブスクリプション ベースの複数リージョン対応 Web サービス デプロイ モデルが組み込まれました。
* 新たに[料金プラン](https://azure.microsoft.com/pricing/details/machine-learning/)と、新しい Resource Manager RP を課金の目的で使用するプラン管理機能が導入されました。
	* [複数のリージョンに Web サービスをデプロイ](machine-learning-how-to-deploy-to-multiple-regions.md)できるようになりました。各リージョンに新しいサブスクリプションを作成する必要はありません。
* Web サービスの[使用状況の統計](machine-learning-manage-new-webservice.md)が提供されるようになりました。
* サンプル データを使用する Azure ML RRS 関数のテストが簡略化されました。
* サンプル データとジョブの送信履歴が表示される新しい BES テスト ページが提供されました。

さらに、Machine Learning studio が更新され、新しい Web サービス モデルをデプロイするか、従来の Web サービス モデルのデプロイを継続できるようになりました。

<!---HONumber=AcomDC_0727_2016-->