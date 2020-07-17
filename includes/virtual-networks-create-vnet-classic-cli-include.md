---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: cd686e1bf62bbd7f37f61ced767e92918edf919c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181093"
---
## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Azure CLI を使用して従来の VNet を作成する方法
Azure CLI を使用すると、Windows、Linux、または OSX を実行している任意のコンピューターのコマンド プロンプトから Azure のリソースを管理することができます。

1. Azure CLI を初めて使用する場合は、「 [Azure CLI のインストール](../articles/cli-install-nodejs.md) 」を参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。
2. VNet とサブネットを作成するには、次のように **azure network vnet create** コマンドを実行します。
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    予想される出力:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**。 作成する VNet の名前です。 このシナリオでは、*TestVNet* です。
   * **-e (または --address-space)** 。 VNet のアドレス空間です。 このシナリオでは、*192.168.0.0* です。
   * **-i (または -cidr)** 。 CIDR 形式のネットワーク マスクです。 このシナリオでは、*16* です。
   * **-n (または --subnet-name**)。 最初のサブネットの名前です。 このシナリオでは、*FrontEnd* です。
   * **-p (または --subnet-start-ip)** 。 サブネットの開始 IP アドレス、またはサブネット アドレス空間です。 このシナリオでは、*192.168.1.0* です。
   * **-r (または --subnet-cidr)** 。 サブネットの CIDR 形式のネットワーク マスクです。 このシナリオでは、*24* です。
   * **-l (または --location)** 。 VNet が作成される Azure リージョンです。 このシナリオでは、*Central US (米国中部)* です。
3. サブネットを作成するには、次のように **azure network vnet subnet create** コマンドを実行します。
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    上記のコマンドで想定される出力は次のとおりです。
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t (または --vnet-name)** 。 サブネットの作成先となる VNet の名前です。 このシナリオでは、*TestVNet* です。
   * **-n (または --name)** 。 新しいサブネットの名前です。 このシナリオでは、*BackEnd* です。
   * **-a (または --address-prefix)** 。 サブネットの CIDR ブロックです。 このシナリオでは、*192.168.2.0/24* です。
4. 新しい VNet のプロパティを表示するには、次のように **azure network vnet show** コマンドを実行します。
   
            azure network vnet show
   
    上記のコマンドで想定される出力は次のとおりです。
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

