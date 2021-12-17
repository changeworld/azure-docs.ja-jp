---
title: Azure Virtual Desktop のアプリ グループを管理する - Azure
description: PowerShell または Azure CLI を使用して Azure Virtual Desktop アプリ グループを管理する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 07/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: f323819492fe89f7742c6b218afa4d2e1bf1b6c0
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123103931"
---
# <a name="manage-app-groups-using-powershell-or-the-azure-cli"></a>PowerShell または Azure CLI を使用してアプリ グループを管理する

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトを含む Azure Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを含まない Azure Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/manage-app-groups-2019.md)を参照してください。

Azure Virtual Desktop の新しいホスト プール向けに作成される既定のアプリ グループには、完全なデスクトップも公開されています。 加えて、ホスト プールには RemoteApp アプリケーション グループ (複数可) を作成することができます。 このチュートリアルに沿って作業すれば、RemoteApp アプリ グループを作成して、独自の **[スタート]** メニュー アプリを公開することができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * RemoteApp グループを作成する。
> * RemoteApp プログラムへのアクセスを許可する。

## <a name="prerequisites"></a>前提条件

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

この記事では、[PowerShell モジュールの設定](powershell-module.md)に関する記事に記載されている手順に従って PowerShell モジュールを設定し、Azure アカウントにサインインしていることを前提としています。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

この記事では、Azure CLI 用に環境を既に設定し、Azure アカウントにサインイン済みであることを前提としています。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

---

## <a name="create-a-remoteapp-group"></a>RemoteApp グループを作成する

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

PowerShell を使用して RemoteApp グループを作成するには:

1. 次の PowerShell コマンドレットを実行して、新しい空の RemoteApp アプリ グループを作成します。

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. (省略可) アプリ グループが作成されたことを確認したければ、次のコマンドレットを実行すると、ホスト プールのすべてのアプリ グループが一覧表示されます。

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. 次のコマンドレットを実行して、ホスト プールの仮想マシン イメージにある **[スタート]** メニュー アプリの一覧を取得します。 **FilePath**、**IconPath**、**IconIndex** など、公開したいアプリケーションの重要な情報の値を書き留めます。

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more
   ```

   出力には、すべての [スタート] メニュー項目が次のような形式で表示されます。

   ```powershell
   AppAlias            : access
   CommandLineArgument :
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access
   Name                : 0301RAG/Access
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems

   AppAlias            : charactermap
   CommandLineArgument :
   FilePath            : C:\windows\system32\charmap.exe
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\windows\system32\charmap.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map
   Name                : 0301RAG/Character Map
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   ```

4. 次のコマンドレットを実行して、`AppAlias` に基づくアプリケーションをインストールします。 `AppAlias` は、手順 3. の出力を実行すると利用できるようになります。

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

5. (省略可) 次のコマンドレットを実行して、手順 1. で作成したアプリケーション グループに新しい RemoteApp プログラムを発行します。

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

6. アプリが公開されたことを確認するために、次のコマンドレットを実行します。

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. このアプリ グループに発行するアプリケーションごとに手順 1. から手順 5. を繰り返します。
8. 次のコマンドレットを実行して、アプリ グループ内の RemoteApp プログラムへのアクセスをユーザーに許可します。

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Azure CLI には、スタート メニュー アプリを取得するコマンド、新しい RemoteApp プログラムを作成するコマンド、アプリケーション グループにそれを発行するコマンドは現在提供されていません。 Azure PowerShell を使用する。

Azure CLI を使用して RemoteApp グループを作成するには、次の手順を実行します。

1. [az desktopvirtualization applicationgroup create](/cli/azure/desktopvirtualization##az_desktopvirtualization_applicationgroup_create) コマンドを使用して、新しいリモート アプリケーション グループを作成します。

   ```azurecli
   az desktopvirtualization applicationgroup create --name "MyApplicationGroup" \
      --resource-group "MyResourceGroup" \
      --location "MyLocation" \
      --application-group-type "RemoteApp" \
      --host-pool-arm-path "/subscriptions/MySubscriptionGUID/resourceGroups/MyResourceGroup/providers/Microsoft.DesktopVirtualization/hostpools/MyHostPool"
      --tags tag1="value1" tag2="value2" \
      --friendly-name "Friendly name of this application group" \
      --description "Description of this application group" 
   ```
    
2. (省略可) アプリ グループが作成されたことを確認したければ、次のコマンドを実行すると、ホスト プールのすべてのアプリ グループが一覧表示されます。

   ```azurecli
   az desktopvirtualization applicationgroup list \
      --resource-group "MyResourceGroup"
   ```
---

## <a name="next-steps"></a>次のステップ

Microsoft のチュートリアルからこのハウツー ガイドに移動してきた場合は、[サービスの更新プログラムを検証するためのホスト プールの作成](create-validation-host-pool.md)に関する記事を参照してください。 運用環境に展開する前に、検証ホスト プールを使用してサービスの更新プログラムを監視できます。