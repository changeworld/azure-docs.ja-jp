---
title: "Azure 仮想ネットワークの作成、変更、削除 | Microsoft Docs"
description: "仮想ネットワークの作成、変更、削除の方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: b577891afc52013b712634dd51e7e28efcfc4482
ms.contentlocale: ja-jp
ms.lasthandoff: 05/16/2017


---
# <a name="create-change-or-delete-virtual-networks"></a>仮想ネットワークの作成、変更、削除

仮想ネットワーク (VNet) を作成および削除する方法と、既存の VNet の DNS サーバーや IP アドレス空間などの設定を変更する方法について説明します。 VNet とは、クラウド内のユーザー独自のネットワークを表したものです。 サブスクリプション専用に Azure クラウドが論理的に分離されています。 各 VNet では、次のことを実行できます。
- 割り当てるアドレス空間を選択する。 アドレス空間は、10.0.0.0/16 のように CIDR 表記を使用して定義された 1 つ以上のアドレス範囲で構成されます。
- VNet ごとに、Azure で提供される DNS サーバーを使用するか独自の DNS サーバーを使用するかを選択する。 VNet 内で名前を解決するために、VNet に接続されているすべてのリソースにこの DNS サーバーが割り当てられます。
- VNet をサブネットに分割し、VNet のアドレス空間内で各サブネットに専用のアドレス範囲を設定する。 サブネットを作成、変更、および削除する方法については、[サブネットの追加、変更、削除](virtual-network-manage-subnet.md)に関する記事を参照してください。

この記事では、Azure Resource Manager デプロイメント モデルで VNet を作成する方法と、その VNet を変更および削除する方法について説明します。
 
## <a name="before"></a>開始する前に

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- VNet を初めて使用する場合は、この記事を読む前に、[初めての Azure Virtual Network の作成](virtual-network-get-started-vnet-subnet.md)に関する記事で演習を行うことをお勧めします。 この演習は VNet を理解するのに役立ちます。
- VNet の制限について、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事を参照してください。
- Azure アカウントを使用して、Azure Portal、Azure コマンド ライン インターフェイス (CLI)、または Azure PowerShell にログインします。 まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- Azure PowerShell のコマンドを使用してこの記事のタスクを行う場合は、最初に [Azure PowerShell をインストールして構成する](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)必要があります。 最新バージョンの Azure PowerShell コマンドレットがインストールされていることを確認してください。 PowerShell コマンドのヘルプとサンプルを表示するには、「`get-help <command> -full`」と入力します。
- Azure コマンド ライン インターフェイス (CLI) のコマンドを使用してこの記事のタスクを行う場合は、最初に [Azure CLI をインストールして構成する](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)必要があります。 最新バージョンの Azure CLI がインストールされていることを確認してください。 CLI コマンドのヘルプを表示するには、「`az <command> --help`」と入力します。


