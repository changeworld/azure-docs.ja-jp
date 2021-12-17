---
title: VPN ゲートウェイを使用してパブリック IP アドレスを管理する
titleSuffix: Azure Virtual Network
description: VPN ゲートウェイでパブリック IP アドレスを使用する方法と、構成を変更する方法について説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: fa553360085a2793dba43e91cb08fde74eebf52e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233925"
---
# <a name="manage-a-public-ip-address-with-a-vpn-gateway"></a>VPN ゲートウェイを使用してパブリック IP アドレスを管理する

パブリック IP アドレスは Standard と Basic の 2 つの SKU で使用できます。 SKU の選択によって、IP アドレスの機能が決まります。 SKU によって、IP アドレスに関連付けできるリソースが決まります。 

VPN ゲートウェイは、仮想ネットワーク ゲートウェイの一種で、パブリック インターネットを介して Azure 仮想ネットワークとオンプレミスの場所の間で暗号化されたトラフィックを送信するために使用されます。 VPN ゲートウェイを使用すると、Microsoft ネットワークを経由して Azure 仮想ネットワーク間で暗号化されたトラフィックを送信することもできます。 各仮想ネットワークには VPN ゲートウェイを 1 つだけ作成できます。 VPN ゲートウェイは、その SKU に応じて、Standard SKU と Basic SKU のパブリック IP アドレスをサポートしています。 パブリック IP プレフィックスはサポートされていません。

VPN ゲートウェイの構成には、パブリック IP アドレスが必要です。 パブリック IP アドレスは、VPN の外部接続ポイントとして使用されます。 

このアーティクルでは、サブスクリプション内の既存のパブリック IP を使用して VPN ゲートウェイを作成する方法について説明します。 

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- サブスクリプション内の 1 つの Standard SKU のパブリック IP アドレス。 これらの IP アドレスはどのリソースにも関連付けできません。 Standard SKU のパブリック IP アドレス作成の詳細については、[パブリック IP の作成 - Azure portal](./create-public-ip-portal.md) を参照してください。
    - このアーティクルの例では、新しいパブリック IP アドレスに **myStandardPublicIP** という名前を付けます。

## <a name="create-vpn-gateway-existing-public-ip"></a>VPN ゲートウェイの既存のパブリック IP を作成する

このセクションでは、VPN ゲートウェイを作成します。 前提条件で作成した IP アドレスを VPN ゲートウェイのパブリック IP として選択します。

### <a name="create-virtual-network"></a>Create virtual network

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**仮想ネットワーク**」と入力します。

3. 検索結果で、**[仮想ネットワーク]** を選択します。

4. **[+ 作成]** を選択します。

5. **[Create virtual network (仮想ネットワーク リンクの追加)]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** |   |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[新規作成]** を選択します。 </br> 「**myResourceGroupVPN**」と入力します。 </br> **[OK]** を選択します。 |
    | **インスタンスの詳細** |   |
    | 名前 | 「**myVNet**」と入力します。 |
    | リージョン | **[(米国) 米国西部 2]** を選択します。 |
    
6. **[Review + create (確認と作成)]** タブを選択するか、青い **[Review + create (確認と作成)]** ボタンを選択します。

7. **［作成］** を選択します

8. ポータルの上部にある検索ボックスに、「**仮想ネットワーク**」と入力します。

9. 検索結果で、**[仮想ネットワーク]** を選択します。

10. **[仮想ネットワーク]** で **myVNet** を選択します。

11. **myVNET** の **設定** で、 **[サブネット]** を選択します。

12. **+ ゲートウェイ サブネット** を選択します。

13. **[Add subnet (サブネットの追加)]** で、 **[Subnet address range (サブネット アドレスの範囲)]** を **/24** から **/27** に変更します。

14. **[保存]** を選択します。

### <a name="create-vpn-gateway"></a>VPN ゲートウェイを作成する

2. ポータルの上部にある検索ボックスに、「**仮想ネットワーク ゲートウェイ**」と入力します。

3. 検索結果で、**仮想ネットワーク ゲートウェイ** を選択します。

4. **[+ 作成]** を選択します。

5. **[Create virtual network gateway (仮想ネットワーク ゲートウェイの追加)]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** |   |
    | サブスクリプション | サブスクリプションを選択します。 |
    | **インスタンスの詳細** |   |
    | 名前 | 「**myVPNGateway**」と入力します。 |
    | リージョン | **[米国西部 2]** を選択します。 |
    | ゲートウェイの種類 | 既定値の **VPN** のままにします。 |
    | VPN の種類 | 既定値の **ルートベース** のままにします。 |
    | SKU | **VpnGw1AZ** を選択します。 |
    | 仮想ネットワーク | **[myVNet]** を選択します。 |
    | Subnet | エントリでは、先ほど作成した **GatewaySubnet** が自動選択されます |
    | **パブリック IP アドレス** |   |
    | パブリック IP アドレス | **[既存のものを使用]** を選択します。 |
    | パブリック IP アドレスの選択 | **myStandardPublicIP** または実際のパブリック IP アドレスを選択します |
    | アクティブ/アクティブ モードの有効化 | 既定値の **[無効]** のままにします。 |
    | BGP の構成 | 既定値の **[無効]** のままにします。 |

6. **[確認と作成]** タブを選択するか、青い **[確認と作成]** ボタンを選択します。

7. **［作成］** を選択します

> [!NOTE]
> これは、VPN Gateway の簡単なデプロイです。 詳細な構成と設定については、[チュートリアル: Azure portal を使用した VPN ゲートウェイの作成と管理](../../vpn-gateway/tutorial-create-gateway-portal.md)を参照してください。
>
> VPN Gateway の詳細については、[VPN Gateway とは](../../vpn-gateway/vpn-gateway-about-vpngateways.md)を参照してください。

## <a name="change-or-remove-public-ip-address"></a>パブリック IP アドレスを変更または削除する

VPN ゲートウェイでは、作成後のパブリック IP アドレスの変更はサポートされていません。

## <a name="caveats"></a>注意事項

* 現時点では、パブリック IPv6 アドレスは VPN ゲートウェイでサポートされていません。
 
## <a name="next-steps"></a>次のステップ

このアーティクルでは、VPN Gateway を作成し、既存のパブリック IP を使用する方法について説明しました。 

- Azure のパブリック IP アドレスの詳細については、[パブリック IP アドレス](./public-ip-addresses.md)を参照してください。
- VPN Gateway の詳細は、[VPN Gateway について](../../vpn-gateway/vpn-gateway-about-vpngateways.md)を参照してください。