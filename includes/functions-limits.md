---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 91f72117fdbcdbeda1d906a9760243e66404920c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104589"
---
| リソース |[従量課金プラン](../articles/azure-functions/consumption-plan.md)|[Premium プラン](../articles/azure-functions/functions-premium-plan.md)|[専用プラン](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|既定の[タイムアウトまでの時間](../articles/azure-functions/functions-scale.md#timeout) (分) |5 | 30 |30<sup>1</sup> | 30 | 30 |
|最大の[タイムアウトまでの時間](../articles/azure-functions/functions-scale.md#timeout) (分) |10 | 無制限<sup>7</sup> | 無制限<sup>2</sup> | unbounded | unbounded |
| 最大送信接続数 (インスタンスあたり) | アクティブ 600 (合計 1200) | unbounded | unbounded | unbounded | unbounded |
| 最大要求サイズ (MB)<sup>3</sup> | 100 | 100 | 100 | 100 | クラスターに応じる |
| クエリ文字列の最大長<sup>3</sup> | 4096 | 4096 | 4096 | 4096 | クラスターに応じる |
| 要求 URL の最大長<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | クラスターに応じる |
|インスタンスあたりの [ACU](../articles/virtual-machines/acu.md) | 100 | 210 ～ 840 | 100 ～ 840 | 210 から 250<sup>8</sup> | [AKS の価格](https://azure.microsoft.com/pricing/details/container-service/) |
| 最大メモリ (インスタンスあたりの GB) | 1.5 | 3.5 ～ 14 | 1.75 ～ 14 | 3.5 から 14 | すべてのノードがサポートされています |
| 最大インスタンス数 | 200 | 100<sup>9</sup> | SKU により異なる<sup>10</sup> | 100<sup>10</sup> | クラスターに応じる |   
| プランあたりの関数アプリ |100 |100 |無制限<sup>4</sup> | unbounded | unbounded |
| [App Service プラン](../articles/app-service/overview-hosting-plans.md) | [リージョン](https://azure.microsoft.com/global-infrastructure/regions/)あたり 100 |リソース グループあたり 100 |リソース グループあたり 100 | - | - |
| ストレージ<sup>5</sup> |5 TB |250 GB |50 ～ 1000 GB | 1 TB (テラバイト) | 該当なし |
| アプリケーションごとのカスタム ドメイン数</a> |500<sup>6</sup> |500 |500 | 500 | 該当なし |
| カスタム ドメインの [SSL サポート](../articles/app-service/configure-ssl-bindings.md) |無制限の SNI SSL 接続が含まれる | 無制限の SNI SSL 接続と 1 件の IP SSL 接続が含まれる |無制限の SNI SSL 接続と 1 件の IP SSL 接続が含まれる | 無制限の SNI SSL 接続と 1 件の IP SSL 接続が含まれる | 該当なし |

<sup>1</sup> 既定では、App Service プランでの Functions 1.x ランタイムのタイムアウトは無制限です。  
<sup>2</sup> App Service プランが [Always On](../articles/azure-functions/dedicated-plan.md#always-on) に設定されている必要があります。 標準[料金](https://azure.microsoft.com/pricing/details/app-service/)でのお支払い。  
<sup>3</sup> これらの制限は[ホストで設定](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config)されます。  
<sup>4</sup> 実際にホストできる関数アプリの数は、アプリのアクティビティ、マシン インスタンスのサイズ、対応するリソース使用量によって異なります。  
<sup>5</sup> ストレージの上限は、同じ App Service プランのすべてのアプリにまたがる一時ストレージ内の合計コンテンツ サイズです。 従量課金プランでは、Azure Files を一時ストレージに使用します。  
<sup>6</sup> 関数アプリが[従量課金プラン](../articles/azure-functions/consumption-plan.md)でホストされている場合、CNAME オプションのみがサポートされます。 [Premium プラン](../articles/azure-functions/functions-premium-plan.md)または [App Service プラン](../articles/azure-functions/dedicated-plan.md)の関数アプリでは、CNAME または A レコードを使用してカスタム ドメインをマップできます。  
<sup>7</sup> 60 分まで保証されます。  
<sup>8</sup> ワーカーは、お客様のアプリをホストする役割です。 ワーカーは、3 つの固定サイズで使用できます。1 vCPU/3.5 GB RAM。2 vCPU/7 GB RAM。4 vCPU/14 GB RAM。   
<sup>9</sup> Premium プランの Linux で実行する場合、インスタンス数の上限は現在 20 個です。  
<sup>10</sup> 詳細については、「[App Service の制限](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)」を参照してください。