---
title: Windows Virtual Desktop の PowerShell モジュール - Azure
description: Windows Virtual Desktop 用の PowerShell モジュールをインストールして設定する方法について説明します。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd854691203361847ae9a6c873121c9b66820a90
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743369"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>Windows Virtual Desktop 用 PowerShell モジュールを設定する

>[!IMPORTANT]
>このコンテンツは、Spring 2020 更新プログラムと Azure Resource Manager Windows Virtual Desktop オブジェクトの組み合わせを対象としています。
>
> Windows Virtual Desktop Spring 2020 更新プログラムは現在、パブリック プレビュー段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Windows Virtual Desktop PowerShell モジュールは、Azure PowerShell モジュールに統合されています。 この記事では、Windows Virtual Desktop のコマンドレットを実行できるように PowerShell モジュールを設定する方法について説明します。

## <a name="set-up-your-powershell-environment"></a>PowerShell 環境をセットアップする

モジュールの使用を開始するには、まず、[最新バージョンの PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core) をインストールします。 現在、Windows Virtual Desktop のコマンドレットは PowerShell Core でのみ機能します。

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

PowerShell モジュールの設定が完了したので、コマンドレットを実行して Windows Virtual Desktop であらゆる処理を実行できます。 モジュールを使用できるいくつかの場所を以下に示します。

- まったく独自の Windows Virtual Desktop 環境を設定するには、[Windows Virtual Desktop のチュートリアル]()を実行してください。
- [PowerShell を使用してホスト プールを作成する](create-host-pools-powershell.md)
- [Windows Virtual Desktop の負荷分散方法を構成する](configure-host-pool-load-balancing.md)
- [個人用デスクトップ ホスト プールの割り当ての種類を構成する](configure-host-pool-personal-desktop-assignment-type.md)
- その他にもたくさんあります。

問題が発生した場合は、[PowerShell のトラブルシューティングに関する記事](troubleshoot-powershell.md)を参照してください。

