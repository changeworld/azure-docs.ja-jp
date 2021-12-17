---
title: パブリック IP アドレスをアップグレードする - Azure portal
description: この記事では、Azure portal を使用して Basic SKU のパブリック IP アドレスをアップグレードする方法について説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: a40806f6328c513fe6a835ca3a81b46ad962b86d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224951"
---
# <a name="upgrade-a-public-ip-address-using-the-azure-portal"></a>Azure portal を使用してパブリック IP アドレスをアップグレードする

Azure パブリック IP アドレスは Basic または Standard のいずれかの SKU で作成されます。 SKU によって、割り当て方法、機能サポート、関連付けることができるリソースなどの機能が決まります。 

このアーティクルでは、Azure portal を使用して、静的な Basic SKU のパブリック IP アドレスを Standard SKU にアップグレードする方法について説明します。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* サブスクリプション内の静的な Basic SKU のパブリック IP アドレス。 詳細については、[パブリック IP アドレスの作成 - Azure portal](./create-public-ip-portal.md#create-a-basic-sku-public-ip-address) を参照してください。

## <a name="upgrade-public-ip-address"></a>パブリック IP アドレスのアップグレード

このセクションでは、Azure portal にサインインし、静的な Basic SKU のパブリック IP を Standard SKU にアップグレードします。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**パブリック IP**」と入力します。

3. 検索結果から **パブリック IP アドレス** を選択します。

4. **パブリック IP アドレス** で、**myBasicPublicIP** または アップグレードするIP アドレスを選択します。

5. **myBasicPublicIP** の [overview (概要)] セクションの上部にあるアップグレード バナーを選択します。

    :::image type="content" source="./media/public-ip-upgrade-portal/upgrade-ip-portal.png" alt-text="Azure portal での Basic IP アドレスのアップグレード" border="true":::

    > [!NOTE]
    > アップグレードする Basic パブリック IP には、静的割り当ての種類が必要です。 動的に割り当てられた IP アドレスをアップグレードしようとすると、IP をアップグレードできないという警告が表示されます。

6.  **[I acknowledge (同意する)** ] チェックボックスをオンにします。 **[アップグレード]** を選択します。

    > [!WARNING]
    > Basic パブリック IP を Standard SKU にアップグレードすると、元に戻すことはできません。 Basic から Standard SKU にアップグレードされたパブリック IP には引き続き、[可用性ゾーン](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)の保証はありません。

## <a name="verify-upgrade"></a>アップグレードの確認

このセクションでは、パブリック IP アドレスが Standard sku になっていることを確認します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**パブリック IP**」と入力します。

3. 検索結果から **パブリック IP アドレス** を選択します。

4. **パブリック IP アドレス** で、**myBasicPublicIP** または アップグレードするIP アドレスを選択します。

5. **[Overview (概要)]** セクションに SKU が **Standard** として表示されていることを確認します。

    :::image type="content" source="./media/public-ip-upgrade-portal/verify-upgrade-ip.png" alt-text="パブリック IP アドレスが Standard SKU であることを確認します。" border="true":::

## <a name="next-steps"></a>次のステップ

このアーティクルでは、Basic SKU のパブリック IP アドレスを Standard SKU にアップグレードしました。

Azure のパブリック IP アドレスの詳細については、次を参照してください。

- [Azure でのパブリック IP アドレス](public-ip-addresses.md)
- [パブリック IP の作成 - Azure portal](./create-public-ip-portal.md)