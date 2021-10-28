---
title: Azure Virtual Machine を使用してパブリック IP アドレスを管理する
titleSuffix: Azure Virtual Network
description: Azure Virtual Machines でパブリック IP アドレスを使用する方法と、構成を変更する方法について説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: d7c1f9faeb196f73fd6b336421591cfd895407fe
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249003"
---
# <a name="manage-a-public-ip-address-with-an-azure-virtual-machine"></a>Azure Virtual Machine を使用してパブリック IP アドレスを管理する

パブリック IP アドレスは Standard と Basic の 2 つの SKU で使用できます。 SKU の選択によって、IP アドレスの機能が決まります。 SKU によって、IP アドレスに関連付けることができるリソースが決まります。  

Azure Virtual Machines は、Azure の主要コンピューティング サービスです。 ユーザーは、Linux または Windows 仮想マシンを作成できます。 仮想マシンへの着信接続のために、パブリック IP アドレスを仮想マシンに割り当てることができます。 

仮想マシンの構成には、パブリック IP アドレスは必要ありません。

この記事では、サブスクリプションで既存のパブリック IP を使用して Azure Virtual Machine を作成する方法について説明します。 仮想マシンにパブリック IP アドレスを追加する方法について説明します。 IP アドレスを変更します。 最後に、パブリック IP を削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- サブスクリプション内の 2 つの Standard SKU のパブリック IP アドレス。 IP アドレスはどのリソースにも関連付けることはできません。 Standard SKU のパブリック IP アドレスの作成の詳細については、[パブリック IP の作成 - Azure portal](./create-public-ip-portal.md) に関するページを参照してください。
    - この記事の例では、新しいパブリック IP アドレスに **myStandardPublicIP-1** と **myStandardPublicIP-2** という名前を付けます。
- サブスクリプション内の **インターネット** のルーティング優先設定を持つ 1 つの Standard SKU のパブリック IP アドレス。 **インターネット** ルーティング優先設定を持つパブリック IP を作成する方法の詳細については、「[Azure portal を使用してパブリック IP アドレスのルーティング優先設定を構成する](./routing-preference-portal.md)」を参照してください。
    - この記事の例では、新しいパブリック IP アドレスに **myStandardPublicIP-3** という名前を付けます。
## <a name="create-virtual-machine-existing-public-ip"></a>仮想マシンの既存のパブリック IP を作成する

このセクションでは、仮想マシンを作成します。 前提条件で作成した IP アドレスを仮想マシンのパブリック IP として選択します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**仮想マシン**」と入力します。

3. 検索結果から **[仮想マシン]** を選択します。

4. **[+ 追加]** 次に **[仮想マシン]** を選択します。

5. **[仮想マシンの作成]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** |   |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[新規作成]** を選択します。 </br> 「**myResourceGroupVM**」と入力します。 </br> **[OK]** を選択します。 |
    | **インスタンスの詳細** |   |
    | 仮想マシン名 | 「**myVM**」と入力します。 |
    | リージョン | **[(米国) 米国西部 2]** を選択します。 | 
    | 可用性のオプション | **[インフラストラクチャ冗長は必要ありません]** を選択します。 |
    | Image | **[Windows Server 2019 Datacenter - Gen1]** を選択します。 |
    | Azure Spot インスタンス  | 既定値のオフのままにします。 |
    | サイズ | 仮想マシンのサイズを選択します |
    | **管理者アカウント** |   |
    | ユーザー名 | ユーザー名を入力します。 |
    | Password | パスワードを入力します。 |
    | パスワードの確認 | パスワードを確認入力します。 |
    | **受信ポートの規則** |   |
    | パブリック受信ポート | 既定の **[選択したポートを許可する]** のままにします。 |
    | 受信ポートの選択 | 既定値の **[RDP (3389)]** のままにします。 |

6. **[ネットワーク]** タブを選択するか、または **[次へ: ディスク]** 、さらに **[次へ: ネットワーク]** を選択します。

7. **[ネットワーク]** タブで、次の情報を入力または選択します。

    | 設定 | [値] |
    | ------- | ----- |
    | **ネットワーク インターフェイス** |   |
    | 仮想ネットワーク | 既定値の **(新規) myResourceGroupVM vnet** のままにします。 |
    | Subnet | 既定値の **(新規) 既定 (10.1.0.0/24)** のままにします。 |
    | パブリック IP | **[myStandardPublicIP-1]** を選択します。 |
    | NIC ネットワーク セキュリティ グループ | 既定値の **[Basic]** のままにします。 |
    | パブリック受信ポート | 既定の **[選択したポートを許可する]** のままにします。 |
    | 受信ポートの選択 | 既定値の **[RDP (3389)]** のままにします。 |

6. **[確認と作成]** タブを選択するか、青い **[確認と作成]** ボタンを選択します。

7. **［作成］** を選択します

