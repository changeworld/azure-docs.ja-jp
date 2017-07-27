---
title: "Azure パブリック IP アドレスの作成、変更、削除 | Microsoft Docs"
description: "パブリック IP アドレスの作成、変更、削除の方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 47237fe499cd9244266487cd97f6be0d2cb2e977
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017


---

# <a name="create-change-or-delete-public-ip-addresses"></a>パブリック IP アドレスの作成、変更、削除

パブリック IP アドレスとその作成、変更、削除の方法について説明します。 パブリック IP アドレスは、変更可能な独自の設定を備えたリソースです。 パブリック IP アドレスを他の Azure リソースに割り当てることで次のことが可能となります。
- Azure Virtual Machines (VM)、Azure Virtual Machine Scale Sets、Azure VPN Gateway、インターネット接続 Azure Load Balancer などの各種リソースに対する受信インターネット接続。
- ネットワーク アドレス変換 (NAT) されていないインターネットへの送信接続。 たとえば VM は、それ自体のパブリック IP アドレスを取得せずにそのアドレスを Azure によって NAT 変換することで、インターネットに対して送信方向の通信を行うことができます。 Azure リソースからの送信接続の詳細については、[送信用接続の詳細](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

この記事では、Azure Resource Manager デプロイメント モデルによってデプロイされたパブリック IP アドレスを操作する方法について説明します。

## <a name="before"></a>開始する前に

この記事のいずれかのセクションの手順を実行する前に、次のタスクを完了します。

- [Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事を確認して、パブリック IP アドレスの制限について学習します。
- Azure Portal、Azure コマンド ライン インターフェイス (CLI)、または Azure PowerShell に Azure アカウントでログインします。 まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- PowerShell コマンドを使用して、この記事のタスクを実行する場合、[Azure PowerShell のインストールと設定方法](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順を実行して、Azure PowerShell をインストールして設定します。 最新バージョンの Azure PowerShell コマンドレットがインストールされていることを確認してください。 PowerShell コマンドのヘルプとサンプルを表示するには、「`get-help <command> -full`」と入力します。
- Azure コマンド ライン インターフェイス (CLI) コマンドを使用して、この記事のタスクを実行する場合は、[Azure CLI のインストールと構成の方法](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順を実行して、Azure CLI をインストールして構成します。 最新バージョンの Azure CLI がインストールされていることを確認してください。 CLI コマンドのヘルプを表示するには、「`az <command> --help`」と入力します。

パブリック IP アドレスには、わずかですが料金が発生します。 料金については、「[IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses)」ページを参照してください。 

## <a name="create"></a>パブリック IP アドレスの作成

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事を参照してください。
2. Azure Portal 上部に *[リソースの検索]* というテキストが表示されたボックスがあります。そこに「*パブリック IP アドレス*」と入力します。 検索結果に **[パブリック IP アドレス]** が表示されたら、それをクリックします。
3. 表示された **[パブリック IP アドレス]** ブレードの **[+ 追加]** をクリックします。
4. 表示された **[パブリック IP アドレスの作成]** ブレードで以下の設定の値を入力するか選択し、**[作成]** をクリックします。

    |Setting|必須|詳細|
    |---|---|---|
    |名前|あり|選択したリソース グループ内で一意となる名前を使用してください。|
    |IP アドレスの割り当て|あり|**[動的]:** 動的アドレスの割り当ては、VM の NIC にパブリック IP アドレスが関連付けられた後、VM の初回起動後に行われます。 NIC の接続先 VM が停止 (割り当て解除) された場合、動的アドレスは変化する場合があります。 VM が再起動された場合や、停止されても割り当て解除されなければ、アドレスは変化しません。 **[静的]:** 静的アドレスは、パブリック IP アドレスの作成時に割り当てられます。 VM が停止 (割り当て解除) 状態になっても静的アドレスは変化しません。 アドレスが解放されるのは、NIC が削除されたときだけです。 NIC の作成後に、割り当て方法を変更することができます。|
    |アイドル タイムアウト (分)|いいえ|クライアントからキープアライブ メッセージを送信しなくても TCP 接続または HTTP 接続が開いたまま維持される時間 (分)。|
    |DNS 名ラベル|いいえ|名前の作成先となる Azure の場所 (すべてのサブスクリプション、すべての顧客) で一意となるようにしてください。 指定した名前のリソースに接続できるよう、その名前と IP アドレスが Azure のパブリック DNS サービスによって自動的に登録されます。 指定した名前に *location.cloudapp.azure.com* (location は選択した場所) が自動的に付加されて完全修飾 DNS 名が作成されます。|
    |[サブスクリプション]|あり|パブリック IP アドレスを関連付けるリソースと同じ[サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)内に存在する必要があります。|
    |リソース グループ|あり|所属する[リソース グループ](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)は、パブリック IP アドレスを関連付けるリソースと同じであっても異なっていてもかまいません。|
    |場所|あり|パブリック IP アドレスを関連付けるリソースと同じ[場所](https://azure.microsoft.com/regions) (リージョンとも呼ばれる) に存在する必要があります。|

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network public-ip-create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="change"></a>パブリック IP アドレスの表示、設定変更、削除

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事を参照してください。
2. Azure Portal 上部に *[リソースの検索]* というテキストが表示されたボックスがあります。そこに「*パブリック IP アドレス*」と入力します。 検索結果に **[パブリック IP アドレス]** が表示されたら、それをクリックします。
3. 表示された **[パブリック IP アドレス]** ブレードで、表示、設定変更、削除の対象となるパブリック IP アドレスの名前をクリックします。
4. 表示されたパブリック IP アドレスのブレードで、実行する操作 (削除または変更) に応じて次のいずれかを実行します。
    - **表示**: ブレードの [概要] セクションに、関連付けられているネットワーク インターフェイス (アドレスがネットワーク インターフェイスに関連付けられている場合) など、パブリック IP アドレスの主要な設定が表示されます。
    - **削除**: パブリック IP アドレスを削除するには、ブレードの **[概要]** セクションで **[削除]** をクリックします。 現在 IP 構成に関連付けられているアドレスは削除できません。 アドレスが構成に関連付けられている場合は、**[関連付け解除]** をクリックすると、IP 構成からアドレスの関連付けが解除されます。
    - **変更**: **[構成]** をクリックします。 この記事の「[パブリック IP アドレスの作成](#create)」セクションにある手順 4. の情報を参考にして設定を変更します。 割り当て方法を静的から動的に変更する場合はまず、IP 構成からパブリック IP アドレスの関連付けを解除する必要があります。 そのうえで、動的な割り当て方法に変更し、**[関連付け]** をクリックして、同じ IP 構成または異なる IP 構成に IP アドレスを関連付けるか、関連付けを解除したままの状態にすることができます。 パブリック IP アドレスの関連付けを解除するには、**[概要]** セクションの **[関連付け解除]** をクリックします。

>[!WARNING]
>割り当て方法を静的から動的に変更すると、パブリック IP アドレスに割り当てられた IP アドレスが失われます。 DNS 名ラベル (定義した場合) に対する静的アドレスまたは動的アドレスのマッピングは Azure パブリック DNS サーバーによって管理されますが、動的 IP アドレスは、VM が停止 (割り当て解除) 状態になった後、起動されたときに変化する可能性があります。 アドレスが変化しないようにするには、静的 IP アドレスを割り当ててください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network public-ip-list](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#list) (パブリック IP アドレスの一覧表示)、[az network public-ip-show](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#show) (設定の表示)、[az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) (更新)、[az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) (削除)|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) (パブリック IP アドレス オブジェクトの取得とその設定の表示)、[Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) (設定の更新)、[Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) (削除)|

## <a name="next-steps"></a>次のステップ
次の Azure リソースの作成時にパブリック IP アドレスを割り当てます。

- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシンまたは [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシン
- [インターネットに接続する Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure VPN Gateway を使用したサイト間接続](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

