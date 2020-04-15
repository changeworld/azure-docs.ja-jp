---
title: Azure DevTest Labs 仮想マシンでブラウザー接続を有効にする | Microsoft Docs
description: DevTest Labs が Azure Bastion と統合されました。これにより、ラボの所有者は、すべてのラボ仮想マシンへのブラウザーを使用したアクセスを有効にできます。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: 074d512767769fe434cd4a6d4f4e8e6a88f7ed2a
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549115"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>ラボ仮想マシンでブラウザー接続を有効にする 
DevTest Labs は [Azure Bastion](https://docs.microsoft.com/azure/bastion/) と統合されたことにより、ブラウザーを使用して仮想マシンに接続できます。 最初に、ラボ仮想マシンでブラウザー接続を有効にする必要があります。

ラボの所有者は、ブラウザーを使用したすべてのラボ仮想マシンへのアクセスを有効にできます。 追加のクライアント、エージェント、ソフトウェアは必要ありません。 Azure Bastion では、Azure portal で TLS を経由して、仮想マシンへのセキュリティで保護されたシームレスな RDP または SSH の直接接続が提供されます。 Azure Bastion 経由で接続する場合、仮想マシンにパブリック IP アドレスは必要ありません。 詳細については、「[Azure Bastion とは](../bastion/bastion-overview.md)」を参照してください。


この記事では、ラボ仮想マシンでブラウザー接続を有効にする方法について説明します。

## <a name="prerequisites"></a>前提条件 
既存のラボの仮想ネットワークに Bastion ホストをデプロイするか、 **(または)** Bastion が構成されている仮想ネットワークにラボを接続します。 

仮想ネットワークで Bastion ホストをデプロイする方法については、「[Azure Bastion ホストを作成する](../bastion/bastion-create-host-portal.md)」を参照してください。 Bastion ホストを作成するときに、ラボの仮想ネットワークを選択します。 

最初に、Bastion 仮想ネットワークに 2 番目のサブネットを作成する必要があります。これは、AzureBastionSubnet の中に Bastion 以外のリソースを作成することが許可されていないためです。 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Bastion 仮想ネットワークに 2 番目のサブネットを作成する
Azure Bastion サブネットにラボ VM を作成することはできません。 次の図に示すように、Bastion 仮想ネットワーク内に別のサブネットを作成します。

![Azure Bastion 仮想ネットワーク内の 2 番目のサブネット](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>サブネットで VM の作成を有効にする
ここで、次の手順に従って、このサブネットで VM の作成を有効にします。 

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側のナビゲーション メニューで、 **[すべてのサービス]** を選択します。 
1. 一覧で **[DevTest Labs]** を選択します。 
1. ラボの一覧で、"*目的のラボ*" を選択します。 

    > [!NOTE]
    > Azure Bastion は、次のリージョンで一般公開されています。米国西部、米国東部、西ヨーロッパ、米国中南部、オーストラリア東部、および東日本。 そのため、ラボがこれらのいずれかのリージョンに存在しない場合は、いずれかのリージョンにラボを作成してください。 
    
1. 左側のメニューの **[設定]** セクションで、 **[構成とポリシー]** を選択します。 
1. **[仮想ネットワーク]** を選択します。
1. ツールバーの **[追加]** を選択します。 
1. Bastion ホストがデプロイされている**仮想ネットワーク**を選択します。 
1. VM 用のサブネットを選択します。これは **AzureBastionSubnet** ではなく、以前作成した別のものです。 一覧の下部にサブネットが表示されない場合は、ページを閉じて再度開きます。 

    ![サブネットで VM の作成を有効にする](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. **[仮想マシン作成時に使用]** オプションを選択します。 
1. ツールバーの **[保存]** を選択します。 
1. ラボに古い仮想ネットワークがある場合は、* *...* および **[削除]** を選択して削除します。 

## <a name="enable-browser-connection"></a>ブラウザー接続の有効化 

ラボ内に Bastion が構成されている仮想ネットワークを追加すると、ラボの所有者は、ラボ仮想マシンでブラウザー接続を有効にできます。

ラボ仮想マシンでブラウザー接続を有効にするには、次の手順に従ってください。

1. Azure portal で、"*目的のラボ*" に移動します。
1. **[Configuration and policies (構成とポリシー)]** を選択します。
1. **[設定]** で、 **[ブラウザー接続]** を選択します。 このオプションが表示されない場合は、 **[構成ポリシー]** ページを閉じて、再度開きます。 

    ![ブラウザー接続の有効化](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>次の手順
ブラウザーを使用して VM に接続する方法については、次の記事を参照してください。[ブラウザーを使用して仮想マシンに接続する](connect-virtual-machine-through-browser.md)