## <a name="create-vnet"></a>仮想ネットワークの作成

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで[ポータル](https://portal.azure.com)にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事を参照してください。
2. Azure Portal で、**[+新規]** をクリックします。 表示される **[新規]** ブレードで、**[ネットワーク]** をクリックします。 表示される **[ネットワーク]** ブレードで、**[仮想ネットワーク]** をクリックします。
3. 表示される **[仮想ネットワーク]** ブレードの **[デプロイ モデルの選択]** ボックスで、*[Resource Manager]* が選択されていることを確認して、**[作成]** をクリックします。
4. 表示される **[仮想ネットワークの作成]** ブレードで、以下の設定の値を入力するか選択し、**[作成]** をクリックします。
    - **名前**: VNet の作成先として選択する[リソース グループ](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)内で一意となる名前を指定します。 VNet の作成後は、この名前を変更できません。 長く使用する中で、VNet を複数作成する可能性があります。 複数の VNet を管理しやすいように、[名前付け規則](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions)に関する記事で名前の付け方を確認してください。
    - **アドレス範囲**: CIDR 表記で指定します。 定義するアドレス空間は、パブリックとプライベート (RFC 1918 に準拠) のどちらでもかまいません。 パブリックとプライベートのどちらのアドレス空間を定義する場合でも、そのアドレス空間に到達できるのは、VNet 内から、相互接続された VNet から、および VNet に接続したオンプレミス ネットワークからだけです。 次のアドレス空間は追加できません。
        - 224.0.0.0/4 (マルチキャスト)
        - 255.255.255.255/32 (ブロードキャスト)
        - 127.0.0.0/8 (ループバック)
        - 169.254.0.0/16 (リンク ローカル)
        - 168.63.129.16/32 (内部 DNS)
    
      VNet を作成する際に定義できるアドレス空間は 1 つだけですが、VNet の作成後にアドレス空間を追加することができます。 追加の方法については、この記事のセクション「[アドレス空間の追加と削除](#add-address-spaces)」に記載された手順を参照してください。

      >[!WARNING]
      >VNet のアドレス空間が他の VNet やオンプレミス ネットワークと重複している場合、それらを相互に接続することはできません。 アドレス空間を定義する際は、将来その VNet を他の VNet やオンプレミス ネットワークに接続する可能性があることを考慮に入れてください。
      >
      >
    
    - **サブネット名:** 名前は、VNet 内で一意である必要があります。 サブネットの作成後は、この名前を変更できません。 ポータルでは、VNet を作成する際にサブネットを 1 つ定義するよう求められますが、VNet にサブネットを設定することは必須ではありません。 また、ポータルでは、VNet の作成時に定義できるサブネットは 1 つのみですが、VNet の作成後にサブネットを追加することができます。 VNet にサブネットを追加する方法については、[サブネットの作成、変更、削除](virtual-network-manage-subnet.md)に関する記事の[サブネットの作成](virtual-network-manage-subnet.md#create-subnet)に関するセクションを参照してください。 CLI または PowerShell を使用して、複数のサブネットを設定した VNet を作成できます。

      >[!TIP]
      >複数のサブネットを作成し、サブネット間でトラフィックのルーティングのフィルター処理や制御を行うのが一般的です。 サブネットを定義する前に、サブネット間のトラフィックのルーティングやフィルター処理の方法について検討してください。 サブネット間のトラフィックをフィルター処理する方法の詳細については、[ネットワーク セキュリティ グループ](virtual-networks-nsg.md)に関する記事を参照してください。 Azure ではサブネット間のルーティングが自動的に行われますが、Azure の既定のルートを上書きすることもできます。 上書き方法の詳細については、[ユーザー定義のルート](virtual-networks-udr-overview.md)に関する記事を参照してください。
      >

    - **サブネット アドレス範囲:** VNet 用に入力した**アドレス空間**内にある必要があります。 指定できる最小範囲は、/29 です。これでサブネットに 8 つの IP アドレスを使用できます。 Azure では、サブネットごとに、最初と最後のアドレスがプロトコルに準拠するために予約されています。 そのほか、3 つのアドレスが Azure サービスの使用のために予約されています。 そのため、/29 のサブネット アドレス範囲が設定された VNet で使用できる IP アドレスは 3 つのみです。 VNet を VPN ゲートウェイに接続する場合は、ゲートウェイ サブネットを作成する必要があります。 詳細については、[ゲートウェイ サブネットに指定するアドレス範囲の考慮事項](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet)に関する記事を参照してください。 特定の条件下でのみ、サブネットの作成後にアドレス範囲を変更できます。 サブネット アドレス範囲を変更する方法については、[サブネットの追加、変更、削除](virtual-network-manage-subnet.md)に関するページの[サブネットの変更](#change-subnet)に関するセクションを参照してください。
    - **サブスクリプション**: [サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)を選択します。 1 つの VNet が複数のサブスクリプションにまたがることはできませんが、Azure VPN Gateway または VNET ピアリングを使用して他のサブスクリプションの VNet に接続することはできます。 VNet に接続する Azure リソースは、同じサブスクリプションに存在する必要があります。
    - **リソース グループ**: 既存の[リソース グループ](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups)を選択するか、新しいリソース グループを作成します。 VNet に接続する Azure リソースは、異なるリソース グループに存在していてもかまいません。
    - **場所:** Azure の[場所](https://azure.microsoft.com/regions/)を選択します。この場所はリージョンとも呼ばれます。 1 つの VNet が複数の Azure の場所にまたがることはできませんが、Azure VPN Gateway を使用して他の場所の VNet に接続することはできます。 VNet に接続する Azure リソースは、同じ場所に存在する必要があります。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network vnet create](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>仮想ネットワークと設定の表示

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで[ポータル](https://portal.azure.com)にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事を参照してください。
2. ポータルの上部にある "*リソースの検索*" というテキストが表示されたボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それをクリックします。
3. 表示される **[仮想ネットワーク]** ブレードで、設定を表示する VNet をクリックします。
4. 選択した VNet に対して表示されるブレードに、次の設定が表示されます。
    - **概要:** アドレス空間や DNS サーバーなど、VNet に関する情報が表示されます。 次の図は、**MyVNet** という名前の VNet の概要設定を示しています。
    
        ![ネットワーク インターフェイスの概要](./media/virtual-network-manage-network/vnet-overview.png)

      このブレードで VNet を別のサブスクリプションやリソース グループに移動することができます。 VNet を移動する方法については、[別のリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。 この記事には、リソースを移動するための前提条件や、Azure Portal、PowerShell、および Azure CLI を使用して移動する方法が記載されています。 VNet に接続されているリソースもすべて VNet と共に移動する必要があります。 
    - **アドレス空間:** VNet に割り当てられているアドレス空間が一覧表示されます。 アドレス空間の追加と削除の方法については、この記事のセクション「[アドレス空間の追加と削除](#address-spaces)」に記載された手順を参照してください。
    - **接続されているデバイス:** VNet に接続されているすべてのリソースが表示されます。 前の図で示した例では、3 つのネットワーク インターフェイスと 1 つのロード バランサーが VNet に接続されています。 新しく作成して VNet に接続したリソースはすべて一覧表示されます。 VNet に接続されているリソースを削除すると、一覧に表示されなくなります。
    - **サブネット:** VNet 内に存在するサブネットの一覧。 サブネットの追加と削除の方法については、[サブネットの追加、変更、削除](virtual-network-manage-subnet.md)に関する記事の[サブネットの追加](virtual-network-manage-subnet.md#create-subnet)および[サブネットの削除](virtual-network-manage-subnet.md#delete-subnet)に関する各セクションを参照してください。
    - **DNS サーバー:** VNet に接続されているデバイスの名前解決を Azure の内部 DNS サーバーとカスタム DNS サーバーのどちらで提供するかを指定できます。 Azure Portal を使用して VNet を作成する場合は、既定で、VNet 内の名前解決には Azure の DNS サーバーが使用されます。 DNS サーバーを変更するには、この記事のセクション「[DNS サーバーの追加、変更、削除](#dns-servers)」の手順を実行してください。 
    - **ピアリング:** サブスクリプションに既存のピアリングがある場合は、ここに表示されます。 既存のピアリング設定の表示や、ピアリングの作成、変更、削除を行うことができます。 ピアリングの詳細については、[ピアリングの概要](virtual-network-peering-overview.md)に関する記事を参照してください。
    - **プロパティ:** VNet のリソース ID や、VNet が存在するサブスクリプションなど、VNet に関する設定が表示されます。
    - **ダイアグラム:** このダイアグラムには、VNet に接続されているすべてのデバイスを視覚的に表現したものが、デバイスに関する重要な情報と共に表示されます。 いずれかのデバイスをクリックすると、このビューから直接デバイスを管理できます。
    - **Azure の一般的な設定:** Azure の一般的な設定については、[アクティビティ ログ](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)、[Access Control (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)、[タグ](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)[ロック](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、および [Automation スクリプト](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group) に関する各記事を参照してください。

**コマンド**

|**ツール**|**コマンド**|
|---|---|
|CLI|[az network vnet show](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/resourcemanager/azurerm.network/v3.8.0/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="address-spaces"></a>アドレス空間の追加と削除

アドレス空間を VNet に追加したり VNet から削除したりできます。 アドレス空間は CIDR 表記で指定する必要があります。同じ VNet 内で重複することはできません。 定義するアドレス空間は、パブリックとプライベート (RFC 1918 に準拠) のどちらでもかまいません。 パブリックとプライベートのどちらのアドレス空間を定義する場合でも、VNet 内のそのアドレス空間に到達できるのは、VNet 内から、相互接続された VNet から、および VNet に接続したオンプレミス ネットワークからだけです。 次のアドレス空間は追加できません。
    - 224.0.0.0/4 (マルチキャスト)
    - 255.255.255.255/32 (ブロードキャスト)
    - 127.0.0.0/8 (ループバック)
    - 169.254.0.0/16 (リンク ローカル)
    - 168.63.129.16/32 (内部 DNS)

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで[ポータル](https://portal.azure.com)にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事を参照してください。
2. Azure Portal の上部にある "*リソースの検索*" というテキストが表示されたボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それをクリックします。
3. 表示される **[仮想ネットワーク]** ブレードで、アドレス空間を追加または削除する仮想ネットワークをクリックします。
4. 選択した VNet に対して表示されるブレードの **[設定]** セクションで、**[アドレス空間]** をクリックします。
5. 表示されるアドレス空間のブレードで、次のオプションのいずれかを実行します。
    - **アドレス空間の追加:** ボックス内に新しいアドレス空間を入力します。 この VNet に定義されている既存のアドレス空間と重複するアドレス空間を追加することはできません。
    - **アドレス空間の削除:** アドレス空間を右クリックして、**[削除]** をクリックします。 アドレス空間にサブネットが存在する場合は、アドレス空間を削除することはできません。 アドレス空間を削除するには、アドレス空間内にあるサブネット (およびサブネットに接続されているデバイス) をすべて削除する必要があります。
6. [ **Save**] をクリックします。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|Resource Manager のみ|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers">DNS サーバーの追加、変更、削除</a>

VNet に接続されている VM はすべて、その VNet に指定された DNS サーバーに登録され、この DNS サーバーを使用して名前を解決します。 VM 内のネットワーク インターフェイス (NIC) ごとに独自の DNS サーバー設定を構成できます。 NIC に独自の DNS サーバー設定を構成すると、VNet の DNS サーバー設定が上書きされます。 NIC DNS 設定の詳細については、[ネットワーク インターフェイスのタスクと設定](virtual-network-network-interface.md#dns)に関する記事を参照してください。 VM と Cloud Services ロール インスタンスの名前解決の詳細については、「[VM とロール インスタンスの名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md)」を参照してください。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで[ポータル](https://portal.azure.com)にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事を参照してください。
2. Azure Portal の上部にある "*リソースの検索*" というテキストが表示されたボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それをクリックします。 
3. 表示される **[仮想ネットワーク]** ブレードで、DNS 設定を変更する仮想ネットワークをクリックします。
4. 選択した VNet に対して表示されるブレードの **[設定]** セクションで、**[DNS サーバー]** をクリックします。
5. 表示される DNS サーバーのブレードで、次のオプションのいずれかを実行します。
    - **(Azure で定義された) 既定:** すべてのリソースの名前とプライベート IP アドレスが Azure DNS サーバーに自動的に登録されます。 同じ VNet に接続されているリソース間で名前を解決することができます。 このオプションでは、VNet 間で名前を解決することはできません。 VNet 間で名前を解決するには、カスタムの DNS サーバーを使用する必要があります。
    - **カスタム:** 1 つ以上のサーバーを追加できます。ただし、追加できる数は Azure で VNet に設定されている上限までです。 DNS サーバー数の上限の詳細については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic)に関する記事を参照してください。 次のオプションがあります。
        - **アドレスの追加:** サーバーを仮想ネットワークの DNS サーバー一覧に追加し、その DNS サーバーを Azure に登録します。 既に DNS サーバーが Azure に登録されている場合は、表示される一覧から選択できます。 
        - **アドレスの削除:** 削除するサーバーの横にある **[X]** をクリックします。 サーバーを削除すると、この VNet 一覧からのみ削除されます。 DNS サーバー自体は Azure に登録されたままになり、他の VNet で使用できます。 
        - **DNS サーバーのアドレスの並べ替え**: 環境に適した順序で DNS サーバーが一覧表示されていることを確認することが重要です。 DNS サーバーの一覧はラウンド ロビンに対応していません。 指定した順序で使用されます。 一覧の先頭にある DNS サーバーに到達できる場合は、DNS サーバーが正しく動作しているかどうかに関係なく、その DNS サーバーを使用します。 一覧表示されたすべての DNS サーバーを削除してから、希望する順序で再度追加します。
        - **アドレスの変更**: 一覧で DNS サーバーを強調表示し、新しい名前を入力します。
6. [ **Save**] をクリックします。
7. 新しい DNS サーバー設定が割り当てられるように、VNet に接続されている VM を再起動します。 再起動されるまで、VM は現在の DNS 設定を使用し続けます。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>仮想ネットワークの削除

接続されているリソースがない場合にのみ、VNet を削除できます。 VNet 内のいずれかのサブネットに接続されたリソースがある場合は、最初に VNet 内のサブネットに接続されたリソースをすべて削除する必要があります。 リソースを削除する方法は、リソースによって異なります。 サブネットに接続されているリソースを削除する方法については、削除するリソースの種類に応じたドキュメントを参照してください。 VNet を削除するには、次の手順を実行します。

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで[ポータル](https://portal.azure.com)にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事を参照してください。
2. Azure Portal の上部にある "*リソースの検索*" というテキストが表示されたボックスに、「*仮想ネットワーク*」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それをクリックします。
3. 表示される **[仮想ネットワーク]** ブレードで、削除する VNet をクリックします。
4. 選択した VNet に対して表示されるブレードの **[設定]** セクションで **[接続されているデバイス]** をクリックして、VNet に接続されているデバイスがないことを確認します。 接続されているデバイスがある場合は、先にそのデバイスを削除してから、VNet を削除します。  接続されているデバイスがない場合は、ブレードで **[概要]** をクリックします。
5. ブレードの上部にある **[削除]** をクリックします。 
6. **[はい]** をクリックして、VNet の削除を確定します。


**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[azure network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>次のステップ

- VM を作成して VNet に接続する場合は、[VNet の作成と VM の接続](virtual-network-get-started-vnet-subnet.md#a-namecreate-vmsacreate-virtual-machines)に関する記事を参照してください。
- VNet 内のサブネット間のネットワーク トラフィックをフィルター処理する場合は、[ネットワーク セキュリティ グループの作成](virtual-networks-create-nsg-arm-pportal.md)に関する記事を参照してください。
- VNet を別の VNet にピアリングする場合は、[仮想ネットワーク ピアリングの作成](virtual-networks-create-vnetpeering-arm-portal.md#peering-vnets-in-the-same-subscription)に関する記事を参照してください。
- オンプレミスのネットワークに VNet を接続するためのオプションの詳細については、[ネットワーク ゲートウェイ](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namediagramsaconnection-topology-diagrams)に関する記事を参照してください。