> [!NOTE]
> これは、Azure Virtual Machine の簡単なデプロイです。 詳細な構成とセットアップについては、「[クイック スタート:Azure Portal で Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-portal.md)」を参照してください
>
> Azure Virtual Machines の詳細については、「[Azure の Windows 仮想マシン](../../virtual-machines/windows/overview.md)」を参照してください

## <a name="change-public-ip-address"></a>パブリック IP アドレスを変更する

このセクションでは、仮想マシンの既定のパブリック IP 構成に関連付けられているパブリック IP アドレスを変更します。

1. ポータルの上部にある検索ボックスに、「**仮想マシン**」と入力します。

2. 検索結果から **[仮想マシン]** を選択します。

3. **[仮想マシン]** で、 **[myVM]** を選択します。

4. **[myVM]** の **[設定]** で、 **[ネットワーク]** を選択します。

5. **[ネットワーク]** で、VM の **[ネットワーク インターフェイス]** を選択します。 NIC の名前の先頭には VM の名前が付けられ、末尾にはランダムな数字が付けられます。  この例では **[myvm793]** です。

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="ネットワーク インターフェイスを選択します。" border="true":::

6. ネットワーク インターフェイスの **[設定]** で、 **[IP 構成]** を選択します。

7. **[IP 構成]** で、 **[ipconfig1]** を選択します。  

    :::image type="content" source="./media/configure-public-ip-vm/change-ipconfig.png" alt-text="ipconfig を選択して IP アドレスを変更します。" border="true":::

1. **[ipconfig1]** の **[パブリック IP アドレス]** で **[myStandardPublicIP-2]** を選択します。

7. **[保存]** を選択します。

## <a name="add-public-ip-configuration"></a>パブリック IP 構成の追加

このセクションでは、仮想マシンにパブリック IP 構成を追加します。 

複数の IP アドレスを追加する方法の詳細については、「[Azure Portal を使用して仮想マシンに複数の IP アドレスを割り当てる](./virtual-network-multiple-ip-addresses-portal.md)」を参照してください。 

両方の種類のルーティング優先設定を使用する方法の詳細については、「[仮想マシンの両方のルーティング優先設定オプションを構成する](./routing-preference-mixed-network-adapter-portal.md)」を参照してください。

1. ポータルの上部にある検索ボックスに、「**仮想マシン**」と入力します。

2. 検索結果から **[仮想マシン]** を選択します。

3. **[仮想マシン]** で、 **[myVM]** を選択します。

4. **[myVM]** の **[設定]** で、 **[ネットワーク]** を選択します。

5. **[ネットワーク]** で、VM の **[ネットワーク インターフェイス]** を選択します。 NIC の名前の先頭には VM の名前が付けられ、末尾にはランダムな数字が付けられます。  この例では **[myvm793]** です。

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="ネットワーク インターフェイスを選択します。" border="true":::

6. ネットワーク インターフェイスの **[設定]** で、 **[IP 構成]** を選択します。

7. **[IP 構成]** で **[+ 追加]** を選択します。

8. **[名前]** に「**ipconfig2**」と入力します。

9. **[パブリック IP アドレス]** で **[関連付け]** を選択します。

10. **[パブリック IP アドレス]** で **[myStandardPublicIP-3]** を選択します。

11. **[OK]** を選択します。

## <a name="remove-public-ip-address-association"></a>パブリック IP アドレスの関連付けを削除する

このセクションでは、ネットワーク インターフェイスから、パブリック IP アドレスを削除します。 このプロセスの後の仮想マシンは、外部接続で使用できなくなります。

1. ポータルの上部にある検索ボックスに、「**仮想マシン**」と入力します。

2. 検索結果から **[仮想マシン]** を選択します。

3. **[仮想マシン]** で、 **[myVM]** を選択します。

4. **[myVM]** の **[設定]** で、 **[ネットワーク]** を選択します。

5. **[ネットワーク]** で、VM の **[ネットワーク インターフェイス]** を選択します。 NIC の名前の先頭には VM の名前が付けられ、末尾にはランダムな数字が付けられます。  この例では **[myvm793]** です。

    :::image type="content" source="./media/configure-public-ip-vm/network-interface.png" alt-text="ネットワーク インターフェイスを選択します。" border="true":::

6. ネットワーク インターフェイスの **[設定]** で、 **[IP 構成]** を選択します。

7. **[IP 構成]** で、 **[ipconfig1]** を選択します。  

    :::image type="content" source="./media/configure-public-ip-vm/change-ipconfig.png" alt-text="ipconfig を選択して IP アドレスを変更します。" border="true":::

8. **[パブリック IP アドレスの設定]** で **[関連付けの解除]** を選択します。

9. **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

この記事では、仮想マシンを作成し、既存のパブリック IP を使用する方法について説明しました。 既定の IP 構成のパブリック IP を変更しました。 最後に、インターネット ルーティング優先設定を使用して、パブリック IP 構成をファイアウォールに追加しました。

- Azure のパブリック IP アドレスの詳細については、「[パブリック IP アドレス](./public-ip-addresses.md)」を参照してください。