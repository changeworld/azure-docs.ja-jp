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
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 86304fc7776f49c999924b8609f2d26120cee372
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549077"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>ラボ仮想マシンでブラウザー接続を有効にする 

DevTest Labs は [Azure Bastion](https://docs.microsoft.com/azure/bastion/) と統合されたことにより、ブラウザーを使用して仮想マシンに接続できます。 最初に、ラボ仮想マシンでブラウザー接続を有効にする必要があります。

ラボの所有者は、ブラウザーを使用したすべてのラボ仮想マシンへのアクセスを有効にできます。 追加のクライアント、エージェント、ソフトウェアは必要ありません。 Azure Bastion では、Azure portal で TLS を経由して、仮想マシンへのセキュリティで保護されたシームレスな RDP または SSH の直接接続が提供されます。 Azure Bastion 経由で接続する場合、仮想マシンにパブリック IP アドレスは必要ありません。 詳細については、「[Azure Bastion とは](../bastion/bastion-overview.md)」を参照してください。

> [!NOTE]
> ラボ仮想マシンでのブラウザー接続の有効化はプレビュー段階です。

この記事では、ラボ仮想マシンでブラウザー接続を有効にする方法について説明します。

## <a name="prerequisites"></a>前提条件 
既存のラボの仮想ネットワークに Bastion ホストをデプロイする**か、(または)** Bastion が構成されている VNet にラボを接続します。 

VNet で Bastion ホストをデプロイする方法については、「[Azure Bastion ホスト (プレビュー) を作成する](../bastion/bastion-create-host-portal.md)」を参照してください。 Bastion ホストを作成するときに、ラボの仮想ネットワークを選択します。 

Bastion が構成されている VNet にラボを接続する方法については、「[Azure DevTest Labs で仮想ネットワークを構成する](devtest-lab-configure-vnet.md)」を参照してください。 Bastion ホストがデプロイされている VNet と、その中にある **AzureBastionSubnet** を選択します。 詳細な手順を以下に示します。 

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側のナビゲーション メニューで、 **[すべてのサービス]** を選択します。 
1. 一覧で **[DevTest Labs]** を選択します。 
1. ラボの一覧で、"*目的のラボ*" を選択します。 

    > [!NOTE]
    > Azure Bastion は現在プレビュー段階です。 それは、以下のリージョンに限り提供されます。米国西部、米国東部、西ヨーロッパ、米国中南部、オーストラリア東部、および東日本。 そのため、ラボがこれらのいずれかのリージョンに存在しない場合は、いずれかのリージョンにラボを作成してください。 
1. 左側のメニューの **[設定]** セクションで、 **[構成とポリシー]** を選択します。 
1. **[仮想ネットワーク]** を選択します。
1. ツールバーの **[追加]** を選択します。 
1. Bastion ホストがデプロイされている **VNet** を選択します。 
1. 次のサブネットを選択します:**AzureBastionSubnet**。 

    ![Subnet](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. **[仮想マシン作成時に使用]** オプションを選択します。 
1. ツールバーの **[保存]** を選択します。 
1. ラボに古い VNet がある場合は、* *...* と **[削除]** を選択して削除します。 

## <a name="enable-browser-connection"></a>ブラウザー接続の有効化 

ラボ内に Bastion が構成されている VNet を追加すると、ラボの所有者は、ラボ仮想マシンでブラウザー接続を有効にできます。

ラボ仮想マシンでブラウザー接続を有効にするには、次の手順に従ってください。

1. Azure portal で、"*目的のラボ*" に移動します。
1. **[Configuration and policies (構成とポリシー)]** を選択します。
1. **[設定]** で、 **[Browser connect (Preview)]\(ブラウザー接続 (プレビュー)\)** を選択します。

![ブラウザー接続の有効化](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>次の手順
ブラウザーを使用して VM に接続する方法については、次の記事を参照してください。[ブラウザーを使用して仮想マシンに接続する](connect-virtual-machine-through-browser.md)