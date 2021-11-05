---
title: Azure Active Directory (プレビュー) を使用して Azure 内の Linux 仮想マシンにサインインする
description: Linux を実行している Azure VM への Azure AD サインイン
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 10/21/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli, subject-rbac-steps
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeca09f5763cf11edc13cecd2df0c267fad8a23d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049750"
---
# <a name="preview-login-to-a-linux-virtual-machine-in-azure-with-azure-active-directory-using-ssh-certificate-based-authentication"></a>プレビュー: SSH 証明書ベースの認証を使用した Azure Active Directory で Azure の Linux 仮想マシンにログインする

Azure の Linux 仮想マシン (VM) のセキュリティを強化するために、Azure Active Directory (Azure AD) の認証と統合できます。 コア認証プラットフォームと証明機関として Azure AD を使用して、AD および SSH 証明書ベースの認証によって Linux VM に SSH 接続できるようになりました。 この機能により、組織は、VM へのアクセスを管理する Azure ロールベースのアクセス制御 (RBAC) ポリシーと条件付きアクセス ポリシーを集中管理して、適用できます。 この記事では、SSH 証明書ベースの認証を使用した Azure AD で Linux VM を作成および構成し、ログインをする方法について説明します。

> [!IMPORTANT]
> この機能は現在パブリック プレビューの段階です。 [デバイス コード フローを使用した以前のバージョンは、2021 年 8 月 15 日に非推奨になります](../../virtual-machines/linux/login-using-aad.md)。 古いバージョンからこのバージョンに移行するには、「[前のプレビューからの移行](#migration-from-previous-preview)」セクションを参照してください。
> このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 テスト後に破棄する予定のテスト用仮想マシンでこの機能を使用してください。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

SSH 証明書ベースの認証による Azure AD を使用して、Azure の Linux VM にログインすることには、次のような多くのメリットがあります。

- Azure AD 資格情報を使用して、Azure Linux VM にログインします。
- SSH キーをユーザーに配布したり、デプロイする Azure Linux VM で SSH 公開キーをプロビジョニングしたりする必要なく、SSH キー ベースの認証を取得します。 このエクスペリエンスは、未認可のアクセスの原因となる可能性がある古い SSH 公開キーの無秩序な広がりについて心配しなければならないことよりもはるかに単純です。
- ローカル管理者アカウントへの依存、資格情報の盗難、脆弱な資格情報を削減します。
- Azure AD に構成されたパスワードの複雑さと、パスワード有効期間ポリシーによって、Linux VM のセキュリティ保護にも役立ちます。
- Azure ロールベースのアクセス制御 (Azure RBAC) により、通常のユーザーとして、または管理者権限を使用して、VM にログインできるユーザーを指定できます。 ユーザーがチームに参加またはチームから脱退する場合は、適切なアクセス権が付与されるよう VM の Azure RBAC ポリシーを更新できます。 従業員が退職し、そのユーザー アカウントが無効化または Azure AD から削除されると、リソースにアクセスできなくなります。
- 条件付きアクセスにより、Linux VM に SSH 接続する前に、多要素認証を必要としたり、SSH 接続に使用するクライアント デバイスをマネージド デバイス (準拠デバイスや Hybrid Azure AD Join を使用した) にすることを必要としたりするポリシーを構成します。 
- Azure のデプロイおよび監査ポリシーを使用して、Linux VM への Azure AD ログインを必要とし、VM で承認されていないローカル アカウントの使用にフラグを設定します。
- Azure Active Directory による Linux VM へのログインは、フェデレーション サービスを使用するお客様にも機能します。

## <a name="supported-linux-distributions-and-azure-regions"></a>サポートされている Linux ディストリビューションと Azure リージョン

サポートされているリージョンにデプロイされる場合、この機能のプレビュー期間中、次の Linux ディストリビューションが現在サポートされています。

| Distribution | Version |
| --- | --- |
| CentOS | CentOS 7、CentOS 8.3 |
| Debian | Debian 9、Debian 10 |
| openSUSE | openSUSE Leap 42.3、openSUSE Leap 15.1+ |
| RedHat Enterprise Linux | RHEL 7.4 ～ RHEL 7.10、RHEL 8.3 |
| SUSE Linux Enterprise Server | SLES 12, SLES 15.1+ |
| Ubuntu Server | Ubuntu Server 16.04 から Ubuntu Server 20.04 |

この機能のプレビュー期間中は、次の Azure リージョンがサポートされます。

- Azure Global

 
Azure Kubernetes Service (AKS) クラスターでは、この拡張機能の使用はサポートされていません。 詳細については、[AKS のポリシーのサポート](../../aks/support-policies.md)に関するページを参照してください。

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.22.1 以降を実行していることを必要とします。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="requirements-for-login-with-azure-ad-using-ssh-certificate-based-authentication"></a>SSH 証明書ベースの認証を使用した Azure AD によるログインの要件

Azure の Linux VM に対して SSH 証明書ベースの認証を使用した Azure AD ログインを有効にするには、次のネットワーク、仮想マシン、クライアント (ssh クライアント) の要件が満たされていることを確認する必要があります。

### <a name="network"></a>ネットワーク

VM ネットワーク構成では、TCP ポート 443 経由の次のエンドポイントへの発信アクセスが許可されている必要があります。

Azure Global の場合

- `https://packages.microsoft.com` - パッケージのインストールとアップグレード用。
- `http://169.254.169.254` - Azure Instance Metadata Service エンドポイント。
- `https://login.microsoftonline.com` - PAM (プラグ可能な認証モジュール) ベースの認証フロー用。
- `https://pas.windows.net` - Azure RBAC フロー用。

Azure Government の場合

- `https://packages.microsoft.com` - パッケージのインストールとアップグレード用。
- `http://169.254.169.254` - Azure Instance Metadata Service エンドポイント。
- `https://login.microsoftonline.us` - PAM (プラグ可能な認証モジュール) ベースの認証フロー用。
- `https://pasff.usgovcloudapi.net` - Azure RBAC フロー用。

Azure China の場合

- `https://packages.microsoft.com` - パッケージのインストールとアップグレード用。
- `http://169.254.169.254` - Azure Instance Metadata Service エンドポイント。
- `https://login.chinacloudapi.cn` - PAM (プラグ可能な認証モジュール) ベースの認証フロー用。
- `https://pas.chinacloudapi.cn` - Azure RBAC フロー用。

### <a name="virtual-machine"></a>仮想マシン

次の機能で VM が構成されていることを確認します。

- システム割り当てマネージド ID。 Azure portal を使用して VM を作成し、Azure AD ログイン オプションを選択すると、このオプションが自動的に選択されます。 Azure CLI を使用して、新規または既存の VM でシステム割り当てマネージド ID を有効にすることもできます。
- `aadsshlogin` と`aadsshlogin-selinux` (必要に応じて) これらのパッケージは、AADSSHLoginForLinux VM 拡張機能によってインストールされます。 この拡張機能は、Azure portal を使用して VM を作成し、Azure AD ログインを有効にした ([管理] タブ) とき、または Azure CLI 経由でインストールされます。

### <a name="client"></a>クライアント

クライアントが次の要件を満たしていることを確認してください。

- SSH クライアントでは、認証のために OpenSSH ベースの証明書をサポートしている必要があります。 Az CLI (2.21.1 以降) と OpenSSH (Windows 10 バージョン 1803 またはそれ以降に組み込み) または Azure Cloud Shell を使用して、この要件を満たすことができます。 
- Az CLI 用 SSH 拡張機能。 これは、`az extension add --name ssh` を使用してインストールできます。 この拡張機能は、Azure Cloud Shell を使用している場合は、プレインストール済みであるため、インストールする必要がありません。
- OpenSSH 証明書をサポートする Az CLI または Azure Cloud Shell 以外の他の SSH クライアントを使用している場合でも、SSH 拡張機能を備えた Az CLI を使用して、構成ファイル内のエフェメラル SSH 証明書を取得し、オプションで SSH クライアントでその構成ファイルを使用する必要があります。
- クライアントから VM のパブリックまたはプライベートのいずれかの IP への TCP 接続 (ProxyCommand または SSH 転送と接続のあるマシンへの接続も機能します)。

## <a name="enabling-azure-ad-login-in-for-linux-vm-in-azure"></a>Azure 内の Linux VM に対して Azure AD ログインを有効にする

Azure 内の Linux VM に Azure AD ログインを使用するには、最初に Linux VM に対して Azure AD ログイン オプションを有効にし、VM にログインする権限を持つユーザーの Azure ロールの割り当てを構成してから、Az CLI や Az Cloud Shell などの OpensSSH をサポートする SSH クライアントを使用して、Linux VM に SSH 接続する必要があります。 Linux VM に対して Azure AD ログインを有効にする方法は複数あり、たとえば次を使用できます。

- Linux VM の作成時の Azure portal エクスペリエンス
- Windows VM の作成時、または既存の Linux VM に対する Azure Cloud Shell エクスペリエンス

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Azure portal の VM の作成エクスペリエンスを使用して Azure AD ログインを有効にする

Azure portal を使用して、上記のいずれかのサポートされている Azure AD Linux ディストリビューションに対して Azure AD ログインを有効にできます。

たとえば、Azure AD ログオンによって、Azure に Ubuntu Server 18.04 LTS VM を作成するには:

1. VM を作成するためのアクセス権を持つアカウントを使用して Azure portal にサインし、 **[+ リソースの作成]** を選択します。
1. **[人気順]** ビューの **[Ubuntu Server 18.04 LTS]** の下の **[作成]** をクリックします。
1. **[管理]** タブで、 
   1. チェック ボックスをオンにし、 **[Azure Active Directory でログインする (プレビュー)]** を有効にします。
   1. **システム割り当てマネージド ID** がオンにされていることを確認します。
1. 仮想マシンの作成エクスペリエンスの残りの部分に移動します。 このプレビュー期間中は、ユーザー名とパスワードまたは SSH 公開キーで管理者アカウントを作成する必要があります。
 
### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Azure Cloud Shell のエクスペリエンスを使用して Azure AD ログインを有効にする

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 Cloud Shell には一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 [コピー] ボタンを選択してコードをコピーし、Cloud Shell に貼り付けて Enter キーを押すだけで、コードを実行することができます。 Cloud Shell は、次のようにいくつかの方法で開くことができます。

- コード ブロックの右上隅にある [使ってみる] を選択します。
- ブラウザーで Cloud Shell を開きます。
- Azure portal の右上隅にあるメニューの [Cloud Shell] ボタンを選択します。

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.22.1 以降を実行していることが要件となります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、記事「Azure CLI のインストール」を参照してください。

1. [az group create](/cli/azure/group#az_group_create) を使用して、リソース グループを作成します。
1. サポートされるリージョンのサポートされるディストリビューションを使用して、[az vm create](/cli/azure/vm#az_vm_create&preserve-view=true)で VM を作成します。
1. [az vm extension set](/cli/azure/vm/extension?view=azure-cli-latest#az_vm_extension_set&preserve-view=true) によって、Azure AD ログイン VM 拡張機能をインストールします。

次の例では、*southcentralus* リージョンの *AzureADLinuxVMPreview* というリソース グループに *Ubuntu 18.04 LTS* を使用する *myVM* という名前の VM をデプロイします。 次に、*Azure AD ログイン VM 拡張機能* をインストールし、Linux VM に対して Azure AD ログインを有効にします。 VM 拡張機能は、Azure 仮想マシンでのデプロイ後の構成と自動タスクを提供する小さなアプリケーションです。

この例は、必要に応じてテスト要件をサポートするためにカスタマイズできます。

```azurecli-interactive
az group create --name AzureADLinuxVMPreview --location southcentralus

az vm create \
    --resource-group AzureADLinuxVMPreview \
    --name myVM \   
    --image UbuntuLTS \
    --assign-identity \
    --admin-username azureuser \
    --generate-ssh-keys

az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADSSHLoginForLinux \
    --resource-group AzureADLinuxVMPreview \
    --vm-name myVM
```

VM とサポートするリソースを作成するには数分かかります。

AADSSHLoginForLinux 拡張機能は、実行中の VM エージェントによって、既存の (サポートされているディストリビューション) Linux VM にインストールして、Azure AD 認証を有効にすることができます。 この拡張機能を、以前に作成した VM にデプロイする場合は、マシンに少なくとも 1 GB のメモリが割り当てられていることを確認してください。そうでない場合、拡張機能のインストールは失敗します。

VM に拡張機能が正常にインストールされると、provisioningState に Succeeded が表示されます。 拡張機能をインストールするには、VM に実行中の [VM エージェント](../../virtual-machines/extensions/agent-linux.md)が必要です。

## <a name="configure-role-assignments-for-the-vm"></a>仮想マシン ロールの割り当てを構成する

VM を作成したので、VM にログインできるユーザーを決定する Azure RBAC ポリシーを構成する必要があります。 VM へのログインを承認するには、次の 2 つの Azure ロールが使用されます。

- **仮想マシンの管理者ログイン**:このロールを割り当てられたユーザーは、管理者特権を持つユーザーとして Azure 仮想マシンにログインできます。
- **仮想マシンのユーザー ログイン**:このロールが割り当てられたユーザーは正規ユーザーの権限を持つユーザーとして Azure 仮想マシンにログインできます。
 
ユーザーが SSH 経由で VM にログインできるようにするには、仮想マシンの管理者ログインまたは仮想マシンのユーザー ログイン ロールのいずれかをユーザーに割り当てる必要があります。 VM の所有者または共同作成者ロールが割り当てられた Azure ユーザーに、SSH 経由で VM に Azure AD ログインする権限は自動的には付与されません。 この分離は、仮想マシンを管理する一連のユーザーと仮想マシンにアクセスできる一連のユーザーを監査上分離するためです。 

VM のロールの割り当てを構成できる方法は複数あり、たとえば次を使用できます。

- Azure AD ポータル エクスペリエンス
- Azure Cloud Shell エクスペリエンス

> [!Note]
> 仮想マシンの管理者ログインと仮想マシンのユーザー ログインのロールは、dataActions を使用しているため、管理グループのスコープで割り当てることはできません。 現時点では、これらのロールは、サブスクリプション、リソース グループまたはリソース スコープでのみ割り当てることができます。 サブスクリプションあたりの [Azure ロール割り当ての上限](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)を超えるリスクを避けるため、個々の VM レベルではなく、サブスクリプションまたはリソース レベルでロールを割り当てることが推奨されます。

### <a name="using-azure-ad-portal-experience"></a>Azure AD ポータルのエクスペリエンスを使用する

Azure AD 対応 Linux VM のロールの割り当てを構成するには:

1. **[アクセス制御 (IAM)]** を選択します。

1. **[追加]**  >  **[ロールの割り当ての追加]** を選択して、[ロールの割り当ての追加] ページを開きます。

1. 次のロールを割り当てます。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。
    
    | 設定 | 値 |
    | --- | --- |
    | Role | **[仮想マシンの管理者ログイン]** または **[仮想マシンのユーザー ログイン]** |
    | アクセスの割り当て先 | ユーザー、グループ、サービス プリンシパル、またはマネージド ID |

    ![Azure portal でロール割り当てページを追加します。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

しばらくすると、セキュリティ プリンシパルに選択されたスコープのロールが割り当てられます。
 
### <a name="using-the-azure-cloud-shell-experience"></a>Azure Cloud Shell のエクスペリエンスを使用する

次の例では、[az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) を使用し、現在の Azure ユーザーに対して、VM に対する仮想マシンの管理者ログイン ロールを割り当てます。 現在の Azure アカウントのユーザー名は [az account show](/cli/azure/account#az_account_show) で取得され、スコープは、[az vm show](/cli/azure/vm#az_vm_show) で前の手順で作成された VM に設定されます。 スコープは、リソース グループまたはサブスクリプション レベルで割り当てることもでき、Azure RBAC における通常の継承アクセス許可が適用されます。

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group AzureADLinuxVMPreview --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Azure AD ドメインとログオン ユーザー名ドメインが一致しない場合は、`--assignee` のユーザー名だけでなく、`--assignee-object-id` を使用してユーザー アカウントのオブジェクト ID を指定する必要があります。 ユーザー アカウントのオブジェクト ID は、[az ad user list](/cli/azure/ad/user#az_ad_user_list)を使用して取得できます。

Azure RBAC を使用して、Azure サブスクリプション リソースへのアクセスを管理する方法の詳細については、記事「[Azure ロールを割り当てる手順](../../role-based-access-control/role-assignments-steps.md)」を参照してください。

## <a name="install-ssh-extension-for-az-cli"></a>Az CLI 用 SSH 拡張機能のインストール

Azure Cloud Shell を使用している場合は、Az CLI と Az CLI 用 SSH 拡張機能の両方の最低限必要なバージョンが既に Cloud Shell 環境に含まれているため、他のセットアップは必要ありません。

次のコマンドを実行して、Az CLI 用 SSH 拡張機能を追加します

```azurecli
az extension add --name ssh
```

拡張機能に必要な最小バージョンは 0.1.4 です。 次のコマンドを使用して、インストールされている SSH 拡張機能のバージョンを確認します。

```azurecli
az extension show --name ssh
```

## <a name="using-conditional-access"></a>条件付きアクセスの使用

Azure AD ログインによって有効にされる Azure の Linux VM へのアクセスを認可する前に、ユーザーに MFA を要求する、SSH クライアントを実行するデバイスに準拠または Hybrid Azure AD join を使用したデバイスを必要とする、ユーザーとサインインのリスクが低いかどうかを確認するなどの条件付きアクセスポリシーを適用できます。 

条件付きアクセス ポリシーを適用するには、クラウド アプリまたはアクションの割り当てオプションから "Azure Linux VM サインイン" アプリを選択し、次に、多要素認証の要求や準拠または Hybrid Azure AD join を使用したデバイスの要求を満たした後に、条件としてユーザーやサインインのリスクを使用し、アクセス権の付与としてアクセス制御を使用する必要があります。

> [!NOTE]
> SSH クライアントを実行しているクライアント デバイスでのデバイスの準拠または Hybrid Azure AD 参加を必要とする条件付きアクセスポリシーの適用は、Windows および macOS で実行されている Az CLI でのみ機能します。 Linux または Azure Cloud Shell で Az CLI を使用している場合はサポートされません。

## <a name="login-using-azure-ad-user-account-to-ssh-into-the-linux-vm"></a>Azure AD ユーザー アカウントを使用して SSH 接続し、Linux VM にログインする

### <a name="using-az-cli"></a>AZ CLI の使用

まず、az login を実行し、次に az ssh vm を実行します。

```azurecli
az login 
```

このコマンドにより、ブラウザー ウィンドウが起動し、ユーザーが各自の Azure AD アカウントを使用してサインインできます。 

次の例では、VM の適切な IP アドレスが自動的に解決されます。

```azurecli
az ssh vm -n myVM -g AzureADLinuxVMPreview
```

メッセージが表示されたら、ログイン ページで Azure AD ログイン資格情報を入力し、MFA を実行するか、デバイス チェックを満たします。 az CLI セッションが必要な条件付きアクセス基準をまだ満たしていない場合にのみ、メッセージが表示されます。 ブラウザー ウィンドウを閉じ、SSH プロンプトに戻ると、VM に自動的に接続されます。

これで、VM ユーザーまたは VM 管理者などの、割り当てられたロール権限を持つユーザーとして、Azure Linux 仮想マシンにサインインしました。 自分のユーザー アカウントに仮想マシンの管理者ログイン ロールが割り当てられている場合は、sudo を使用してルート権限を必要とするコマンドを実行できます。

### <a name="using-az-cloud-shell"></a>Az Cloud Shell の使用

クライアント コンピューターにローカルに何もインストールする必要なく、Az Cloud Shell を使用して VM に接続できます。 Azure portal の右上隅にあるシェル アイコンをクリックして、Cloud Shell を起動します。
 
Az Cloud Shell によって、サインイン済みユーザーのコンテキストでセッションに自動的に接続します。 Linux 用 Azure AD ログイン プレビュー期間中に、**az login を再度実行し、対話型サインイン フローを実行する必要があります**。

```azurecli
az login
```

それにより、通常の az ssh vm コマンドを実行して、VM の名前とリソース グループまたは IP アドレスを使用して接続できます。

```azurecli
az ssh vm -n myVM -g AzureADLinuxVMPreview
```

> [!NOTE]
> Az Cloud Shell を使用する場合は、デバイスのコンプライアンスまたは Hybrid Azure AD join を必要とする条件付きアクセス ポリシーの適用はサポートされていません。

### <a name="login-using-azure-ad-service-principal-to-ssh-into-the-linux-vm"></a>Azure AD サービス プリンシパルを使用して SSH 接続し、Linux VM にログインする

Azure CLI では、ユーザー アカウントではなく、サービス プリンシパルによる認証がサポートされています。 サービス プリンシパルは特定のユーザーに関連付けられていないアカウントであるため、お客様は、それらを使用して、VM に SSH 接続して、使用する任意の自動化シナリオをサポートできます。 サービス プリンシパルには、VM 管理者または VM ユーザー権限が割り当てられている必要があります。 サブスクリプションまたはリソース グループ レベルでアクセス許可を割り当てます。 

次の例では、リソース グループ レベルで、サービス プリンシパルに VM 管理者権限を割り当てます。 サービス プリンシパル オブジェクト ID、サブスクリプション ID、およびリソース グループ名のフィールドを置き換えてください。

```azurecli
az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee-object-id <service-principal-objectid> \
    --assignee-principal-type ServicePrincipal \
    --scope “/subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>"
```

次の例では、サービス プリンシパルを使用して Azure CLI に対して認証します。 サービス プリンシパルを使用してサインインする方法の詳細については、[サービス プリンシパルを使用した Azure CLI へのサインイン](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)に関する記事を参照してください。

```azurecli
az login --service-principal -u <sp-app-id> -p <password-or-cert> --tenant <tenant-id>
```

サービス プリンシパルによる認証が完了したら、通常の Az CLI SSH コマンドを使用して VM に接続します。

```azurecli
az ssh vm -n myVM -g AzureADLinuxVMPreview
```

### <a name="exporting-ssh-configuration-for-use-with-ssh-clients-that-support-openssh"></a>OpenSSH をサポートする SSH クライアントで使用するための SSH 構成のエクスポート

Azure AD による Azure Linux VM へのログインでは、OpenSSH 証明書と構成のエクスポートがサポートされているため、OpenSSH ベースの証明書をサポートする任意の SSH クライアントを使用して、Azure AD にサインインできます。 次の例では、VM に割り当てられているすべての IP アドレスの構成をエクスポートしています。

```azurecli
az ssh config --file ~/.ssh/config -n myVM -g AzureADLinuxVMPreview
```

または、IP アドレスだけを指定して、構成をエクスポートすることもできます。 この例の IP アドレスは、VM のパブリック IP アドレスまたはプライベート IP アドレス (プライベート IP には独自の接続を使用する必要があります) に置き換えてください。 このコマンドのヘルプについては、「`az ssh config -h`」と入力します。

```azurecli
az ssh config --file ~/.ssh/config --ip 10.11.123.456
```

その後、通常の OpenSSH の使用方法によって VM に接続できます。 接続は、OpenSSH を使用する任意の SSH クライアント経由で行うことができます。

## <a name="sudo-and-azure-ad-login"></a>sudo と Azure AD ログイン

VM 管理者ロールが割り当てられたユーザーが、Linux VM への SSH 接続に成功すると、他の操作や認証要件なしで sudo を実行できるようになります。 VM ユーザー ロールが割り当てられたユーザーは sudo を実行できません。

## <a name="virtual-machine-scale-set-support"></a>仮想マシン スケール セットのサポート

仮想マシン スケール セットはサポートされていますが、仮想マシン スケールセット VM の有効化と接続の手順は若干異なります。

1. まず、仮想マシン スケール セットを作成するか、既に存在するものを選択します。 Azure 仮想マシン スケール セットのシステム割り当てマネージド ID を有効にします。

```azurecli
az vmss identity assign --vmss-name myVMSS --resource-group AzureADLinuxVMPreview
```

2. 仮想マシン スケールセット に Azure AD 拡張機能をインストールします。

```azurecli
az vmss extension set --publisher Microsoft.Azure.ActiveDirectory --name Azure ADSSHLoginForLinux --resource-group AzureADLinuxVMPreview --vmss-name myVMSS
```

通常、仮想マシン スケール セットにはパブリック IP アドレスがないため、Azure Virtual Network に到達できる別のマシンから、それらに接続できる必要があります。 この例では、仮想マシン スケール セット VM のプライベート IP を使用して、同じ仮想ネットワーク内のマシンから接続する方法を示しています。 

```azurecli
az ssh vm --ip 10.11.123.456
```

> [!NOTE]
> `--resource-group` および `--name` スイッチを使用して、仮想マシン スケール セット VM の IP アドレスを自動的に決定することはできません。

## <a name="migration-from-previous-preview"></a>以前のプレビューからの移行

デバイス コード フローに基づいた以前のバージョンの Linux 用 Azure AD ログインを使用しているお客様は、次の手順を実行してください。

1. VM の AADLoginForLinux 拡張機能をアンインストールします。
   
   ```azurecli
   az vm extension delete -g MyResourceGroup --vm-name MyVm -n AADLoginForLinux
   ```

1. VM でシステム割り当てマネージド ID を有効にします。

   ```azurecli
   az vm identity assign -g myResourceGroup -n myVm
   ```

1. VM に AADLoginForLinux 拡張機能をインストールします

    ```azurecli
    az vm extension set \
        --publisher Microsoft.Azure.ActiveDirectory \
        --name AADSSHLoginForLinux \
        --resource-group myResourceGroup \
        --vm-name myVM
    ```

## <a name="using-azure-policy-to-ensure-standards-and-assess-compliance"></a>Azure Policy を使用して、標準および評価コンプライアンスを確保する

Azure Policy を使用して、新規および既存の Linux 仮想マシンに対して Azure AD ログインが確実に有効にされるようにし、Azure Policy コンプライアンス ダッシュボードで大規模に環境のコンプライアンスを評価します。 この機能により、さまざまな適用のレベルを使用できます。Azure AD ログインが有効になっていない環境内の新規および既存の Linux VM にフラグを設定できます。 さらに、Azure Policy を使用して、Azure AD ログインが有効になっていない新しい Linux VM に Azure AD 拡張機能をデプロイするほか、既存の Linux VM を同じ標準に修復することもできます。 これらの機能に加えて、Azure Policy を使用して、マシン上に承認されていないローカル アカウントが作成されている Linux VM を検出してフラグを設定することもできます。 詳細については、[Azure Policy](../../governance/policy/overview.md) に関するページを確認してください。

## <a name="troubleshoot-sign-in-issues"></a>サインアップに関する問題のトラブルシューティング

Azure AD の資格情報を使用して SSH 経由でログインしようとしたときによく発生する問題は、Azure ロールが割り当てられていないために、サインインを求める画面が繰り返し表示されることです。 次のセクションを使用してこの問題を解決してください。

### <a name="could-not-retrieve-token-from-local-cache"></a>ローカル キャッシュからトークンを取得できない

az login を再度実行し、対話型サインイン フローを実行する必要があります。 「[Az Cloud Shell の使用](#using-az-cloud-shell)」セクションを確認してください。

### <a name="access-denied-azure-role-not-assigned"></a>アクセスが拒否されました:Azure ロールが割り当てられていません

SSH プロンプトで次のエラーが表示された場合は、仮想マシンの管理者ログインまたは仮想マシンのユーザー ログイン ロールのいずれかをユーザーに付与する VM 用 Azure RBAC ポリシーが構成済みであることを確認してください。 Azure のロールの割り当てに関する問題が発生した場合は、「[Azure RBAC のトラブルシューティング](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)」の記事を参照してください。

### <a name="extension-install-errors"></a>拡張機能のインストール エラー

既存のコンピューターへの AADSSHLoginForLinux VM 拡張機能のインストールが、次のいずれかの既知のエラー コードで失敗します。

#### <a name="non-zero-exit-code-22"></a>ゼロ以外の終了コード: 22

ポータルに AADSSHLoginForLinux VM 拡張機能の状態が移行中と表示されます。

原因 1: このエラーは、システム割り当てマネージド ID が必要であるためです。

解決策 1: 次のアクションを実行します。

1. 失敗した拡張機能をアンインストールします。
1. Azure VM でシステム割り当てマネージド ID を有効にします。
1. 拡張機能のインストール コマンドを再度実行します。

#### <a name="non-zero-exit-code-23"></a>ゼロ以外の終了コード: 23

ポータルに AADSSHLoginForLinux VM 拡張機能の状態が移行中と表示されます。

原因 1: このエラーは、古い AADLoginForLinux VM 拡張機能がまだインストールされているためです。

解決策 1: 次のアクションを実行します。

1. VM から古い AADLoginForLinux VM 拡張機能をアンインストールします。 ポータルで、新しい AADSSHLoginForLinux VM 拡張機能の状態が、プロビジョニング成功に変わります。

#### <a name="az-ssh-vm-fails-with-keyerror-access_token"></a>az ssh vm が KeyError: 'access_token' で失敗しました。

原因 1: Azure CLI クライアントの古いバージョンが使用されています。

解決策 1: Azure CLI クライアントをバージョン 2.21.0 以上にアップグレードします。

#### <a name="ssh-connection-closed"></a>SSH 接続が閉じられました

az login を使用して正常にサインインした後、`az ssh vm -ip <addres>` または `az ssh vm --name <vm_name> -g <resource_group>` を使用した VM への接続が、 *<ip_address> ポート 22 によって接続が閉じられました* で失敗します。

原因 1: ユーザーが、この VM のスコープ内で、仮想マシンの管理者またはユーザーのいずれのログイン Azure RBAC ロールにも割り当てられていません。

解決策 1: この VM のスコープ内で、ユーザーを仮想マシンの管理者またはユーザーのいずれかのログイン Azure RBAC ロールに追加します。

原因 2: ユーザーが必要な Azure RBAC ロールに含まれていますが、その VM でシステム割り当てマネージド ID が無効になっています。

解決策 2: 次のアクションを実行します。

1. VM でシステム割り当てマネージド ID を有効にします。
1. `az ssh vm --ip <ip_address>` を使用して接続しようとするまでに数分かかることがあります。

### <a name="virtual-machine-scale-set-connection-issues"></a>仮想マシン スケール セットの接続に関する問題

仮想マシン スケール セット インスタンスで古いモデルが実行されている場合、仮想マシン スケール セットの VM 接続が失敗することがあります。 仮想マシン スケール セット インスタンスを最新のモデルにアップグレードすると、特に、Azure AD ログイン拡張機能がインストールされてからアップグレードが実行されていなかった場合に、問題が解決する可能性があります。 インスタンスをアップグレードすると、標準の仮想マシン スケール セット構成が個々のインスタンスに適用されます。

## <a name="preview-feedback"></a>プレビューのフィードバック

[Azure AD フィードバック フォーラム](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789)で、このプレビュー機能に関するフィードバックを共有するか、プレビュー機能の使用に関する問題を報告してください。

## <a name="next-steps"></a>次のステップ
