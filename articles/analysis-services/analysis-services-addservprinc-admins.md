---
title: Azure Analysis Services の管理者ロールにサービス プリンシパルを追加する | Microsoft Docs
description: Azure Analysis Services のサーバー管理者ロールに自動化サービス プリンシパルを追加する方法について説明します
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: b75740e9bff714ad68c93bea7e387e60da2f1c59
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212499"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>サーバー管理者ロールへのサービス プリンシパルの追加 

 無人の PowerShell タスクを自動化するには、管理対象の Analysis Services サーバー上で、サービス プリンシパルに**サーバー管理者**権限が付与されている必要があります。 この記事では、Azure AS サーバー上のサーバー管理者ロールにサービス プリンシパルを追加する方法について説明します。 これは、SQL Server Management Studio または Resource Manager テンプレートを使用して行うことができます。
 
> [!NOTE]
> Azure PowerShell コマンドレットを使用したサーバー操作の場合、サービス プリンシパルが、[Azure のロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) でリソースの**所有者**ロールに属している必要もあります。 

## <a name="before-you-begin"></a>開始する前に
このタスクを完了するには、Azure Active Directory にサービス プリンシパルが登録されている必要があります。

[サービス プリンシパルを作成する - Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[サービス プリンシパルを作成する - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>SQL Server Management Studio を使用する

サーバー管理は、SQL Server Management Studio (SSMS) を使用して構成することができます。 このタスクを完了するには、Azure AS サーバーに対する[サーバー管理者](analysis-services-server-admins.md)権限が必要です。 

1. SSMS で、目的の Azure AS サーバーに接続します。
2. **[サーバーのプロパティ]**  >  **[セキュリティ]** で、 **[追加]** をクリックします。
3. **[ユーザーまたはグループを選択します]** で、登録されているアプリを名前で検索し、選択した後、 **[追加]** をクリックします。

    ![サービス プリンシパル アカウントの検索](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. サービス プリンシパルのアカウント ID を確認し、 **[OK]** をクリックします。
    
    ![サービス プリンシパル アカウントの検索](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Resource Manager テンプレートの使用

Azure Resource Manager テンプレートを使用して Analysis Services サーバーを展開して、サーバー管理者を構成することも可能です。 この展開を実行する ID は、[Azure ロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) の**共同作成者**ロールに属している必要があります。

> [!IMPORTANT]
> サービス プリンシパルは `app:{service-principal-client-id}@{azure-ad-tenant-id}` の書式を使用して追加します。

次の Resource Manager テンプレートは、Analysis Services の管理者ロールにサービス プリンシパルを指定して Analysis Services サーバーを展開します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="related-information"></a>関連情報

* [SQL Server PowerShell モジュールのダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SSMS のダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


