---
title: 'VPN Gateway: ゲートウェイの IP アドレスの設定を変更する: Azure portal'
description: この記事では、Azure Portal を使用してローカル ネットワーク ゲートウェイの IP アドレスのプレフィックスを変更する手順について説明します
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/16/2020
ms.author: cherylmc
ms.openlocfilehash: af3513c4a4f3b3187e85c65de51ad2e6e2d7279c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983178"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Azure Portal を使用したローカル ネットワーク ゲートウェイの設定の変更

ローカル ネットワーク ゲートウェイの AddressPrefix または GatewayIPAddress の設定が変わることがあります。 この記事では、ローカル ネットワーク ゲートウェイの設定を変更する方法を説明します。 これらの設定は別の方法で変更することもできます。その場合は、次のボックスから別のオプションを選択してください。

接続を削除する前に、定義された PSK を取得するために、接続デバイスの構成をダウンロードしてしておくことをお勧めします。 こうすると、もう一方の側で再定義する必要はありません。

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>ローカル ネットワーク ゲートウェイ構成

下のスクリーンショットは、パブリック IP アドレス エンドポイントを使用したローカル ネットワーク ゲートウェイ リソースの **[構成]** ページを示しています。

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/ip-address.png" alt-text="ローカル ネットワーク ゲートウェイの構成 - IP アドレス":::

FQDN エンドポイントの場合、同じ構成ページが次のようになります。

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/fqdn.png" alt-text="ローカル ネットワーク ゲートウェイの構成 - IP アドレス":::

## <a name="modify-the-gateway-ip-address"></a><a name="ip"></a>ゲートウェイの IP アドレスを変更する

接続先の VPN デバイスのパブリック IP アドレスが変更された場合には、ローカル ネットワーク ゲートウェイを変更してアドレスの変更を反映する必要があります。

1. ローカル ネットワーク ゲートウェイ リソースの **[設定]** セクションで、**[構成]** をクリックします。
2. **[IP アドレス]** ボックスの IP アドレスを変更します。
3. **[保存]** をクリックして設定を保存します。

## <a name="modify-the-gateway-fqdn"></a><a name="fqdn"></a>ゲートウェイ FQDN の変更

接続先の VPN デバイスの FQDN (完全修飾ドメイン名) が変更された場合には、ローカル ネットワーク ゲートウェイを変更してアドレスの変更を反映する必要があります。

1. ローカル ネットワーク ゲートウェイ リソースの **[設定]** セクションで、**[構成]** をクリックします。
2. **[FQDN]** ボックスでドメイン名を変更します。
3. **[保存]** をクリックして設定を保存します。

> ![注記] FQDN エンドポイントと IP アドレス エンドポイントの間でローカル ネットワーク ゲートウェイを変更することはできません。 このローカル ネットワーク ゲートウェイに関連付けられている接続をすべて削除し、新しいエンドポイント (IP アドレスまたは FQDN) で新規作成し、接続を再作成する必要があります。

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP アドレスのプレフィックスを変更する

### <a name="to-add-additional-address-prefixes"></a>アドレス プレフィックスを追加するには:

1. ローカル ネットワーク ゲートウェイ リソースの **[設定]** セクションで、**[構成]** をクリックします。
2. *[その他のアドレス範囲の追加]* ボックスに、IP アドレス空間を追加します。
3. **[Save]** をクリックして設定を保存します。

### <a name="to-remove-address-prefixes"></a>アドレス プレフィックスを削除するには:

1. ローカル ネットワーク ゲートウェイ リソースの **[設定]** セクションで、**[構成]** をクリックします。
2. 削除するプレフィックスが含まれる行の **[...]** をクリックします。
3. **[削除]** をクリックします。
4. **[Save]** をクリックして設定を保存します。

## <a name="modify-bgp-settings"></a><a name="bgp"></a>BGP 設定を変更する

### <a name="to-add-or-update-bgp-settings"></a>BGP 設定を追加または更新するには:

1. ローカル ネットワーク ゲートウェイ リソースの **[設定]** セクションで、**[構成]** をクリックします。
2. このローカル ネットワーク ゲートウェイの BGP 構成を表示したり、更新したりするには、 **[BGP 設定の構成]** を選択します。
3. 対応するフィールドで自律システム番号か BGP ピア IP アドレスを追加するか更新します。
4. **[Save]** をクリックして設定を保存します。

### <a name="to-remove-bgp-settings"></a>BGP 設定を削除するには:

1. ローカル ネットワーク ゲートウェイ リソースの **[設定]** セクションで、**[構成]** をクリックします。
2. 既存の BGP ASN と BGP ピア IP アドレスを削除するには、 **[BGP 設定の構成]** の選択を解除します。
3. **[Save]** をクリックして設定を保存します。

## <a name="next-steps"></a>次の手順

ゲートウェイの接続を確認することができます。 「 [Verify a gateway connection (ゲートウェイの接続を確認する)](vpn-gateway-verify-connection-resource-manager.md)」を参照してください。
