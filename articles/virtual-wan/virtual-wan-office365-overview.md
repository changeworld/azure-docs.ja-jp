---
title: Azure Virtual WAN の Office 365 コントロール プレーン
description: Virtual WAN の Office 365 コントロール プレーンについて説明します。
author: cherylmc
ms.service: virtual-wan
services: virtual-wan
ms.topic: article
ms.date: 9/24/2018
ms.author: cherylmc
ms.openlocfilehash: 815b91295540e93f8f0ffbc002fcf02ce1c13365
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992679"
---
# <a name="office-365-control-plane-in-virtual-wan"></a>Virtual WAN の Office 365 コントロール プレーン

一部の SDWAN デバイスを使用する Virtual WAN のお客様は、Azure portal で信頼済みトラフィックの O365 インターネット ブレークアウト ポリシーを構成できます。 これにより、次のことを実現できます。
- O365 トラフィックは、最適なユーザー エクスペリエンスを提供する、ユーザーの近くの Microsoft ネットワークに入ることができます。
- トラフィックのバックホーリングやヘアピニングを回避できるので、WAN コストが削減されます。
- O365 の接続の原則を満たすことができます。

## <a name="faqs"></a>FAQ
### <a name="what-is-the-customer-benefit"></a>顧客にとってのメリットは何ですか?
Virtual WAN のこの機能を使用すると、ダイレクト インターネット ブレークアウトのために、信頼する Office 365 トラフィック カテゴリを指定できるようになります。 この信頼済み O365 トラフィックはプロキシをバイパスし、ユーザーの場所から最寄りの Microsoft POP に直接ルーティングされます。 これにより、トラフィックのバックホーリングやヘアピニングが回避されるので、最適なユーザー エクスペリエンスが提供され、WAN コストも削減されます。 

### <a name="what-are-the-office-365-traffic-categories"></a>Office 365 トラフィック カテゴリを教えてください。
Office 365 エンドポイントは、ネットワーク アドレスとサブネットを表します。 エンドポイントは、URL、IP アドレス、または IP 範囲になります。 URL には、*account.office.net* のような FQDN、または **.office365.com* のようなワイルドカード URL を使用できます。 エンドポイントは、重要度に基づいて、**最適化**、**許可**、**既定**の 3 つのカテゴリに分けられます。 エンドポイントのカテゴリの詳細については、[こちら](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles#BKMK_Categories)をご覧ください。

### <a name="which-office-365-traffic-category-is-recommended-by-microsoft-for-direct-internet-breakout"></a>ダイレクト インターネット ブレークアウトに推奨される Office 365 トラフィック カテゴリはどれですか?
**最適化**カテゴリは最も重要なネットワーク エンドポイントであり、SSL 中断/検査デバイスや他のネットワーク セキュリティ デバイスをバイパスする必要があります。 ユーザーの近くに直接インターネット エグレスが必要です。 これらのエンドポイントは、ネットワークのパフォーマンス、待ち時間、可用性の影響を最も受けやすい Office 365 シナリオを表します。 このカテゴリには、重要な URL の小規模なセット (最大約 10 個) と、Office 365 コア ワークロード (Exchange Online、SharePoint Online、Skype for Business Online、Microsoft Teams など) 専用の IP サブネットの定義済みセットが含まれます。 

**許可**カテゴリも、直接インターネット エグレスに推奨されます。 ただし、"許可" ネットワーク トラフィックは、多少のネットワーク待ち時間を許容できます。 "最適化" カテゴリと "許可" カテゴリのエンドポイントは、すべて Microsoft データセンターでホストされ、Office 365 の一部として管理されます。 "既定" カテゴリは、既定のインターネット エグレスの場所に送信されます。直接インターネット エグレスや、SSL 中断/検査デバイスのバイパスは不要です。

### <a name="how-do-i-set-my-o365-policies-via-virtual-wan"></a>Virtual WAN を使用して O365 ポリシーを設定するにはどうすればよいですか?
ポリシーは、**[Virtual WAN]** -> **[設定]** -> **[構成]** タブで有効にすることができます。ここで、ダイレクト インターネット ブレークアウトのための O365 トラフィックの優先カテゴリを指定できます。

![Virtual WAN で Office 365 コントロール プレーンを構成する](media/virtual-wan-office365-overview/configure-office365-control-plane.png)

### <a name="how-does-this-work"></a>しくみ

1.  O365 トラフィックは、最適なエクスペリエンスを提供する、ユーザーの近くの Microsoft ネットワークに入ります。
2.  SDWAN によってルート ポリシーが使用されます。 その後、信頼済みカテゴリのためにセキュリティ プロキシをバイパスし、これらのカテゴリに対してローカル ダイレクト ブレークアウトが実行されます。
3.  バックホーリングとトラフィックのヘアピニングが回避され、WAN コストが削減されます。

### <a name="which-partner-devices-support-this-via-virtual-wan"></a>Virtual WAN を使用してこれをサポートしているのは、どのパートナー デバイスですか?
現時点では、Citrix が Virtual WAN を使用してこれらのポリシーをサポートしています。

### <a name="what-happens-to-the-remaining-categories-of-untrusted-o365-traffic"></a>残りのカテゴリの (信頼されていない) O365 トラフィックはどうなりますか?
残りの O365 トラフィックは、お客様の既定のインターネット トラフィック パスに従います。

### <a name="what-if-i-have-already-specified-my-o365-policies-via-my-sdwan-provider"></a>SDWAN プロバイダーを使用して O365 ポリシーを既に指定している場合はどうなりますか?
SDWAN UX と Azure Virtual WAN の両方を使用してポリシーを指定した場合、Virtual WAN で設定されたポリシーが優先されます。

## <a name="next-steps"></a>次の手順
- [Virtual WAN](virtual-wan-about.md) の詳細を確認します。