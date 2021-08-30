---
title: パブリック IP アドレス プレフィックスの作成 - Azure portal
titlesuffix: Azure Virtual Network
description: Azure portal を使用して、パブリック IP アドレス プレフィックスを作成する方法について説明します。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/06/2021
ms.author: allensu
ms.openlocfilehash: 3a1dfefb034816038af4401a22ee87f7f1914c54
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598453"
---
# <a name="create-a-public-ip-address-prefix-using-the-azure-portal"></a>Azure portal を使用してパブリック IP アドレス プレフィックスを作成する

パブリック IP アドレス プレフィックスとその作成、変更、削除の方法について説明します。 パブリック IP アドレス プレフィックスは、Standard SKU のパブリック IP アドレスの連続した範囲です。 

パブリック IP アドレス リソースを作成するときに、プレフィックスから静的なパブリック IP アドレスを割り当てて、そのアドレスを仮想マシン、ロード バランサーやその他のリソースに関連付けることができます。 詳細については、[パブリック IP アドレス プレフィックスの概要](public-ip-address-prefix.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

- まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。

## <a name="create-a-public-ip-address-prefix"></a>パブリック IP アドレス プレフィックスの作成

このセクションでは、Azure portal でパブリック IP プレフィックスを作成します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**パブリック IP**」と入力します。

3. 検索結果から、 **[パブリック IP プレフィックス]** を選択します。

4. **[+ 作成]** を選択します。

5. **[パブリック IP プレフィックスの作成]** で、 **[基本]** タブに次の値を入力するか、項目を選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** |   |
    | サブスクリプション | サブスクリプションを選択します。 |
    | リソース グループ | **[新規作成]** を選択します。 </br> 「**myResourceGroup**」と入力します。 </br> **[OK]** を選択します。 |
    | **インスタンスの詳細** |   |
    | 名前 | 「**myPublicIPPrefix**」と入力します。 |
    | リージョン | **[(米国) 米国西部 2]** を選択します。 |
    | IP バージョン | 既定値の **[IPv4]** のままにします。 |
    | プレフィックス サイズ | プレフィックスのサイズを選択します。 |

    :::image type="content" source="./media/create-public-ip-prefix-portal/create-public-ip-prefix.png" alt-text="Azure portal でのパブリック IP アドレス プレフィックスの作成" border="true":::
    
    > [!NOTE]
    >IPv6 プレフィックスを作成するには、 **[IP バージョン]** に **[IPv6]** を選択します。

     :::image type="content" source="./media/create-public-ip-prefix-portal/create-public-ipv6-prefix.png" alt-text="Azure portal でのパブリック IPv6 アドレス プレフィックスの作成" border="true":::

6. **[確認と作成]** タブを選択するか、ページの下部にある青色の **[確認と作成]** ボタンを選択します。

7. **［作成］** を選択します

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>プレフィックスから静的パブリック IP アドレスを作成する
プレフィックスを作成したら、プレフィックスから静的 IP アドレスを作成する必要があります。 このセクションでは、以前に作成したプレフィックスから静的 IP アドレスを作成します。

1. ポータルの上部にある検索ボックスに、「**パブリック IP**」と入力します。

2. 検索結果から、 **[パブリック IP プレフィックス]** を選択します。

3. **[パブリック IP プレフィックス]** で、 **[myPublicIPPrefix]** を選択します。

4. **[myPublicIPPrefix]** の **[概要]** で、 **[IP アドレスの追加]** を選択します。

    :::image type="content" source="./media/create-public-ip-prefix-portal/add-ip-address.png" alt-text="Azure portal でのプレフィックスからのパブリック IP アドレスの作成" border="true":::

5. **[名前]** に「**myPublicIP**」と入力します。 

6. その他の選択項目は、デフォルトのままにしておきます。

7. **[追加]** を選択します。

    >[!NOTE]
    >プレフィックスの範囲から割り当てることができるのは、Standard SKU で作成された静的パブリック IP アドレスのみです。 パブリック IP アドレスの SKU の詳細については、「[パブリック IP アドレス](./public-ip-addresses.md#public-ip-addresses)」をご覧ください。

8. **[設定]** で **[パブリック IP アドレス]** を選択し、作成された IP アドレスを表示します。
## <a name="delete-a-prefix"></a>プレフィックスを削除する

このセクションでは、プレフィックスを表示または削除する方法を説明します。

1. ポータルの上部にある検索ボックスに、「**パブリック IP**」と入力します。

2. 検索結果から、 **[パブリック IP プレフィックス]** を選択します。

3. **[パブリック IP プレフィックス]** で、 **[myPublicIPPrefix]** を選択します。

4. **[概要]** セクションで **[削除]** を選択します。

    >[!NOTE]
    >プレフィックス内のアドレスがパブリック IP アドレス リソースに関連付けられている場合は、まずパブリック IP アドレス リソースを削除する必要があります。 [パブリック IP アドレスの削除](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address)に関するページを参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

ここでは、パブリック IP プレフィックスを作成し、さらにそのプレフィックスからパブリック IP を作成しました。 

パブリック IP プレフィックスでの処理が終わったら、リソース グループと、それに含まれているすべてのリソースを削除します。

1. **myResourceGroup** を検索して選択します。

2. **[リソース グループの削除]** を選択します。

3. **[TYPE THE RESOURCE GROUP NAME]\(リソース グループ名を入力してください\)** に「**myResourceGroup**」と入力し、 **[削除]** を選択します。
## <a name="next-steps"></a>次のステップ

- [パブリック IP プレフィックス](public-ip-address-prefix.md)を使用するシナリオと利点について説明します
