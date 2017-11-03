---
title: "Azure Virtual Machines に対するネットワーク インターフェイスの追加と削除 | Microsoft Docs"
description: "仮想マシンにネットワーク インターフェイスを追加する、または仮想マシンからネットワーク インターフェイスを削除する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: jdial
ms.openlocfilehash: 7df1dfbea8c985907d5330819dc1e7bf1578aafa
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2017
---
# <a name="add-network-interfaces-to-or-remove-from-virtual-machines"></a>仮想マシンに対するネットワーク インターフェイスの追加と削除

VM を作成する際に既存のネットワーク インターフェイスを追加する方法と、停止 (割り当て解除) 状態の既存の VM に対してネットワーク インターフェイスの追加または削除を行う方法について説明します。 ネットワーク インターフェイスは、Azure 仮想マシン (VM) がインターネット、Azure、およびオンプレミスのリソースと通信できるようにします。 VM には、1 つまたは複数のネットワーク インターフェイスを実装できます。 

ネットワーク インターフェイスに対して IP アドレスの追加、変更、または削除が必要な場合は、[ネットワーク インターフェイスの IP アドレスの管理](virtual-network-network-interface-addresses.md)についての記事を参照してください。 ネットワーク インターフェイスを作成、変更、または削除する必要がある場合は、[ネットワークインターフェイスの管理](virtual-network-network-interface.md)に関する記事を参照してください。

