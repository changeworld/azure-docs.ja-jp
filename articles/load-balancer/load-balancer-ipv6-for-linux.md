---
title: Linux VM の DHCPv6 の構成
titlesuffix: Azure Load Balancer
description: Linux VM の DHCPv6 の構成方法について説明します。
services: load-balancer
documentationcenter: na
author: KumudD
keywords: ipv6, azure load balancer, デュアル スタック, パブリック IP, ネイティブ ipv6, モバイル, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ea1ef845f55fbdadeea1992e167ef6568572abc9
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141715"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Linux VM の DHCPv6 の構成


Azure Marketplace の一部の Linux 仮想マシン イメージでは、動的ホスト構成プロトコル バージョン 6 (DHCPv6) が既定で構成されていません。 IPv6 をサポートするには、使用している Linux OS のディストリビューションで DHCPv6 を構成する必要があります。 Linux ディストリビューションによって使用するパッケージが異なるため、さまざまな方法で DHCPv6 が構成されます。

> [!NOTE]
> Azure Marketplace の最近の SUSE Linux と CoreOS のイメージは DHCPv6 に対応するように事前構成されています。 これらのイメージを使用する場合、追加の変更は不要です。

このドキュメントでは、DHCPv6 を有効にしてお使いの Linux 仮想マシンが IPv6 アドレスを取得できるようにする方法について説明します。

> [!WARNING]
> ネットワーク構成ファイルが適切に編集されていないと、VM へのネットワーク アクセスが失われるおそれがあります。 構成の変更は、運用環境以外のシステムでテストすることをお勧めします。 この記事の手順は、Azure Marketplace 内の最新バージョンの Linux イメージでテストされています。 詳細な手順については、お使いのバージョンの Linux のドキュメントを参照してください。

## <a name="ubuntu"></a>Ubuntu

1. */etc/dhcp/dhclient6.conf* ファイルを編集し、次の行を追加します。

        timeout 10;

2. eth0 インターフェースのネットワーク構成を、次のように編集します。

   * **Ubuntu 12.04 および 14.04** では、*/etc/network/interfaces.d/eth0.cfg* ファイルを編集します。 
   * **Ubuntu 16.04** では、*/etc/network/interfaces.d/50-cloud-init.cfg* ファイルを編集します。

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. IPv6 アドレスを次のように更新します。

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. */etc/dhcp/dhclient6.conf* ファイルを編集し、次の行を追加します。

        timeout 10;

2. */etc/network/interfaces* ファイルを編集し、次の構成を追加します。

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. IPv6 アドレスを次のように更新します。

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL、CentOS、および Oracle Linux

1. */etc/sysconfig/network* ファイルを編集し、次のパラメーターを追加します。

        NETWORKING_IPV6=yes

2. */etc/sysconfig/network-scripts/ifcfg-eth0* ファイルを編集し、次の 2 つのパラメーターを追加します。

        IPV6INIT=yes
        DHCPV6C=yes

3. IPv6 アドレスを次のように更新します。

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 と openSUSE 13

Azure の最近の SUSE Linux Enterprise Server (SLES) と openSUSE のイメージは、DHCPv6 に対応するように事前構成されています。 これらのイメージを使用する場合、追加の変更は不要です。 古い SUSE イメージまたはカスタム SUSE イメージに基づく VM がある場合は、次の手順に従います。

1. 必要に応じて、 `dhcp-client` パッケージをインストールします。

    ```bash
    sudo zypper install dhcp-client
    ```

2. */etc/sysconfig/network/ifcfg-eth0* ファイルを編集し、次のパラメーターを追加します。

        DHCLIENT6_MODE='managed'

3. IPv6 アドレスを次のように更新します。

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 と openSUSE Leap

Azure の最近の SLES と openSUSE のイメージは、DHCPv6 に対応するように事前構成されています。 これらのイメージを使用する場合、追加の変更は不要です。 古い SUSE イメージまたはカスタム SUSE イメージに基づく VM がある場合は、次の手順に従います。

1. */etc/sysconfig/network/ifcfg-eth0* ファイルを編集し、`#BOOTPROTO='dhcp4'` パラメーターを次の値に置き換えます。

        BOOTPROTO='dhcp'

2. */etc/sysconfig/network/ifcfg-eth0* ファイルに、次のパラメーターを追加します。

        DHCLIENT6_MODE='managed'

3. IPv6 アドレスを次のように更新します。

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Azure の最近の CoreOS のイメージは、DHCPv6 に対応するように事前構成されています。 これらのイメージを使用する場合、追加の変更は不要です。 古い CoreOS イメージまたはカスタム CoreOS イメージに基づく VM がある場合は、次の手順に従います。

1. */etc/systemd/network/10_dhcp.network* ファイルを次のように編集します。

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. IPv6 アドレスを次のように更新します。

    ```bash
    sudo systemctl restart systemd-networkd
    ```
