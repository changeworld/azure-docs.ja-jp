---
title: 静的プライベート IP アドレスを持つ VM を作成する - Azure portal
description: Azure portal を使用して静的プライベート IP アドレスを持つ仮想マシンを作成する方法を説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: eec4e9e07f19130732219c0932f66a1cfa0fa2b7
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367711"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-the-azure-portal"></a>Azure portal を使用して静的プライベート IP アドレスを持つ仮想マシンを作成する

仮想マシン (VM) には、指定された範囲のプライベート IP アドレスが自動的に割り当てられます。 この範囲は、VM がデプロイされているサブネットに基づいています。 VM では、その VM が削除されるまでこのアドレスが保持されます。 Azure は、次に VM に作成されているサブネットから使用可能なプライベート IP アドレスを自動的に割り当てます。 サブネットで特定の IP アドレスが必要な場合は、VM に静的 IP アドレスを割り当てます。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-virtual-machine"></a>仮想マシンの作成

仮想マシン、仮想ネットワーク、サブネットを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**仮想マシン**」と入力します。 検索結果から **[仮想マシン]** を選択します。

3. **[+ 作成]** を選択してから、 **[仮想マシン]** で **[+ 仮想マシン]** を選択します。

4. **[仮想マシンの作成]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** |   |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[新規作成]** を選択します。 </br> **[名前]** に「**myResourceGroup**」と入力します。 </br> **[OK]** を選択します。 |
    | **インスタンスの詳細** |   |
    | 仮想マシン名 | 「**myVM**」と入力します。 |
    | リージョン | **[(米国) 米国東部 2]** を選択します。 |
    | 可用性のオプション | **[インフラストラクチャ冗長は必要ありません]** を選択します。 |
    | Image | **[Windows Server 2019 Datacenter - Gen2]** を選択します。 |
    | Azure Spot インスタンス | オフのままにします。 |
    | サイズ | サイズを選択します。 |
    | **管理者アカウント** |   |
    | ユーザー名 | ユーザー名を入力します。 |
    | Password | パスワードを入力します。 |
    | パスワードの確認 | パスワードを再入力します。 |
    | パブリック受信ポート | **[選択したポートを許可する]** を選択します。 |
    | 受信ポートの選択 | **[RDP (3389)]** を選択します |

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/create-vm.png" alt-text="仮想マシンの作成のスクリーンショット。":::

    > [!WARNING]
    > インターネットから Windows Server 仮想マシンへのリモート アクセスを有効にするために、ポータル 3389 が選択されています。 運用環境のワークロードを管理するために、インターネットに対してポート 3389 を開くことはお勧めしません。 </br> Azure 仮想マシンへのアクセスをセキュリティで保護する方法については、「 **[Azure Bastion とは](../../bastion/bastion-overview.md)** 」を参照してください。

3. **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。
  
4. [ネットワーク] タブで、次を選択または入力します。

    | 設定 | 値 |
    |-|-|
    | **ネットワーク インターフェイス** |  |
    | 仮想ネットワーク | 既定のネットワーク名を受け入れます。 |
    | Subnet | 既定のサブネット構成を受け入れます。 |
    | パブリック IP | 既定のパブリック IP 構成を受け入れます。 |
    | NIC ネットワーク セキュリティ グループ | **[Basic]** を選択します |
    | パブリック受信ポート | **[選択したポートを許可する]** を選択します。 |
    | 受信ポートの選択 | **[RDP (3389)]** を選択します |

5. **[Review + create]\(レビュー + 作成\)** を選択します。 
  
6. 設定を確認し、 **[作成]** を選択します。

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="change-private-ip-address-to-static"></a>プライベート IP アドレスを静的に変更する

このセクションでは、以前に作成した仮想マシンのプライベート IP アドレスを "**動的**" から "**静的**" に変更します。

1. ポータルの上部にある検索ボックスに、「**仮想マシン**」と入力します。 検索結果から **[仮想マシン]** を選択します。

2. **[仮想マシン]** で、 **[myVM]** を選択します。

3. **[myVM]** の **[設定]** で、 **[ネットワーク]** を選択します。

4. **[ネットワーク]** ページで、 **[ネットワーク インターフェイス]** の横にあるネットワーク インターフェイスの名前を選択します。 この例での NIC の名前は **myvm472** です。

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-nic.png" alt-text="ネットワーク インターフェイスの選択のスクリーンショット。":::

5. そのネットワーク インターフェイスのプロパティの **[設定]** で、 **[IP 構成]** を選択します。

6. **[IP 構成]** ページで、 **[ipconfig1]** を選択します。

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-ip-configuration.png" alt-text="IP 構成の選択のスクリーンショット。":::

7. **[割り当て]** で **[静的]** を選択します。 **[保存]** を選択します。

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-static-assignment.png" alt-text="静的割り当ての選択のスクリーンショット。":::

    > [!NOTE]
    > **[保存]** の選択後、割り当てがまだ **[動的]** に設定されていることに気付いた場合、入力した IP アドレスは既に使用中です。 別の IP アドレスを試してください。

IP アドレスを動的に戻すには、プライベート IP アドレスの割り当てを **[動的]** に設定してから、 **[保存]** を選択します。

> [!WARNING]
> VM のオペレーティング システム内から、Azure VM に割り当てられている *プライベート* IP を静的に割り当てないでください。 [VM に多数の IP アドレスを割り当てる](virtual-network-multiple-ip-addresses-portal.md)ときなど、必要な場合にのみ、プライベート IP の静的割り当てを行います。 
>
>オペレーティング システム内でプライベート IP アドレスを手動で設定する場合は、そのアドレスが、Azure の[ネットワーク インターフェイス](virtual-network-network-interface-addresses.md#change-ip-address-settings)に割り当てられているプライベート IP アドレスと一致していることを確認します。 そのようにしないと、VM への接続が失われる可能性があります。 詳細については、[プライベート IP アドレス](virtual-network-network-interface-addresses.md#private)設定に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループとそれに含まれるすべてのリソースが不要になったら、それらを削除します。

1. ポータル上部の **[検索]** ボックスに「**myResourceGroup**」と入力します。 検索結果に **[myResourceGroup]** が表示されたら、それを選択します。

2. **[リソース グループの削除]** を選択します。

3. **[TYPE THE RESOURCE GROUP NAME:]\(リソース グループ名を入力してください:\)** に「**myResourceGroup**」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

- [静的パブリック IP アドレス](public-ip-addresses.md#ip-address-assignment)の詳細を参照してください。

- Azure での[パブリック IP アドレス](public-ip-addresses.md#public-ip-addresses)について詳しく学習する。

- すべての[パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。

- [プライベート IP アドレス](private-ip-addresses.md)と Azure 仮想マシンへの[静的プライベート IP アドレス](virtual-network-network-interface-addresses.md#add-ip-addresses)の割り当ての詳細を確認します。

