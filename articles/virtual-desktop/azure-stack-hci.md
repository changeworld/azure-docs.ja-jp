---
title: Azure Stack HCI 用 Azure Virtual Desktop (プレビュー) をセットアップする - Azure
description: Azure Stack HCI 用 Azure Virtual Desktop (プレビュー) をセットアップする方法。
author: Heidilohr
ms.topic: how-to
ms.date: 11/02/2021
ms.author: helohr
manager: femila
ms.custom: ignite-fall-2021
ms.openlocfilehash: ce89ec45e9c4f59107b205f04ce2c3d83afc8ce2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704023"
---
# <a name="set-up-azure-virtual-desktop-for-azure-stack-hci-preview"></a>Azure Stack HCI 用 Azure Virtual Desktop (プレビュー) をセットアップする

> [!IMPORTANT]
> Azure Stack HCI 用 Azure Virtual Desktop は現在プレビュー中です。
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Azure Stack HCI 用 Azure Virtual Desktop (プレビュー) を使うと、オンプレミスの Azure Stack HCI インフラストラクチャで Azure Virtual Desktop セッション ホストを使うことができます。 詳しくは、「[Azure Stack HCI 用 Azure Virtual Desktop (プレビュー)](azure-stack-hci-overview.md)」をご覧ください。

## <a name="requirements"></a>必要条件

Azure Stack HCI 用 Azure Virtual Desktop を使うには、次のものが必要です。

- [Azure に登録された Azure Stack HCI クラスター](/azure-stack/hci/deploy/register-with-azure)。

- 必要なすべての管理者アクセス許可を持つ Azure Virtual Desktop セッション ホスト プール作成用の Azure サブスクリプション。

- [Azure Active Directory と同期されたオンプレミスの Active Directory (AD)](/azure/architecture/reference-architectures/identity/azure-ad)。

- オンプレミス ネットワークから Azure への安定した接続。

- お使いのオンプレミス ネットワークから、Azure Virtual Desktop の、仮想マシンに[必要な URL の一覧](safe-url-list.md)に記載されているすべての必須 URL へのアクセス。

## <a name="configure-azure-virtual-desktop-for-azure-stack-hci"></a>Azure Stack HCI 用 Azure Virtual Desktop を構成する

Azure Stack HCI 用 Azure Virtual Desktop をセットアップするには:

