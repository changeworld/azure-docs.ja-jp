---
title: プロバイダー リソース使用量 API | Microsoft Docs
description: Azure Stack の使用状況情報を取得するリソース使用量 API のリファレンス
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 46e46cfea621f99e150446fcc75b71feb468fa49
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052700"
---
# <a name="provider-resource-usage-api"></a>プロバイダー リソース使用量 API
*プロバイダー*という用語は、サービス管理者と委任されたすべてのプロバイダーに適用されます。 Azure Stack オペレーターおよび委任されたプロバイダーは、プロバイダー使用量 API を使用して、直接のテナントの使用状況を表示できます。 たとえば、図に示したように、P0 はプロバイダー API を呼び出して、P1 と P2 の直接の使用状況に関する情報を取得でき、P1 は P3 と P4 の使用状況情報を呼び出すことができます。

![プロバイダー階層の概念モデル](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>API 呼び出しリファレンス
### <a name="request"></a>要求
要求は、要求されたサブスクリプションと要求された期間の使用の詳細を取得します。 要求の本文はありません。

この使用状況 API はプロバイダー API であるため、呼び出し元に、プロバイダーのサブスクリプションの所有者、共同作成者、またはリーダーの役割が割り当てられている必要があります。

| **メソッド** | **要求 URI** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>引数
| **引数** | **説明** |
| --- | --- |
| *armendpoint* |Azure Stack 環境の Azure Resource Manager エンドポイント。 Azure Stack 規則は、Azure Resource Manager エンドポイントの名前が、`https://adminmanagement.{domain-name}` の形式であることです。 たとえば、開発キットでは、ドメイン名が *local.azurestack.external* の場合、Resource Manager エンドポイントは `https://adminmanagement.local.azurestack.external` になります。 |
| *subId* |呼び出しを行っているユーザーのサブスクリプション ID。 |
| *reportedStartTime* |クエリの開始時間。 *DateTime* の値は世界協定時刻 (UTC) で、13:00 など、毎時 0 分に設定する必要があります。 毎日の集計では、この値を UTC の午前 0 時に設定します。 形式は*エスケープ*ISO 8601 形式です。 たとえば、*2015-06-16T18%3a53%3a11%2b00%3a00Z* などで、URI フレンドリになるように、コロンは *%3a* にエスケープされ、プラスは *%2b* にエスケープされます。 |
| *reportedEndTime* |クエリの終了時間。 *reportedStartTime* に適用される制約は、この引数にも適用されます。 *reportedEndTime* の値は将来または現在の日付に設定することはできません。 そうすると、結果は "処理が未完了" に設定されます。 |
| *aggregationGranularity* |daily と hourly の 2 つの個別の指定可能な値を持つ省略可能なパラメーター。 値が示すように、一方は日単位の粒度でデータを返し、もう一方は時間単位の解像度です。 daily オプションが、既定値です。 |
| *subscriberId* |[サブスクリプション ID] が表示されます。 フィルター処理されたデータを取得するには、プロバイダーの直接のテナントのサブスクリプション ID が必要です。 サブスクリプション ID パラメーターが指定されていない場合、呼び出しは、すべてのプロバイダーの直接のテナントの使用状況データを返します。 |
| *api-version* |この要求を行うために使用するプロトコルのバージョン。 この値は *2015-06-01-preview* に設定されます。 |
| *continuationToken* |使用状況 API プロバイダーへの最後の呼び出しから取得されたトークン。 このトークンは、応答が 1,000 行より大きい場合に必要であり、進行状況のブックマークとして機能します。 トークンが存在しない場合、渡された単位に基づいて、日または時間の開始点から、データが取得されます。 |

### <a name="response"></a>Response
GET /subscriptions/sub1/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&subscriberId=sub1.1&api-version=1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce.Admin/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce.Admin/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

…
```

### <a name="response-details"></a>応答の詳細
| **引数** | **説明** |
| --- | --- |
| *id* |使用状況集計の一意の ID |
| *name* |使用状況集計の名前 |
| *type* |リソース定義 |
| *subscriptionId* |Azure Stack ユーザーのサブスクリプション識別子 |
| *usageStartTime* |この使用状況集計が属する使用状況バケットの UTC 開始時間|
| *usageEndTime* |この使用状況集計が属する使用状況バケットの UTC 終了時間 |
| *instanceData* |インスタンスの詳細のキーと値のペア (新しい形式)。<br> *resourceUri*: 完全修飾リソース ID。リソース グループとインスタンス名が含まれます <br> *location*: このサービスが実行されたリージョン <br> *tags*: ユーザーによって指定されたリソース タグ <br> *additionalInfo*: OS のバージョンやイメージの種類など、使用されたリソースの詳細 |
| *quantity* |この期間に発生したリソース使用量 |
| *meterId* |使用されたリソースの一意の ID (*ResourceID* とも呼ばれます) |


## <a name="retrieve-usage-information"></a>使用量情報を取得する

使用量データを生成するには、実行されていて、システムをアクティブに使っているリソース (アクティブな仮想マシンや、データを格納しているストレージ アカウントなど) が必要です。Azure Stack Marketplace で実行されているリソースがあるかどうか不明な場合は、仮想マシン (VM) をデプロイし、実行されているかどうか仮想マシン監視ブレードを確認します。 使用量データを表示するには、次の PowerShell コマンドレットを使います。

1. [PowerShell for Azure Stack をインストールします。](azure-stack-powershell-install.md)
2. [Azure Stack ユーザー](user/azure-stack-powershell-configure-user.md)または [Azure Stack オペレーター](azure-stack-powershell-configure-admin.md)の PowerShell 環境を構成します 
3. 使用量データを取得するには、[Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) PowerShell コマンドレットを使います。
```powershell
Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
```

## <a name="next-steps"></a>次の手順
[テナント リソース使用量 API リファレンス](azure-stack-tenant-resource-usage-api.md)

[使用量に関するよくあるご質問 (FAQ)](azure-stack-usage-related-faq.md)
