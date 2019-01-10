---
title: Azure Stack に使用状況追跡のためのテナントを登録する | Microsoft Docs
description: Azure Stack でテナント登録の管理に使用される操作とテナントの使用状況を追跡する方法についての詳細。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/08/2019
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 6d28eea434b081602f0e2455b22fcc58022a800e
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117115"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Azure Stack でテナントの登録を管理する

*適用対象:Azure Stack 統合システム*

この記事では、登録操作について詳しく説明します。 これらの操作を使用して次のことができます。
- テナントの登録を管理する
- テナントの使用状況の追跡を管理する

テナントのマッピングを追加、一覧表示、または削除する方法についての詳細を確認することができます。 PowerShell または Billing API エンドポイントを使用すると、使用の追跡を管理できます。 テナントのマッピングを追加、一覧表示、または削除する方法についての詳細を確認することができます。 PowerShell または Billing API エンドポイントを使用すると、使用の追跡を管理できます。

## <a name="add-tenant-to-registration"></a>テナントを登録に追加する

新しいテナントを登録に追加するときは、この操作を使用します。 テナントの使用状況は、Azure Active Directory (Azure AD) テナントに接続されている Azure サブスクリプションで報告されます。

テナントに関連付けられているサブスクリプションを変更する場合にもこの操作を使用します。 PUT/New-AzureRMResource を呼び出して、以前のマッピングを上書きします。

テナントと関連付けることができる Azure サブスクリプションは 1 つだけです。 既存のテナントに第 2 のサブスクリプションを追加しようとすると、最初のサブスクリプションは上書きされます。

### <a name="use-api-profiles"></a>API プロファイルの使用

登録コマンドレットでは、PowerShell を実行するときに API プロファイルを指定する必要があります。 API プロファイルは、一連の Azure リソース プロバイダーとその API バージョンを表します。 これにより、複数の Azure クラウドを対話操作するときに、適切なバージョンの API を使用できます。 たとえば、グローバルな Azure と Azure Stack を使用するときは複数のクラウドを扱います。 プロファイルでは、そのリリース日に対応する名前を指定します。 **2017-09-03** プロファイルを使用する必要があります。

Azure Stack と API プロファイルの詳細については、「[Azure Stack での API バージョンのプロファイルの管理](user/azure-stack-version-profiles.md)」を参照してください。 API プロファイルと PowerShell を使用するために必要な手順については、「[Azure Stack での PowerShell による API バージョンのプロファイルの使用](user/azure-stack-version-profiles-powershell.md)」を参照してください。

### <a name="parameters"></a>parameters

| パラメーター                  | 説明 |
|---                         | --- |
| registrationSubscriptionID | 初期登録に使用された Azure サブスクリプション。 |
| customerSubscriptionID     | 登録される顧客の Azure サブスクリプション (Azure Stack ではない)。 パートナー センターを使用してクラウド サービス プロバイダー (CSP) オファーで作成されている必要があります。 顧客が複数のテナントを持っている場合は、Azure Stack にログインするためのテナント用のサブスクリプションを作成します。 |
| resourceGroup              | 登録が格納されている Azure 内のリソース グループ。 |
| registrationName           | Azure Stack の登録名。 Azure にオブジェクトとして格納されています。 通常、名前は azurestack-CloudID の形式で、CloudID は Azure Stack デプロイのクラウド ID です。 |

> [!Note]  
> テナントには、使用する各 Azure Stack を登録する必要があります。 テナントで複数の Azure Stack を使用する場合は、各デプロイの初期登録をテナントのサブスクリプションで更新する必要があります。

### <a name="powershell"></a>PowerShell

New-AzureRmResource コマンドレットを使用して、登録リソースを更新します。 テナントの追加方法の例を次に示します。

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>API 呼び出し

**Operation**:PUT  
**要求 URI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**応答**:201 Created  
**応答本文**:Empty  

## <a name="list-all-registered-tenants"></a>すべての登録済みテナントの一覧

登録に追加されているすべてのテナントの一覧を取得します。

 > [!Note]  
 > テナントが登録されていない場合は、応答を受信しません。

### <a name="parameters"></a>parameters

| パラメーター                  | 説明          |
|---                         | ---                  |
| registrationSubscriptionId | 初期登録に使用された Azure サブスクリプション。   |
| resourceGroup              | 登録が格納されている Azure 内のリソース グループ。    |
| registrationName           | Azure Stack の登録名。 Azure にオブジェクトとして格納されています。 通常、名前は **azurestack**-***CloudID*** の形式で、***CloudID*** は Azure Stack デプロイのクラウド ID です。   |

### <a name="powershell"></a>PowerShell

登録されているすべてのテナントを一覧表示するには、Get-AzureRmResource コマンドレットを使用します。 初期登録に使用したアカウントを使用して Azure (`Add-AzureRmAccount`) にサインインします。 テナントの追加方法の例を次に示します。

```powershell
  Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 呼び出し

GET 操作を使用してすべてのテナント マッピングの一覧を取得することができます

**Operation**:GET  
**要求 URI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**応答**:200  
**応答本文**: 

```JSON  
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}”,
            "name": " cspSubscriptionId 1",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}”,
            "name": " cspSubscriptionId2 ",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>テナント マッピングの削除

登録に追加されているテナントを削除することができます。 そのテナントがまだ Azure Stack のリソースを使用している場合、その使用は Azure Stack の初期登録に使用されたサブスクリプションに課金されます。

### <a name="parameters"></a>parameters

| パラメーター                  | 説明          |
|---                         | ---                  |
| registrationSubscriptionId | 登録用のサブスクリプション ID です。   |
| resourceGroup              | 登録のリソース グループです。   |
| registrationName           | 登録の名前です。  |
| customerSubscriptionId     | 顧客サブスクリプション ID です。  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 呼び出し

DELETE 操作を使用して、テナントのマッピングを削除することができます。

**Operation**:削除  
**要求 URI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**応答**:204 No Content  
**応答本文**:Empty

## <a name="next-steps"></a>次の手順

 - Azure Stack からリソース使用量情報を取得する方法ついて詳しくは、「[Azure Stack での使用量と請求](azure-stack-billing-and-chargeback.md)」をご覧ください。
