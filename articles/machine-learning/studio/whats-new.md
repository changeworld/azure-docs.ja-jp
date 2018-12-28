---
title: 新機能 - Azure Machine Learning Studio | Microsoft Docs
description: Azure Machine Learning Studio で利用できる新しい機能。
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2018
ms.openlocfilehash: 90a9a81de488f2b3b256e3164c3bfde3019d0ad3
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53253019"
---
# <a name="whats-new-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio の新機能

## <a name="october-2018"></a>2018 年 10 月

[R スクリプトの実行](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script)モジュールの R 言語エンジンで、新しい R ランタイム バージョンである Microsoft R Open (MRO) 3.4.4 が追加されました。 MRO 3.4.4 はオープン ソースの CRAN R 3.4.4 に基づいているため、その R のバージョンで動作するパッケージと互換性があります。サポートされている R パッケージの詳細については、「[R Packages supported by Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning#bkmk_List)」(Azure Machine Learning Studio でサポートされている R パッケージ) を参照してください。

## <a name="march-2017"></a>2017 年 3 月 
この Microsoft Azure Machine Learning の更新プログラムのリリースで次の機能が提供されました。

* Azure Machine Learning BES ジョブ専用の容量

    Machine Learning の Batch プール処理では、[Azure Batch](../../batch/batch-technical-overview.md) サービスを使用して、Azure Machine Learning バッチ実行サービスのスケールをユーザーが管理できるようにしています。 Batch プール処理では、バッチ ジョブを送信できる Azure Batch プールを作成し、予測可能な方法で実行することができます。

    詳細については、「[Machine Learning ジョブ用の Azure Batch サービス](dedicated-capacity-for-bes-jobs.md)」を参照してください。


## <a name="august-2016"></a>2016 年 8 月 
この Microsoft Azure Machine Learning の更新プログラムのリリースで次の機能が提供されました。
* クラシック Web サービスを、新しい [Microsoft Azure Machine Learning Web サービス](https://services.azureml.net/) ポータル (Web サービスのすべての側面を管理するための 1 つの場所) で管理できるようになりました。    
  * Web サービスの [使用状況の統計](manage-new-webservice.md)が提供されます。
  * サンプル データを使用して Azure Machine Learning リモート要求呼び出しのテストが簡略化されます。
  * サンプル データとジョブの送信履歴が表示される新しいバッチ実行サービス テスト ページが提供されます。
  * エンドポイントの管理が容易になります。

## <a name="july-2016"></a>2016 年 7 月 
この Microsoft Azure Machine Learning の更新プログラムのリリースで次の機能が提供されました。
* Web サービスは Azure のリソースとして [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) インターフェイスから管理されるようになり、次の拡張機能を使用できるようになりました。
  * Resource Manager ベースの Web サービスをデプロイおよび管理するための新しい [REST API](https://msdn.microsoft.com/library/azure/Dn950030.aspx)
  * Web サービスのすべての側面を管理するための 1 つの場所を提供する新しい [Microsoft Azure Machine Learning Web サービス](https://services.azureml.net/) ポータル
* Web サービスの Resource Manager のリソース プロバイダーを活用する Resource Manager ベースの API を使用する、新しい、サブスクリプション ベースの複数リージョン対応 Web サービス デプロイ モデルが組み込まれました。
* 新たに [料金プラン](https://azure.microsoft.com/pricing/details/machine-learning/) と、新しい Resource Manager RP を課金の目的で使用するプラン管理機能が導入されました。
  * 各リージョンにサブスクリプションを作成せずに、[Web サービスを複数のリージョンにデプロイ](how-to-deploy-to-multiple-regions.md)できるようになりました。
* Web サービスの [使用状況の統計](manage-new-webservice.md)が提供されるようになりました。
* サンプル データを使用して Azure Machine Learning リモート要求呼び出しのテストが簡略化されます。
* サンプル データとジョブの送信履歴が表示される新しいバッチ実行サービス テスト ページが提供されます。

さらに、Machine Learning Studio が更新され、新しい Web サービス モデルをデプロイするか、従来の Web サービス モデルのデプロイを継続できるようになりました。 

