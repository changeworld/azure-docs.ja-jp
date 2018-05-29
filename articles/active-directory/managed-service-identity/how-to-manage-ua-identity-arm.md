---
title: Azure Resource Manager を使用してユーザー割り当てのマネージド サービス ID を作成および削除する方法
description: Azure Resource Manager を使って、ユーザーによって割り当てられたマネージド サービス ID (MSI) を作成および削除する手順を説明します。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: e5c5ff74ee94f8df03ceb5b469ad635bd80d5a11
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33931029"
---
# <a name="create-list-and-delete-a-user-assigned-identity-using-azure-resource-manager"></a>Azure Resource Manager を使用して、ユーザー割り当て ID を作成、一覧表示、削除する

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

管理対象サービス ID は、Azure Active Directory の管理対象 ID を Azure サービスに提供します。 この ID を使用すると、コード内に資格情報を埋め込む必要なく、Azure AD の認証をサポートするサービスに認証することができます。 

この記事では、Azure Resource Manager を使ってユーザー割り当てのマネージド ID を作成します。

Azure Resource Manager テンプレートを使って、ユーザー割り当て ID を一覧表示および削除することはできません。  ユーザー割り当て ID の作成と一覧表示については、次の記事をご覧ください。

- [ユーザー割り当て ID を一覧表示する](how-to-manage-ua-identity-cli.md#list-user-assigned-identities)
- [ユーザー割り当て ID を削除する](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-identity)
## <a name="prerequisites"></a>前提条件

- マネージド サービス ID の基本についてご不明な点がある場合は、[管理対象のサービス ID の概要](overview.md)に関するページを参照してください。 **[システム割り当て ID とユーザー割り当て ID の違い](overview.md#how-does-it-work)を確認してください**。
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。

Azure にローカルでサインインする場合も、Azure Portal を使用してサインインする場合も、VM が含まれる Azure サブスクリプションに関連付けられているアカウントを使用します。 また、お使いのアカウントが、「仮想マシンの共同作業者」などのVM 上の書き込みアクセス許可が提供されるロールに属すようにします。

## <a name="template-creation-and-editing"></a>テンプレートの作成と編集

Azure Portal とスクリプトの場合と同じように、Azure Resource Manager テンプレートは、Azure リソース グループによって定義された新しいリソースまたは変更されたリソースをデプロイする機能を提供します。 ローカルとポータル ベースの両方を含むテンプレートの編集やデプロイでは、次のような複数のオプションが使用できます。

- [Azure Marketplace のカスタム テンプレート](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)を使用します。これにより、最初からテンプレートを作成したり、既存の共通テンプレートまたは[クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)に基づいてテンプレートを作成したりできます。
- [元のデプロイ](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history)または[デプロイの現在の状態](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)からテンプレートをエクスポートすることによって、既存のリソース グループから派生させます。
- ローカルの [JSON エディター (VS Code など)](../../azure-resource-manager/resource-manager-create-first-template.md) を使用してから、PowerShell または CLI を使用してアップロードおよびデプロイします。
- Visual Studio の [Azure リソース グループ プロジェクト](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)を使用して、テンプレートを作成およびデプロイします。 

## <a name="create-a-user-assigned-identity"></a>ユーザー割り当て ID を作成する 

ユーザー割り当て ID を作成するには、次のテンプレートを使います。 `<USER ASSIGNED IDENTITY NAME>` は実際の値に置き換えます。

> [!IMPORTANT]
> ユーザー割り当て ID の作成には、英数字およびハイフン (0-9、a-z、A-Z、-) 文字のみがサポートされます。 さらに、VM/VMSS への割り当てが適切に動作するためには、名前の長さは 24 文字以下にする必要があります。 アップデートは後ほどご確認ください。 詳しくは、「[FAQ と既知の問題](known-issues.md)」をご覧ください。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('<USER ASSIGNED IDENTITY NAME>')]",
      "apiVersion": "2015-08-31-PREVIEW",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('<USER ASSIGNED IDENTITY NAME>')]"
      }
  }
}
```
## <a name="related-content"></a>関連コンテンツ

Azure Resource Manager テンプレートを使って Azure VM にユーザー割り当て ID を割り当てる方法については、「[テンプレートを使用して VM マネージド サービス ID (MSI) を構成する](qs-configure-template-windows-vm.md)」をご覧ください。


 
