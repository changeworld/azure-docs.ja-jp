---
title: Azure Bastion でパブリック IP アドレスを管理する
titleSuffix: Azure Virtual Network
description: Azure Bastion でパブリック IP アドレスを使用する方法と、構成を変更する方法について説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: bd89926102c13937843b6bdbaba43e5915f3fddb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234001"
---
# <a name="manage-a-public-ip-address-with-azure-bastion"></a>Azure Bastion でパブリック IP アドレスを管理する

パブリック IP アドレスは Standard と Basic の 2 つの SKU で使用できます。 SKU の選択によって、IP アドレスの機能が決まります。 SKU によって、IP アドレスに関連付けできるリソースが決まります。

Azure Bastion は、仮想ネットワーク内の仮想マシンにセキュリティで保護された管理接続を提供するためにデプロイされます。 Azure Bastion サービスを使用すると、仮想ネットワーク内の VM に対して安全かつシームレスに RDP と SSH を実行することができます。 Azure Bastion を使用すると、VM のパブリック IP を公開することなく接続できます。 接続は、Azure portal から直接行われ、追加のクライアント、エージェント、またはソフトウェアの一部を必要としません。 Azure Bastion は、Standard SKU のパブリック IP アドレスをサポートしています。

Azure Bastion ホストの構成には、パブリック IP アドレスが必要です。

この記事では、サブスクリプションで既存のパブリック IP を使用して Azure Bastion ホストを作成する方法について説明します。 Azure Bastion は、作成後のパブリック IP アドレスの変更をサポートしていません。  Azure Bastion は、パブリック IP のプレフィックスをサポートしていません。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- サブスクリプション内の 1 つの Standard SKU のパブリック IP アドレス。 これらの IP アドレスはどのリソースにも関連付けできません。 Standard SKU のパブリック IP アドレス作成の詳細については、[パブリック IP の作成 - Azure portal](./create-public-ip-portal.md) を参照してください。
    - このアーティクルの例では、新しいパブリック IP アドレスに **myStandardPublicIP** という名前を付けます。

## <a name="create-azure-bastion-using-existing-ip"></a>既存の IP を使用して Azure bastion を作成する

このセクションでは、Azure Bastion ホストを作成します。 前提条件で作成した IP アドレスを bastion ホストのパブリック IP として選択します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**Bastion**」と入力します。

3. 検索結果にある **[Bastion]** を選択します。

4. **[+ 作成]** を選択します。

5. **[要塞の作成]** で、次の情報を入力するか選択します。

    | 設定 | 値 | 
    | ------- | ----- |
    | **プロジェクトの詳細** |   |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[新規作成]** を選択します。 </br> **[名前]** に「**myResourceGroup**」と入力します。 </br> **[OK]** を選択します。 |
    | **インスタンスの詳細** |  |
    | 名前 | 「**myBastionHost**」と入力します |
    | リージョン | **[(米国) 米国西部 2]** を選択します。 |
    | **仮想ネットワークを構成する** |   |
    | 仮想ネットワーク | **[新規作成]** を選択します。 </br> **[名前]** に「**myVNet**」と入力します。 </br> 既定のアドレス空間は **10.4.0.0/16** のままにします。 </br> 既定のサブネットは **10.4.0.0/24** のままにします。 </br> **既定** のサブネットの下のテキストボックスに「**AzureBastionSubnet**」と入力します。 </br> [アドレス範囲] に、「**10.4.1.0/27**」と入力します。 </br> **[OK]** を選択します。 |
    | Subnet | **[AzureBastionSubnet]** を選択します。 |
    | **パブリック IP アドレス** |   |
    | パブリック IP アドレス | **[既存のものを使用]** を選択します。 |
    | パブリック IP アドレスの選択 | **[myStandardPublicIP]** を選択します。 |

6. **[確認と作成]** タブを選択するか、青い **[確認と作成]** ボタンを選択します。

7. **［作成］** を選択します

> [!NOTE]
> Azure Bastion の詳細については、「[Azure Bastion とは](../../bastion/bastion-overview.md)」を参照してください

## <a name="change-or-remove-public-ip-address"></a>パブリック IP アドレスを変更または削除する

Azure Bastion は、作成後のパブリック IP アドレスの変更をサポートしていません。

## <a name="more-information"></a>詳細情報

* Azure Bastion 経由で接続するときに、仮想マシン上に別のパブリック IP を用意する必要はありません。 トラフィックは、まず Bastion のパブリック IP にルーティングされます。 次に、RDP または SSH 接続は、Bastion によって仮想マシンに関連付けられたプライベート IP アドレスにルーティングされます。 

## <a name="caveats"></a>注意事項

* 現時点では、パブリック IPv6 アドレスは Azure Bastion でサポートされていません。  

## <a name="next-steps"></a>次のステップ

この記事では、Azure bastion を作成し、既存のパブリック IP を使用する方法について説明しました。 

- Azure Bastion の詳細については、「[Azure Bastion とは](../../bastion/bastion-overview.md)」を参照してください。
- Azure のパブリック IP アドレスの詳細については、「[パブリック IP アドレス](./public-ip-addresses.md)」を参照してください。