---
title: Azure Virtual Desktop ホスト プールを作成する - Azure
description: PowerShell または Azure CLI を使用して Azure Virtual Desktop にホスト プールを作成する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 07/23/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: dbd48f8ff2b3da5cec432f6b1fba7d272621535c
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123100830"
---
# <a name="create-an-azure-virtual-desktop-host-pool-with-powershell-or-the-azure-cli"></a>PowerShell または Azure CLI を使用して Azure Virtual Desktop ホスト プールを作成する

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトを含む Azure Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを含まない Azure Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md)を参照してください。

ホスト プールは、Azure Virtual Desktop テナント環境内にある 1 つ以上の同一の仮想マシンをまとめたものです。 各ホスト プールは、複数の RemoteApp グループ、1 つのデスクトップ アプリ グループ、および複数のセッション ホストに関連付けることができます。

## <a name="create-a-host-pool"></a>ホスト プールを作成する

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

まだ行っていない場合は、[PowerShell モジュールのセットアップ](powershell-module.md)に関する記事の手順に従ってください。

次のコマンドレットを実行して Azure Virtual Desktop 環境にサインインします。

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname>
```

このコマンドレットにより、ホスト プール、ワークスペース、およびデスクトップ アプリ グループが作成されます。 さらに、デスクトップ アプリ グループがワークスペースに登録されます。 このコマンドレットを使用してワークスペースを作成するか、既存のワークスペースを使用することができます。

次のコマンドレットを実行して、セッション ホストがホスト プールに参加することを承認する登録トークンを作成し、それをローカル コンピューター上の新しいファイルに保存します。 *-ExpirationTime* パラメーターを使用して、登録トークンが有効である時間の長さを指定できます。

>[!NOTE]
>トークンの有効期限は 1 時間以上、1 か月以内にする必要があります。 この制限を超えて *-ExpirationTime* を設定した場合、コマンドレットではトークンが作成されません。

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

たとえば、2 時間以内に有効期限が切れるトークンを作成する場合は、次のコマンドレットを実行します。

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

その後、このコマンドレットを実行して、Azure Active Directory ユーザーをホスト プールの既定のデスクトップ アプリ グループに追加します。

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

次のコマンドレットを実行して、Azure Active Directory ユーザー グループをホスト プールの既定のデスクトップ アプリ グループに追加します。

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

次のコマンドレットを実行して、登録トークンを変数にエクスポートします。これは後で [Azure Virtual Desktop ホスト プールに仮想マシンを登録](#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool)するときに使用します。

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname>
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

まだ行っていない場合は、Azure CLI 用に環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

サインインした後、[az desktopvirtualization hostpool create](/cli/azure/desktopvirtualization#az_desktopvirtualization_hostpool_create) コマンドを使用して新しいホスト プールを作成し、必要に応じて、セッション ホストをホスト プールに参加させるための登録トークンを作成します。

```azurecli
az desktopvirtualization hostpool create --name "MyHostPool" \
   --resource-group "MyResourceGroup" \
   --location "MyLocation" \
   --host-pool-type "Pooled" \
   --load-balancer-type "BreadthFirst" \
   --max-session-limit 999 \
   --personal-desktop-assignment-type "Automatic" \
   --registration-info expiration-time="2022-03-22T14:01:54.9571247Z" registration-token-operation="Update" \
   --sso-context "KeyVaultPath" \
   --description "Description of this host pool" \
   --friendly-name "Friendly name of this host pool" \
   --tags tag1="value1" tag2="value2"
