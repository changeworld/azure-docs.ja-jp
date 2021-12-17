---
title: Azure Virtual Desktop の PowerShell モジュール - Azure
description: Azure Virtual Desktop 用 PowerShell モジュールをインストールして設定する方法について説明します。
author: Heidilohr
ms.topic: how-to
ms.date: 07/20/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 916ce455d2d053746afb37b6b6dcc84168083b61
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464567"
---
# <a name="set-up-the-powershell-module-for-azure-virtual-desktop"></a>Azure Virtual Desktop 用 PowerShell モジュールを設定する

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager との統合で Azure Virtual Desktop に適用されます。

Azure Virtual Desktop PowerShell モジュールは、Azure PowerShell モジュールに統合されています。 この記事では、Azure Virtual Desktop 用コマンドレットを実行できるように PowerShell モジュールを設定する方法について説明します。

## <a name="set-up-your-powershell-environment"></a>PowerShell 環境をセットアップする

モジュールの使用を開始するには、まず、[最新バージョンの PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core) をインストールします。 現在、Azure Virtual Desktop のコマンドレットは PowerShell Core でのみ機能します。

次に、PowerShell セッションで使用するために、DesktopVirtualization モジュールをインストールする必要があります。

モジュールをインストールするには、管理者特権モードで次の PowerShell コマンドレットを実行します。

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> このコマンドレットが動作しない場合は、管理者特権のアクセス許可を使用して、もう一度実行してみてください。

その後、次のコマンドレットを実行して Azure に接続します。

```powershell
Connect-AzAccount
```

>[!IMPORTANT]
>US Gov ポータルに接続する場合は、代わりに次のコマンドレットを実行します。
> 
> ```powershell
> Connect-AzAccount -EnvironmentName AzureUSGovernment
> ```
> 
> Azure China ポータルに接続するには、次のコマンドレットを実行します。
> 
> ```powershell
> Connect-AzAccount -EnvironmentName AzureChinaCloud
> ```

Azure アカウントにサインインするには、Connect コマンドレットを実行するときに生成されるコードが必要です。 サインインするには、<https://microsoft.com/devicelogin> に移動し、コードを入力してから、Azure 管理者の資格情報を使用してサインインします。

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

これにより、管理者の資格情報の既定値であるサブスクリプションに直接サインインします。

## <a name="change-the-default-subscription"></a>既定のサブスクリプションを変更する

サインイン後に既定のサブスクリプションを変更する場合は、次のコマンドレットを実行します。

```powershell
Select-AzSubscription -Subscription <preferredsubscriptionname>
```

次のように、Out-GridView コマンドレットを使用して、一覧から 1 つを選択することもできます。

```powershell
Get-AzSubscription | Out-GridView -PassThru | Select-AzSubscription
```

使用する新しいサブスクリプションを選択する場合は、後で実行するコマンドレットで、そのサブスクリプションの ID を指定する必要はありません。 たとえば、次のコマンドレットは、サブスクリプション ID を必要とせずに、特定のセッション ホストを取得します。

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

また、必要なサブスクリプション名をパラメーターとして追加することで、コマンドレットごとにサブスクリプションを変更することもできます。 次のコマンドレットは前の例と同じですが、コマンドレットで使用するサブスクリプションを変更するためにパラメーターとしてサブスクリプション ID が追加されている点が異なります。

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>場所を取得する

場所パラメーターは、新しいオブジェクトを作成するすべての **New-AzWVD** コマンドレットで必須です。

次のコマンドレットを実行して、自分のサブスクリプションでサポートされている場所の一覧を取得します。

```powershell
Get-AzLocation
```

**Get-AzLocation** の出力は、次のようになります。

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

アカウントの場所を確認したら、コマンドレットで使用できます。 たとえば、"southeastasia" という場所にホスト プールを作成するコマンドレットを次に示します。

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>次のステップ

PowerShell モジュールの設定が完了したので、コマンドレットを実行して Azure Virtual Desktop 内であらゆる処理を実行できます。 モジュールを使用できるいくつかの場所を以下に示します。

- [Azure Virtual Desktop のチュートリアル]()を実行して、まったく独自の Azure Virtual Desktop 環境を設定する。
- [PowerShell を使用してホスト プールを作成する](create-host-pools-powershell.md)
- [Azure Virtual Desktop の負荷分散方法を構成する](configure-host-pool-load-balancing.md)
- [個人用デスクトップ ホスト プールの割り当ての種類を構成する](configure-host-pool-personal-desktop-assignment-type.md)
- その他にもたくさんあります。

問題が発生した場合は、[PowerShell のトラブルシューティングに関する記事](troubleshoot-powershell.md)を参照してください。