1. 「[ホスト プールのセットアップ プロセスの開始](create-host-pools-azure-marketplace.md#begin-the-host-pool-setup-process)」の手順に従って、仮想マシンがない新しいホスト プールを作成します。 そのセクションを最後まで行った後、この記事に戻り、ステップ 2 から始めます。

2. 新しく作成したホスト プールを検証ホスト プールとして構成します。「[検証ホスト プールとしてホスト プールを定義する](create-validation-host-pool.md#define-your-host-pool-as-a-validation-host-pool)」の手順に従って検証環境プロパティを有効にしてください。

3. 「[ワークスペース情報](create-host-pools-azure-marketplace.md#workspace-information)」の指示に従って、自分用のワークスペースを作成します。

4. 「[新しい仮想マシンを作成する](/azure-stack/hci/manage/vm#create-a-new-vm)」の手順に従って、Azure Stack HCI インフラストラクチャに新しい仮想マシンをデプロイします。 サポートされている OS で VM をデプロイし、ドメインに参加させます。

   >[!NOTE]
   >VM で Windows Server OS が実行されている場合は、リモート デスクトップ セッション ホスト (RDSH) ロールをインストールします。

5. Connected Machine エージェントをインストールして、Azure で Azure Arc を通して新しい仮想マシンを管理できるようにします。 「[Azure Arc 対応サーバーにハイブリッド マシンを接続する](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)」の説明に従って、仮想マシンに Windows エージェントをインストールします。

6. [Azure Virtual Desktop エージェント](agent-overview.md)をインストールして、前に作成した Azure Virtual Desktop ホスト プールに仮想マシンを追加します。 その後、[Azure Virtual Desktop ホスト プールへの VM の登録](create-host-pools-powershell.md#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool)に関する記事の手順に従って、VM を Azure Virtual Desktop サービスに登録します。

7. [アプリ グループの作成とユーザー割り当ての管理](manage-app-groups.md)に関する記事の説明に従って、テスト用のアプリ グループを作成し、それにユーザー アクセスを割り当てます。

8. [Web クライアント](./user-documentation/connect-web.md)に移動し、新しいデプロイへのアクセス権をユーザーに付与します。

## <a name="optional-configurations"></a>オプションの構成

Azure Stack HCI 用 Azure Virtual Desktop を設定したので、デプロイのニーズに応じてさらにいくつかのことを行うことができます。

### <a name="create-a-profile-container-using-a-file-share-on-azure-stack-hci"></a>Azure Stack HCI でファイル共有を使ってプロファイル コンテナーを作成する

ファイル共有を使ってプロファイル コンテナーを作成するには:

1. 単一またはクラスター化された Windows Server VM のデプロイにファイル共有をデプロイします。 ファイル サーバーの役割を持つ Windows Server VM は、セッション ホスト VM がデプロイされているのと同じクラスターに併置することもできます。

2. 仮想マシンの作成時に指定した資格情報を使用して仮想マシンに接続します。

3. 仮想マシン上で、 **[コントロール パネル]** を起動し、 **[システム]** を選択します。

4. コンピューター名を選び、 **[設定の変更]** を選んでから **[変更]** を選びます。

5. **[ドメイン]** を選び、仮想ネットワーク上の Active Directory ドメインを入力します。

6. ドメインに参加しているマシンに対する権限を持つドメイン アカウントで認証します。

7. [ファイル共有として機能する VM の準備](create-host-pools-user-profile.md#prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles)に関する記事の指示に従って、デプロイ用に VM を準備します。

8. 「[FSLogix プロファイル コンテナーを構成する](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)」の説明に従って、使用するプロファイル コンテナーを構成します。

### <a name="download-supported-os-images-from-azure-marketplace"></a>サポートされている OS イメージを Azure Marketplace からダウンロードする

Azure Virtual Desktop と Azure Stack HCI の両方でサポートされている任意の OS イメージを、デプロイで実行できます。 Azure Virtual Desktop でサポートされている OS については、[サポートされている VM OS イメージ](overview.md#supported-virtual-machine-os-images)に関する記事をご覧ください。

イメージをダウンロードするには 2 つのオプションがあります。

- 好みの OS イメージを使って VM をデプロイした後、「[Azure から Windows VHD をダウンロードする](../virtual-machines/windows/download-vhd.md)」の手順に従います。
- VM をデプロイせずに、Azure から Windows 仮想ハード ディスク (VHD) をダウンロードします。

VM をデプロイしないで Windows VHD をダウンロードするには、いくつかの追加ステップがあります。 VM をデプロイせずに Azure から VHD をダウンロードするには、次のセクションの手順を順番に行う必要があります。

### <a name="requirements-to-download-a-vhd-without-a-vm"></a>VM なしで VHD をダウンロードするための要件

始める前に、Azure に接続し、コマンド プロンプトまたは bash 環境で [Azure Cloud Shell](../cloud-shell/quickstart.md) を実行します。 Azure コマンド ライン インターフェイス (CLI) で CLI 参照コマンドを実行することもできます。

ローカル インストールを使っている場合は、[az login](/cli/azure/reference-index#az_login) コマンドを実行して Azure にサインインします。

その後、表示される他のプロンプトに従ってサインインを完了します。 その他のサインイン オプションについては、[Azure CLI でのサインイン](/cli/azure/authenticate-azure-cli)に関するページを参照してください。

Azure CLI を初めて使う場合は、「[Azure CLI で拡張機能を使用する](/cli/azure/azure-cli-extensions-overview)」の手順に従って、必要な拡張機能をインストールします。

最後に、[az version](/cli/azure/reference-index?#az_version) コマンドを実行して、クライアントが最新の状態であることを確認します。 最新ではない場合は、[az upgrade](/cli/azure/reference-index?#az_upgrade) コマンドを実行して最新バージョンにアップグレードします。

### <a name="search-azure-marketplace-for-azure-virtual-desktop-images"></a>Azure Marketplace で Azure Virtual Desktop のイメージを検索する

Azure portal で Azure Marketplace の **検索** 機能を使って、探しているイメージを見つけることができます。 特に Azure Virtual Desktop のイメージを検索するには、次のいずれかのクエリ例を実行します。

Windows 10 マルチセッションを探している場合は、次の条件で検索を実行できます。

```azure
az vm image list --all --publisher "microsoftwindowsdesktop" --offer "windows-10" --sku "21h1-evd-g2"
```

このコマンドからは次の URN が返されるはずです。

```azure
MicrosoftWindowsDesktop:Windows-10:21h1-evd-g2:latest
```

Windows Server 2019 データセンターを探している場合は、Azure CLI で次の条件を実行します。

```azure
az vm image list --all --publisher "microsoftwindowsserver" --offer "WindowsServer" --sku "2019-Datacenter-gen2"
```

このコマンドからは次の URN が返されるはずです。

```azure
MicrosoftWindowsServer:windowsserver-gen2preview:2019-datacenter-gen2:latest
```

>[!IMPORTANT]
>第 2 世代 ("gen2") のイメージのみを使用してください。 Azure Stack HCI 用 Azure Virtual Desktop では、第 1 世代 ("gen1") イメージでの VM の作成はサポートされていません。 "-g1" というサフィックスが付いている SKU は避けます。

### <a name="create-a-new-azure-managed-disk-from-the-image"></a>イメージから新しい Azure マネージド ディスクを作成する

次に、Azure Marketplace からダウンロードしたイメージから Azure マネージド ディスクを作成する必要があります。

Azure マネージド ディスクを作成するには:

1. Azure コマンド ライン プロンプトで次のコマンドを実行して、マネージド ディスクのパラメーターを設定します。 角かっこ内の項目は、実際のシナリオに関連する値に置き換えます。

```azure
$urn = <URN of the Marketplace image> #Example: “MicrosoftWindowsServer:WindowsServer:2019-Datacenter:Latest”
$diskName = <disk name> #Name for new disk to be created
$diskRG = <resource group> #Resource group that contains the new disk
```

2. 次のコマンドを実行してディスクを作成し、Serial Attached SCSI (SAS) アクセス URL を生成します。

```azure
az disk create -g $diskRG -n $diskName --image-reference $urn
$sas = az disk grant-access --duration-in-seconds 36000 --access-level Read --name $diskName --resource-group $diskRG
$diskAccessSAS = ($sas | ConvertFrom-Json)[0].accessSas
```

### <a name="export-a-vhd-from-the-managed-disk-to-azure-stack-hci-cluster"></a>マネージド ディスクから Azure Stack HCI クラスターに VHD をエクスポートする

その後、作成した VHD をマネージド ディスクから Azure Stack HCI クラスターにエクスポートする必要があります。これを使って、新しい VM を作成できます。 通常の Web ブラウザーまたは Storage Explorer で次の方法を使うことができます。

VHD をエクスポートするには:

1. ブラウザーを開き、「[イメージから新しい Azure マネージド ディスクを作成する](#create-a-new-azure-managed-disk-from-the-image)」で生成したマネージド ディスクの SAS URL に移動します。 この URL で、Azure Marketplace からダウンロードしたイメージの VHD イメージをダウンロードできます。

2. VHD イメージをダウンロードします。 ダウンロード プロセスには数分かかる場合があるのでお待ちください。 イメージが完全にダウンロードされたことを確認した後、次のセクションに進みます。

>[!NOTE]
>AzCopy を実行している場合は、このコマンドを実行して md5check をスキップする必要があります。
>
> ```azure
> azcopy copy “$sas" "destination_path_on_cluster" --check-md5 NoCheck
> ```

### <a name="clean-up-the-managed-disk"></a>マネージド ディスクのクリーンアップ

VHD を使い終わったら、マネージド ディスクを削除して領域を解放する必要があります。

作成したマネージド ディスクを削除するには、次のコマンドを実行します。

```azure
az disk revoke-access --name $diskName --resource-group $diskRG 
az disk delete --name $diskName --resource-group $diskRG --yes
```

このコマンドの完了には数分かかる場合があるのでお待ちください。

>[!NOTE]
>必要に応じて、次のコマンドを実行して、ダウンロード VHD を動的 VHDx に変換することもできます。
>
> ```powershell
> Convert-VHD -Path " destination_path_on_cluster\file_name.vhd" -DestinationPath " destination_path_on_cluster\file_name.vhdx" -VHDType Dynamic
> ```

## <a name="next-steps"></a>次のステップ

基本または価格に関する情報を確認する必要がある場合は、[Azure Stack HCI 用 Azure Virtual Desktop](azure-stack-hci-overview.md) に関する記事をご覧ください。

その他の質問がある場合は、[FAQ](azure-stack-hci-faq.yml) をご覧ください。
