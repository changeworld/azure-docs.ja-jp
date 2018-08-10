---
title: 管理グループを作成して Azure リソースを整理する | Microsoft Docs
description: Azure 管理グループを作成して複数のリソースを管理する方法を説明します。
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2018
ms.author: rithorn
ms.openlocfilehash: 13eea4f2b5125d7ab25b05a9e20021ebc6a2e0c6
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435375"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>リソースの整理と管理のための管理グループを作成する

管理グループは、複数のサブスクリプションのアクセス、ポリシー、コンプライアンスを管理するのに役立つコンテナーです。 これらのコンテナーを作成して、[Azure Policy](../azure-policy/azure-policy-introduction.md) と [Azure ロール ベースのアクセス制御](../role-based-access-control/overview.md)で使用できる効果的で効率的な階層を構築します。 管理グループについて詳しくは、「[Organize your resources with Azure management groups](management-groups-overview.md)」(Azure 管理グループでリソースを整理する) をご覧ください。

ディレクトリに作成される最初の管理グループは、完了までに最大 15 分かかる場合があります。 Azure 内でディレクトリの管理グループ サービスを初めて設定する際に実行するプロセスがあります。 プロセスが完了すると、通知を受け取ります。  

## <a name="create-a-management-group"></a>管理グループの作成

管理グループを作成するには、ポータル、PowerShell、または Azure CLI を使用します。

### <a name="create-in-portal"></a>ポータルで作成する

1. [Azure Portal](http://portal.azure.com) にログインします。
2. **[すべてのサービス]** > **[管理グループ]** を選択します。
3. メイン ページで、**[New Management group]\(新しい管理グループ\)** を選択します。

    ![メイン グループ](media/management-groups/main.png)
4.  [管理グループ ID] フィールドに入力します。
    - **[管理グループ ID]** は、この管理グループでコマンドを送信するために使用するディレクトリの一意識別子です。 この識別子は、このグループを識別するために Azure システム全体で使用されるため、作成後は編集できません。
    - 表示名フィールドは、Azure Portal 内で表示される名前です。 管理グループの作成時には別の表示名は省略可能なフィールドで、いつでも変更できます。  

    ![Create](media/management-groups/create_context_menu.png)  
5.  **[保存]** を選びます。

### <a name="create-in-powershell"></a>PowerShell で作成する

PowerShell で、Add-AzureRmManagementGroups コマンドレットを使用します。

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso
```

**GroupName** は、作成される一意識別子です。 この ID は、このグループを参照するために他のコマンドで使用され、後で変更することはできません。

Azure Portal 内で管理グループを別の名前で表示する場合は、**DisplayName** パラメーターを文字列と共に追加します。 たとえば、Contoso という GroupName と "Contoso Group" という表示名を持つ管理グループを作成する場合は、次のコマンドレットを使用します。

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso -DisplayName "Contoso Group" -ParentId ContosoTenant
```

この管理グループを別の管理の下に作成するには **ParentId** パラメーターを使用します。  

### <a name="create-in-azure-cli"></a>Azure CLI で作成する

Azure CLI で、az account management-group create コマンドを使用します。

```azure-cli
C:\ az account management-group create --group-name <YourGroupName>
```

---

## <a name="next-steps"></a>次の手順 
管理グループについて詳しくは、以下をご覧ください。 
- [Azure 管理グループでリソースを整理する](management-groups-overview.md)
- [管理グループを変更、削除、または管理する方法](management-groups-manage.md)
- [Azure PowerShell モジュールのインストール](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [REST API 仕様の確認](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Azure CLI 拡張機能のインストール](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)