## <a name="before"></a>開始する前に

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- 各サイズの Linux VM および Windows VM でサポートされるネットワーク インターフェイスの数については、[Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の VM のサイズに関する記事をご覧ください。
- Azure アカウントを使用して、Azure [Portal](https://portal.azure.com)、Azure コマンド ライン インターフェイス (CLI)、または Azure PowerShell にログインします。 まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure PowerShell をインストールして構成します](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 最新バージョンの Azure PowerShell コマンドレットがインストールされていることを確認してください。 PowerShell コマンドのヘルプとサンプルを表示するには、「`get-help <command> -full`」と入力します。
- Azure コマンド ライン インターフェイス (CLI) のコマンドを使ってこの記事のタスクを実行する場合は、[Azure CLI をインストールして構成します](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 最新バージョンの Azure CLI がインストールされていることを確認してください。 CLI コマンドのヘルプを表示するには、「`az <command> --help`」と入力します。 CLI とその前提条件をインストールする代わりに、Azure Cloud Shell を使うことができます。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Azure CLI が事前にインストールされており、アカウントで使用できるように構成されています。 Cloud Shell を使うには、[ポータル](https://portal.azure.com)の上部にある [Cloud Shell**>_**] ボタンをクリックします。

## <a name="about"></a>ネットワーク インターフェイスと VM について

VM を作成する際に既存のネットワーク インターフェイスを VM に追加 (アタッチ) できます。ただし、その時点で別の VM にアタッチされていないネットワーク インターフェイスに限られます。 既存の VM に対するネットワーク インターフェイスの追加と削除 (デタッチ) は、VM が停止 (割り当て解除) 状態にあるときに実行できます。 Azure ポータルを使用して VM を作成すると、ネットワーク インターフェイスが既定の設定で自動的に作成されます。 ポータルでは次のことは実行できません。

- VM の作成時に追加する既存のネットワーク インターフェイスを指定する
- 複数のネットワーク インターフェイスを持つ VM を作成する
- ネットワーク インターフェイスの名前を指定する (ポータルでは、ネットワーク インターフェイスは既定の名前で作成されます)

Azure PowerShell または CLI を使用すると、ポータルでは使用できないすべての属性を使用してネットワーク インターフェイスまたは VM を作成できます。 この後のセクションのタスクを実行する前に、次の制約と動作について検討してください。

- すべてのサイズの VM でネットワーク インターフェイスを少なくとも 2 つサポートしますが、一部のサイズの VM では 3 つ以上のネットワーク インターフェイスをサポートします。 以前は、一部のサイズの VM ではネットワーク インターフェイスを 1 つしかサポートしていませんでした。 各サイズの VM でサポートされるネットワーク インターフェイスの数については、[Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の VM のサイズに関する記事をご覧ください。 
- 以前は、複数のネットワーク インターフェイスをサポートしていて、少なくとも 2 つのネットワーク インターフェイスが作成された VM にのみ、ネットワーク インターフェイスを追加できました。 1 つのネットワーク インターフェイスが作成された VM は、VM のサイズが複数のネットワーク インターフェイスをサポートしている場合でも、ネットワーク インターフェイスを追加することはできませんでした。 逆に、ネットワーク インターフェイスを削除できるのは、少なくとも 3 つのネットワーク インターフェイスが存在する VM からのみでした。これは、少なくとも 2 つのネットワーク インターフェイスが作成された VM には、常に少なくとも 2 つのネットワーク インターフェイスが存在する必要があったためです。 これらの制約は、どちらも当てはまらなくなっています。 任意の数のネットワーク インターフェイスを持つ VM を作成でき (VM のサイズでサポートされる最大数まで)、VM に常に少なくとも 1 つのネットワーク インターフェイスが存在する限り、(停止 (割り当て解除) 状態にある VM から) 任意の数のネットワーク インターフェイスを追加または削除できるようになりました。
- 既定では、VM 内の最初のネットワーク インターフェイスは、*プライマリ* ネットワーク インターフェイスとして定義されます。 VM 内の他のすべてのネットワーク インターフェイスは、*セカンダリ* ネットワーク インターフェイスになります。
- プライマリ ネットワーク インターフェイスには Azure DHCP サーバーによって既定のゲートウェイが割り当てられますが、セカンダリ ネットワーク インターフェイスへの割り当てはありません。 セカンダリ ネットワーク インターフェイスには既定のゲートウェイが割り当てられないため、既定では、サブネットの外部にあるリソースと通信することはできません。 セカンダリ ネットワーク インターフェイスがサブネットの外部にあるリソースと通信できるようにするには、「[複数のネットワーク インターフェイスを持つ仮想マシンのオペレーティング システム内でのルーティング](#routing-within-a-virtual-machine-operating-system-with-multiple-network-interfaces)」を参照してください。
- 既定では、VM からのすべての送信トラフィックは、プライマリ ネットワーク インターフェイスのプライマリ IP 構成に割り当てられた IP アドレスで送信されます。 VM のオペレーティング システム内で送信トラフィックに使用する IP アドレスを制御できますが、既定では、プライマリ ネットワーク インターフェイスを経由します。
- 以前は、同じ可用性セット内のすべての VM は、アタッチされるネットワーク インターフェイスを 1 つまたは複数に統一する必要がありました。 現在は、VM のサイズでサポートされている最大数までのネットワーク インターフェイスがアタッチされた VM が同じ可用性セットに存在できます。 ただし、VM を可用性セットに追加できるのは、VM の作成時のみです。 可用性セットについて詳しくは、[Azure での VM の可用性の管理](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)に関する記事をご覧ください。
- 同じ VM にアタッチされている複数のネットワーク インターフェイスは、VNet 内の異なるサブネットに接続できますが、異なる VNet に接続することはできません。
- 任意のプライマリまたはセカンダリ ネットワーク インターフェイスの任意の IP 構成の任意の IP アドレスを Azure Load Balancer バックエンド プールに追加できます。 以前は、プライマリ ネットワーク インターフェイスのプライマリ IP アドレスのみをバックエンド プールに追加できました。 IP アドレスと IP 構成について詳しくは、[IP アドレスの追加、変更、削除](virtual-network-network-interface-addresses.md)に関する記事をご覧ください。
- VM を削除しても VM にアタッチされたネットワーク インターフェイスは削除されません。 VM を削除すると、ネットワーク インターフェイスは VM からデタッチされます。 デタッチされたネットワーク インターフェイスは、別の VM に追加することも削除することもできます。
- ネットワーク インターフェイスにプライベート IPv6 アドレスが割り当てられている場合は、VM の作成時に VM にアタッチすることができます。 VM の作成後に、IPv6 アドレスが割り当てられているネットワーク インターフェイスを VM にアタッチすることはできません。 仮想マシンの作成時に、プライベート IPv6 アドレスが割り当てられたネットワーク インターフェイスをアタッチする場合、VM サイトがサポートしているネットワーク インターフェイス数にかかわらず、その仮想マシンにアタッチできるのはそのネットワーク インターフェイスのみです。 IP アドレスをネットワーク インターフェイスに割り当てる方法については、[ネットワーク インターフェイスの IP アドレス](virtual-network-network-interface-addresses.md)に関する記事を参照してください。

## <a name="vm-create"></a>既存のネットワーク インターフェイスを新しい VM に追加する

ポータルで VM を作成すると、ネットワーク インターフェイスが既定の設定で自動的に作成され、VM にアタッチされます。 新しい VM への既存のネットワーク インターフェイスの追加と、複数のネットワーク インターフェイスがアタッチされた VM の作成は、Azure ポータルでは実行できません。 この 2 つは CLI または PowerShell を使用して行うことができます。 ネットワーク インターフェイスは、作成する VM のサイズでサポートされている数まで VM に追加できます。 各サイズの VM でサポートされるネットワーク インターフェイスの数については、[Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の VM のサイズに関する記事をご覧ください。 その時点で別の VM にアタッチされているネットワーク インターフェイスを VM に追加することはできません。 ネットワーク インターフェイスの作成の詳細については、[ネットワークインターフェイスの管理](virtual-network-network-interface.md#create-a-network-interface)に関する記事を参照してください。

### <a name="routing-within-a-virtual-machine-operating-system-with-multiple-network-interfaces"></a>複数のネットワーク インターフェイスを持つ仮想マシンのオペレーティング システム内でのルーティング

Azure では、既定のゲートウェイが、仮想マシンにアタッチされた最初の (プライマリ) ネットワーク インターフェイスに割り当てられます。 Azure では、既定のゲートウェイは、仮想マシンにアタッチされた追加の (セカンダリ) ネットワーク インターフェイスに割り当てられません。 したがって、既定では、セカンダリ ネットワーク インターフェイスのサブネット外のリソースと通信することはできません。 ただし、通信を有効にする手順はオペレーティング システムによってそれぞれ異なりますが、セカンダリ ネットワーク インターフェイスはサブネットの外部にあるリソースと通信することができます。

### <a name="windows"></a>Windows

Windows コマンド プロンプトから次の手順を実行します。

1. `route print` コマンドを実行します。このコマンドは、2 つのネットワーク インターフェイスがアタッチされた仮想マシンについて、次の出力のような内容を返します。

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    この例で、**Microsoft Hyper-V Network Adapter #4** (インターフェイス 7) は、既定のゲートウェイが割り当てられていないセカンダリ ネットワーク インターフェイスです。

2. コマンド プロンプトから `ipconfig` コマンドを実行して、セカンダリ ネットワーク インターフェイスに割り当てられている IP アドレスを確認します。 この例では、インターフェイス 7 には 192.168.2.4 が割り当てられています。 セカンダリ ネットワーク インターフェイスに対して、既定のゲートウェイ アドレスは返されません。

3. セカンダリ ネットワーク インターフェイスのサブネットの外部にあるアドレスに宛てたすべてのトラフィックをサブネットのゲートウェイにルーティングするには、次のコマンドを実行します。

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    サブネットのゲートウェイ アドレスは、サブネットに対して定義されているアドレス範囲内の最初の IP アドレス (.1 で終わる) です。 すべてのトラフィックをサブネットの外部にルーティングするわけではない場合は、特定の宛先へのルートを個別に追加することができます。 たとえば、セカンダリ ネットワーク インターフェイスからネットワーク 192.168.3.0 にトラフィックをルーティングするだけの場合は、次のコマンドを入力します。

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. ネットワーク 192.168.3.0 上にあるリソースとの通信が成功したことを確認するには、次のコマンドを入力します。インターフェイス 7 (192.168.2.4) を使用して 192.168.3.4 に対して ping が実行されます。

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    次のコマンドを使用して、ping を実行しているデバイスの Windows ファイアウォールを介して ICMP を開くことが必要な場合があります。
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. 追加したルートがルート テーブルにあることを確認するには、`route print` コマンドを入力します。次のテキストのような出力が返されます。

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    リストされたルートのうち、**Gateway** の値が *192.168.1.1* になっているルートは、プライマリ ネットワーク インターフェイスの既定のルートです。 **Gateway** の値が *192.168.2.1* になっているルートは、追加したルートです。

### <a name="linux"></a>Linux

既定の動作では弱いホスト ルーティングが使用されるので、同じサブネット内のリソース間でセカンダリ ネットワーク インターフェイスのトラフィックを制限することをお勧めします。 セカンダリ ネットワーク インターフェイスのサブネットの外部との通信を必要とする場合は、仮想マシンが特定のネットワーク インターフェイスを介してトラフィックを送受信することを許可するルーティング規則を作成する必要があります。 そうしないと、たとえば、eth1 に属しているトラフィックを、定義された既定のルートで正しく処理できません。 ルーティング規則の構成方法については、「[Configure Linux for multiple NICs](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics)」 (複数の NIC に対して Linux を構成する) を参照してください。

> [!WARNING]
> ネットワーク インターフェイスにプライベート IPv6 アドレスが割り当てられている場合、仮想マシンの作成時に、そのネットワーク インターフェイスのみを仮想マシンに追加できます。 仮想マシンにアタッチされたネットワーク インターフェイスに IPv6 アドレスが割り当てられている場合、仮想マシンの作成時、または仮想マシンの作成後に、その仮想マシンに複数のネットワーク インターフェイスをアタッチすることはできません。 IP アドレスをネットワーク インターフェイスに割り当てる方法については、[ネットワーク インターフェイスの IP アドレス](virtual-network-network-interface-addresses.md)に関する記事を参照してください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>既存のネットワーク インターフェイスを既存の VM に追加する

ネットワーク インターフェイスは、ネットワーク インターフェイスを追加する VM のサイズがサポートする数まで、VM に追加できます。 各サイズの VM でサポートされるネットワーク インターフェイスの数については、[Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の VM のサイズに関する記事をご覧ください。 ネットワーク インターフェイスを追加する VM は、追加対象の複数のネットワーク インターフェイスをサポートし、停止 (割り当て解除) 状態になっている必要があります。 その時点で別の VM にアタッチされているネットワーク インターフェイスを VM に追加することはできません。 Azure ポータルを使用して既存の VM にネットワーク インターフェイスを追加することはできません。 既存の VM にネットワーク インターフェイスを追加するには、CLI または PowerShell を使用する必要があります。 

Azure では、既定のゲートウェイが、仮想マシンにアタッチされた最初の (プライマリ) ネットワーク インターフェイスに割り当てられます。 Azure では、既定のゲートウェイは、仮想マシンにアタッチされた追加の (セカンダリ) ネットワーク インターフェイスに割り当てられません。 したがって、既定では、セカンダリ ネットワーク インターフェイスのサブネット外のリソースと通信することはできません。 しかし、セカンダリ ネットワーク インターフェイスは、そのサブネット外のリソースと通信できます。 セカンダリ ネットワーク インターフェイスがサブネットの外部にあるリソースと通信する必要がある場合は、「[複数のネットワーク インターフェイスを持つ仮想マシンのオペレーティング システム内でのルーティング](#routing-within-a virtual-machine-operating-system-with-multiple-network-interfaces)」を参照してください。

> [!WARNING]
> プライベート IPv6 アドレスが割り当てられたネットワーク インターフェイスを既存の仮想マシンに追加することはできません。 仮想マシンの作成時に、プライベート IPv6 アドレスが割り当てられた 1 つのネットワーク インターフェイスのみを仮想マシンに追加できます。 IP アドレスをネットワーク インターフェイスに割り当てる方法については、[ネットワーク インターフェイスの IP アドレス](virtual-network-network-interface-addresses.md)に関する記事を参照してください。

|ツール|コマンド|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add) (参照) または [detailed steps](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (参照) or [detailed steps](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="vm-view-nic"></a>VM のネットワーク インターフェイスを表示する

VM にアタッチされているネットワーク インターフェイスを表示して、各ネットワーク インターフェイスの構成の詳細や、各ネットワーク インターフェイスに割り当てられている IP アドレスを確認できます。 

1. ご利用のサブスクリプションの所有者、共同作成者、またはネットワーク共同作成者いずれかのロールが割り当てられているアカウントで、[Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールの割り当ての詳細については、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」を参照してください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*仮想マシン*" と入力します。 検索結果に **[仮想マシン]** が表示されたら、それをクリックします。
3. 表示される **[仮想マシン]** ブレードで、ネットワーク インターフェイスを表示する VM の名前をクリックします。
4. 選択した VM に対して表示される [仮想マシン] ブレードの **[設定]** セクションで、**[ネットワーク]** をクリックします。 ネットワーク インターフェイスの設定とそれらを変更する方法については、[ネットワーク インターフェイスの管理](virtual-network-network-interface.md)に関する記事を参照してください。 ネットワーク インターフェイスに割り当てられる IP アドレスの追加、変更、または削除については、[IP アドレスの管理](virtual-network-network-interface-addresses.md)に関する記事を参照してください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a> ネットワーク インターフェイスを VM から削除する

ネットワーク インターフェイスを削除 (デタッチ) する VM は、停止 (割り当て解除) 状態になっており、2 つ以上のネットワーク インターフェイスがアタッチされている必要があります。 任意のネットワーク インターフェイスを削除できますが、削除後の VM には、常に 1 つ以上のネットワーク インターフェイスがアタッチされている必要があります。 プライマリ ネットワーク インターフェイスを削除した場合は、VM に最も長い時間アタッチされているネットワーク インターフェイスにプライマリ属性が自動的に割り当てられます。 

1. ご利用のサブスクリプションの所有者、共同作成者、またはネットワーク共同作成者いずれかのロールが割り当てられているアカウントで、[Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールの割り当ての詳細については、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」を参照してください。
2. Azure Portal 上部に "*リソースの検索*" というテキストが表示されたボックスがあります。そこに "*仮想マシン*" と入力します。 検索結果に **[仮想マシン]** が表示されたら、それをクリックします。
3. 表示される **[仮想マシン]** ブレードで、ネットワーク インターフェイスを削除する VM の名前をクリックします。
4. 選択した VM に対して表示される [仮想マシン] ブレードの **[設定]** セクションで、**[ネットワーク]** をクリックします。 ネットワーク インターフェイスの設定とそれらを変更する方法については、[ネットワーク インターフェイスの管理](virtual-network-network-interface.md)に関する記事を参照してください。 ネットワーク インターフェイスに割り当てられる IP アドレスの追加、変更、または削除については、[IP アドレスの管理](virtual-network-network-interface-addresses.md)に関する記事を参照してください。
5. **[X Detach network interface]\(X ネットワーク インターフェイスのデタッチ\)** をクリックします。
6. ドロップダウン リストからデタッチするネットワーク インターフェイスを選択し、**[OK]** をクリックします。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove) (参照) または [detailed steps](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (参照) または [detailed steps](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="next-steps"></a>次のステップ
複数のネットワーク インターフェイスまたは IP アドレスを持つ VM を作成する方法については、次の記事をご覧ください。

**コマンド**

|タスク|ツール|
|---|---|
|複数 NIC を持つ VM の作成|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|複数の IPv4 アドレスが割り当てられた 1 つの NIC VM の作成|[CLI](virtual-network-multiple-ip-addresses-cli.md)、[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|プライベート IPv6 アドレスが割り当てられた 1 つの NIC VM の作成 (Azure Load Balancer の背後)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Azure Resource Manager テンプレート](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
