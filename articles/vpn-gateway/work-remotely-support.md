---
title: P2S を使用したリモート作業:Azure VPN Gateway
description: このページでは、COVID-19 の世界的流行により、Azure Bastion を利用してリモートで作業できるようにする方法について説明します。
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: alzam
ms.openlocfilehash: b751817467bd263e8b7c64ccc95ec82ef1579836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337118"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Azure VPN Gateway ポイント対サイトを使用したリモート作業

>[!NOTE]
>この記事では、Azure VPN Gateway、Azure、Microsoft ネットワーク、および Azure パートナー エコシステムを利用してリモートで作業し、COVID-19 の危機によって直面しているネットワークの問題を軽減する方法について説明します。
>

この記事では、COVID-19 の流行中に組織がユーザー向けにリモート アクセスを設定したり、容量を追加して既存のソリューションを補完したりするために使用できるオプションについて説明します。

Azure ポイント対サイトのソリューションはクラウド ベースであり、自宅で作業したいというユーザーの高まる要望に応えるために迅速にプロビジョニングできます。 簡単にスケールアップでき、増やした容量が不要になったら簡単かつ迅速に無効にできます。

## <a name="about-point-to-site-vpn"></a>ポイント対サイト VPN について

ポイント対サイト (P2S) VPN ゲートウェイ接続では、個々のクライアント コンピューターから仮想ネットワークへの、セキュリティで保護された接続を作成することができます。 P2S 接続は、クライアント コンピューターから接続を開始することによって確立されます。 このソリューションは、在宅勤務者が自宅や会議室など、遠隔地から Azure VNet またはオンプレミスのデータ センターに接続する場合に便利です。 この記事では、さまざまなシナリオに基づいてユーザーがリモートで作業できるようにする方法について説明します。

次の表は、クライアント オペレーティング システムと、それらに使用できる認証オプションを示しています。 既に使用中のクライアント OS に基づいて認証方法を選択すると便利です。 たとえば、接続する必要があるクライアント オペレーティング システムが混在している場合は、証明書ベースの認証を使用する OpenVPN を選択します。 また、ポイント対サイト VPN は、ルート ベースの VPN ゲートウェイでのみサポートされていることに注意してください。

![ポイント対サイト](./media/working-remotely-support/ostable.png "OS")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a>シナリオ 1 - ユーザーが Azure のリソースにのみアクセスする必要がある

このシナリオでは、リモート ユーザーは、Azure 内のリソースにのみアクセスする必要があります。

![ポイント対サイト](./media/working-remotely-support/scenario1.png "シナリオ 1")

ユーザーが Azure リソースに安全に接続できるようにするために必要な手順の概要は、次のとおりです。

1. Virtual Network ゲートウェイを作成します (存在しない場合)
2. ゲートウェイでポイント対サイト VPN を構成します
    3. [証明書の認証の場合は、このリンクに従います](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw)。
    2.  [OpenVPN の場合は、このリンクに従います](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn)。
    3.  [Azure AD 認証の場合は、このリンクに従います](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-tenant)。
    4.  [ポイント対サイト接続のトラブルシューティングの場合は、このリンクに従います](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems)。
3. VPN クライアント構成をダウンロードして配布します
4. (証明書の認証を選択した場合) 証明書をクライアントに配布します
5. Azure VPN に接続します

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a>シナリオ 2 - ユーザーが Azure またはオンプレミス (あるいは両方) のリソースにアクセスする必要がある

このシナリオでは、リモート ユーザーは、Azure とオンプレミスのデータ センター内のリソースにアクセスする必要があります。

![ポイント対サイト](./media/working-remotely-support/scenario2.png "シナリオ 2")

ユーザーが Azure リソースに安全に接続できるようにするために必要な手順は、大まかに次のとおりです。

1. Virtual Network ゲートウェイを作成します (存在しない場合)
2. ゲートウェイでポイント対サイト VPN を構成します (上記のシナリオ 1 を参照)
3. BGP が有効になっている Azure Virtual Network ゲートウェイでサイト間トンネルを構成します
4. Azure Virtual Network ゲートウェイに接続するようにオンプレミスのデバイスを構成します
5. Azure portal からポイント対サイト プロファイルをダウンロードし、クライアントに配布します

[サイト間 VPN トンネルを設定する方法を参照するには、このリンクに従います](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>ネイティブ Azure 証明書認証に関する FAQ

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>RADIUS 認証に関する FAQ

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>次の手順

* [P2S 接続の構成 - Azure AD 認証](openvpn-azure-ad-tenant.md)

* [P2S 接続の構成 - RADIUS 認証](point-to-site-how-to-radius-ps.md)

* [P2S 接続の構成 - Azure ネイティブ証明書認証](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" は OpenVPN Inc. の商標です。**