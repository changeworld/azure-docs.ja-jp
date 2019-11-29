---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: eb61bf5734d0aea1534735b23a2b95b52000f5ad
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322904"
---
| リソース | [従量課金プラン](../articles/azure-functions/functions-scale.md#consumption-plan) | [Premium プラン](../articles/azure-functions/functions-scale.md#premium-plan) | [App Service プラン](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| スケールアウト | イベント ドリブン | イベント ドリブン | [手動/自動スケール](../articles/app-service/manage-scale-up.md) | 
| 最大インスタンス数 | 200 | 100 | 10 - 20 |
|既定の[タイムアウトまでの時間](../articles/azure-functions/functions-scale.md#timeout) (分) |5 | 30 |30<sup>2</sup> |
|最大の[タイムアウトまでの時間](../articles/azure-functions/functions-scale.md#timeout) (分) |10 | 60 | 無制限<sup>3</sup> |
| 最大送信接続数 (インスタンスあたり) | アクティブ 600 (合計 1200) | unbounded | unbounded |
| 最大要求サイズ (MB)<sup>4</sup> | 100 | 100 | 100 |
| クエリ文字列の最大長<sup>4</sup> | 4096 | 4096 | 4096 |
| 要求 URL の最大長<sup>4</sup> | 8192 | 8192 | 8192 |
| インスタンスあたりの [ACU](../articles/virtual-machines/windows/acu.md) | 100 | 210 ～ 840 | 100 ～ 840 |
| 最大メモリ (インスタンスあたりの GB) | 1.5 | 3.5 ～ 14 | 1.75 ～ 14 |
| プランあたりの関数アプリ |100 |100 |無制限<sup>5</sup> |
| [App Service プラン](../articles/app-service/overview-hosting-plans.md) | [リージョン](https://azure.microsoft.com/global-infrastructure/regions/)あたり 100 |リソース グループあたり 100 |リソース グループあたり 100 |
| ストレージ<sup>6</sup> |1 GB |250 GB |50 ～ 1000 GB |
| アプリケーションごとのカスタム ドメイン数</a> |500<sup>7</sup> |500 |500 |
| カスタム ドメインの [SSL サポート](../articles/app-service/configure-ssl-bindings.md) |無制限の SNI SSL 接続が含まれる | 無制限の SNI SSL 接続と 1 件の IP SSL 接続が含まれる |無制限の SNI SSL 接続と 1 件の IP SSL 接続が含まれる | 

<sup>1</sup> 各種 App Service プラン オプションに固有の制限については、[App Service プランの制限](../articles/azure-subscription-service-limits.md#app-service-limits)に関する記事を参照してください。  
<sup>2</sup> 既定では、App Service プランでの Functions 1.x ランタイムのタイムアウトは無制限です。  
<sup>3</sup> App Service プランが [Always On](../articles/azure-functions/functions-scale.md#always-on) に設定されている必要があります。 標準[料金](https://azure.microsoft.com/pricing/details/app-service/)でのお支払い。  
<sup>4</sup> これらの制限は[ホストで設定](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config)されます。  
<sup>5</sup> 実際にホストできる関数アプリの数は、アプリのアクティビティ、マシン インスタンスのサイズ、対応するリソース使用量によって異なります。  
<sup>6</sup> ストレージの上限は、同じ App Service プランのすべてのアプリにまたがる一時ストレージ内の合計コンテンツ サイズです。 従量課金プランでは、Azure Files を一時ストレージに使用します。  
<sup>7</sup> 関数アプリが[従量課金プラン](../articles/azure-functions/functions-scale.md#consumption-plan)でホストされている場合、CNAME オプションのみがサポートされます。 [Premium プラン](../articles/azure-functions/functions-scale.md#premium-plan)または [App Service プラン](../articles/azure-functions/functions-scale.md#app-service-plan)の関数アプリでは、CNAME または A レコードを使用してカスタム ドメインをマップできます。
