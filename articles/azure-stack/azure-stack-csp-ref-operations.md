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
ms.date: 06/08/2018
ms.author: brenduns
ms.reviewer: alfredo
ms.openlocfilehash: efd1c1eafbff8bf56b16131e44cff6b03ce7338a
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39264812"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Azure Stack でテナントの登録を管理する

*適用対象: Azure Stack 統合システム*

この記事では、テナント登録の管理に使用できる操作と、テナントの使用状況を追跡する方法について説明します。 テナントのマッピングを追加、一覧表示、または削除する方法についての詳細を確認することができます。 PowerShell または Billing API エンドポイントを使用すると、使用の追跡を管理できます。

## <a name="add-tenant-to-registration"></a>テナントを登録に追加する

この操作は、自分の登録に新しいテナントを追加して、Azure Active Directory (Azure AD) テナントに接続された Azure サブスクリプションで使用状況がレポートされるようにする場合に使用します。

テナントに関連付けられているサブスクリプションを変更する場合にこの操作を使用し、PUT/New-AzureRMResource をもう一度呼び出すこともできます。 以前のマッピングは上書きされます。

テナントには 1 つの Azure サブスクリプションのみを関連付けできます。 既存のテナントに第 2 のサブスクリプションを追加しようとすると、最初のサブスクリプションは上書きされます。 

### <a name="use-api-profiles"></a>API プロファイルの使用

この記事で取り上げるコマンドレットは、PowerShell を実行する際に API プロファイルを指定する必要があります。 API プロファイルは、一連の Azure リソース プロバイダーとその API バージョンを表します。 世界各国の Azure と Azure Stack を扱うときなど、複数の Azure クラウドを対話操作する際は、プロファイルを指定することにより適切なバージョンの API を使うことができます。 プロファイルは、その公開日に対応する名前で指定します。 この記事では、**2017-09-03** プロファイルを使用する必要があります。

Azure Stack と API プロファイルの詳細については、「[Azure Stack での API バージョンのプロファイルの管理](user/azure-stack-version-profiles.md)」を参照してください。 API プロファイルと PowerShell を使用するために必要な手順については、「[Azure Stack での PowerShell による API バージョンのプロファイルの使用](user/azure-stack-version-profiles-powershell.md)」を参照してください。

### <a name="parameters"></a>parameters

| パラメーター                  | 説明 |
|---                         | --- |
| registrationSubscriptionID | 初期登録に使用された Azure サブスクリプション。 |
| customerSubscriptionID     | 登録される顧客の Azure サブスクリプション (Azure Stack ではない)。 クラウド サービス プロバイダー (CSP) プランで作成されている必要があります。 実際には、パートナー センターを介することを意味します。 顧客が 1 つ以上のテナントを持っている場合は、Azure Stack へのログインに使用されるテナントでこのサブスクリプションを作成する必要があります。 |
| resourceGroup              | 登録が格納されている Azure 内のリソース グループ。 |
| registrationName           | Azure Stack の登録名。 Azure にオブジェクトとして格納されています。 通常、名前は azurestack-CloudID の形式で、CloudID は Azure Stack デプロイのクラウド ID です。 |

> [!Note]  
> テナントには、使用する各 Azure Stack を登録する必要があります。 テナントで複数の Azure Stack を使用する場合は、各デプロイの初期登録をテナントのサブスクリプションで更新する必要があります。

### <a name="powershell"></a>PowerShell

New-AzureRmResource コマンドレットを使用して、登録リソースを更新します。 初期登録に使用したアカウントを使用して Azure (`Add-AzureRmAccount`) にサインインします。 テナントの追加方法の例を次に示します。

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>API 呼び出し

**操作**: PUT  
**要求 URI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**応答**: 201 Created  
**応答本文**: 空  

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

登録されているすべてのテナントを一覧表示するには、Get-AzureRmResovurce コマンドレットを使用します。 初期登録に使用したアカウントを使用して Azure (`Add-AzureRmAccount`) にサインインします。 テナントの追加方法の例を次に示します。

```powershell
  Get-AzureRmResovurce -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 呼び出し

GET 操作を使用してすべてのテナント マッピングの一覧を取得することができます

**操作**: GET  
**要求 URI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**応答**: 200  
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

**操作**: DELETE  
**要求 URI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**応答**: 204 No Content  
**応答本文**: 空

## <a name="next-steps"></a>次の手順

 - Azure Stack からリソース使用量情報を取得する方法ついて詳しくは、「[Azure Stack での使用量と請求](/azure-stack-billing-and-chargeback.md)」をご覧ください。