```

---

## <a name="create-virtual-machines-for-the-host-pool"></a>ホスト プールの仮想マシンを作成する

ここで、Azure Virtual Desktop ホスト プールに参加できる Azure 仮想マシンを作成できます。

仮想マシンは複数の方法で作成できます。

- [Azure ギャラリー イメージから仮想マシンを作成する](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [マネージド イメージから仮想マシンを作成する](../virtual-machines/windows/create-vm-generalized-managed.md)
- [アンマネージド イメージから仮想マシンを作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-from-user-image)

>[!NOTE]
>ホスト OS として Windows 7 を使用して仮想マシンをデプロイしている場合、作成と展開のプロセスは若干異なります。 詳細については、「[Azure Virtual Desktop で Windows 7 仮想マシンをデプロイする](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md)」を参照してください。

セッション ホスト仮想マシンを作成したら、[Windows ライセンスをセッション ホスト VM に適用](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm)すると、ライセンス料をさらに支払うことなく、Windows または Windows Server の仮想マシンが実行されます。

## <a name="prepare-the-virtual-machines-for-azure-virtual-desktop-agent-installations"></a>Azure Virtual Desktop エージェントのインストール用に仮想マシンを準備する

Azure Virtual Desktop エージェントをインストールして仮想マシンを Azure Virtual Desktop ホスト プールに登録する前に、次の操作を行って仮想マシンを準備する必要があります。

- マシンをドメインに参加させる必要があります。 これにより、受信 Azure Virtual Desktop ユーザーを Azure Active Directory アカウントから Active Directory アカウントにマップでき、仮想マシンへのアクセスを正常に許可できます。
- 仮想マシンで Windows Server OS を実行している場合は、リモート デスクトップ セッション ホスト (RDSH) ロールをインストールする必要があります。 RDSH ロールでは、Azure Virtual Desktop エージェントを正常にインストールできます。

正常にドメイン参加するには、各仮想マシン上で次の操作を実行します。

1. 仮想マシンの作成時に指定した資格情報を使用して[仮想マシンに接続](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)します。
2. 仮想マシン上で、 **[コントロール パネル]** を起動し、 **[システム]** を選択します。
3. **[コンピューター名]** を選択し、 **[設定の変更]** を選択してから **[変更]** を選択します
4. **[ドメイン]** を選択し、仮想ネットワーク上の Active Directory ドメインを入力します。
5. ドメインに参加しているマシンに対する権限を持つドメイン アカウントで認証します。

    >[!NOTE]
    > ご使用の VM を Azure Active Directory Domain Services (Azure AD DS) 環境へ参加させる場合は、ドメイン参加ユーザーが [AAD DC Administrators グループ](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)のメンバーでもあることを確認します。

>[!IMPORTANT]
>Windows インストーラーを無効にするポリシーや構成は、有効にしないことをお勧めします。 Windows インストーラーを無効にすると、サービスでセッション ホストにエージェントの更新をインストールできなくなり、セッション ホストが正常に機能しなくなります。

## <a name="register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool"></a>Azure Virtual Desktop ホスト プールに仮想マシンを登録する

Azure Virtual Desktop ホスト プールへの仮想マシンの登録は、Azure Virtual Desktop エージェントのインストールと同じくらい簡単です。

Azure Virtual Desktop エージェントを登録するには、各仮想マシン上で次の操作を行います。

1. 仮想マシンの作成時に指定した資格情報を使用して[仮想マシンに接続](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)します。
2. Azure Virtual Desktop エージェントをダウンロードしてインストールします。
   - [Azure Virtual Desktop エージェント](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)をダウンロードします。
   - インストーラーを実行します。 インストーラーから登録トークンを要求されたら、**Get-AzWvdRegistrationInfo** コマンドレットから取得した値を入力します。
3. Azure Virtual Desktop エージェント ブートローダーをダウンロードしてインストールします。
   - [Azure Virtual Desktop エージェント ブートローダー](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)をダウンロードします。
   - インストーラーを実行します。

>[!IMPORTANT]
>Azure で Azure Virtual Desktop 環境のセキュリティを保護できるようにするには、ご利用の VM 上の受信ポート 3389 を開かないことをお勧めします。 Azure Virtual Desktop では、ユーザーがホスト プールの VM にアクセスするために、受信ポート 3389 を開く必要はありません。 トラブルシューティングの目的でポート 3389 を開く必要がある場合は、[Just-In-Time VM アクセス](../security-center/security-center-just-in-time.md)を使用することをお勧めします。 また、パブリック IP に VM を割り当てないことをお勧めします。

## <a name="update-the-agent"></a>エージェントを更新する

次のいずれかの状況に該当する場合は、エージェントを更新する必要があります。

- 以前に登録されたセッション ホストを新しいホスト プールに移行する必要がある
- 更新後にセッション ホストがホスト プールに表示されない

エージェントを更新するには、次の操作を実行します。

1. 管理者として VM にサインインします。
2. **[サービス]** に移動して、**Rdagent** プロセス、**Remote Desktop Agent Loader** プロセスの順に停止します。
3. 次に、エージェント MSI とブートローダー MSI を見つけます。 これらは、**C:\DeployAgent** フォルダー、またはインストール時に保存した場所のいずれかに配置されています。
4. 次のファイルを見つけて、アンインストールします。
     
     - Microsoft.RDInfra.RDAgent.Installer-x64-verx.x.x
     - Microsoft.RDInfra.RDAgentBootLoader.Installer-x64

   これらのファイルをアンインストールするには、各ファイル名を右クリックし、 **[アンインストール]** を選択します。
5. 必要に応じて、次のレジストリ設定を削除することもできます。
     
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDInfraAgent
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDAgentBootLoader

6. これらの項目をアンインストールすると、古いホスト プールとの関連付けがすべて削除されます。 このホストをサービスに再登録する場合は、「[Azure Virtual Desktop ホスト プールに仮想マシンを登録する](create-host-pools-powershell.md#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool)」の手順に従います。


## <a name="next-steps"></a>次のステップ

これでホスト プールが作成されたので、RemoteApp を設定できます。 Azure Virtual Desktop 内でアプリを管理する方法に関する詳細については、アプリ グループの管理に関するチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [アプリ グループの管理に関するチュートリアル](./manage-app-groups.md)
