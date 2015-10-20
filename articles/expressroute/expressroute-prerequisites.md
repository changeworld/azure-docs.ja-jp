<properties
   pageTitle="ExpressRoute 導入の前提条件 | Microsoft Azure"
   description="このページでは、Azure ExpressRoute 回線を注文する前に満たす必要がある要件を一覧します。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/21/2015"
   ms.author="cherylmc"/>


# ExpressRoute の前提条件   

ExpressRoute を使用して Microsoft クラウド サービスに接続する場合は、以下のセクションにある前提条件を満たしていることを確認する必要があります。

## アカウントの要件

- 有効でアクティブな Microsoft Azure アカウント。これは ExpressRoute 回線のセットアップに必要です。ExpressRoute 回線は Azure サブスクリプション内のリソースです。Azure サブスクリプションは、Office 365 や CRM Online など、非 Azure Microsoft クラウド サービスに接続が制限される場合でも、要件となります。
- アクティブな Office 365 サブスクリプション (Office 365 サービスを利用する場合)。詳細については、この記事の「[Office 365 固有の要件](#office-365-specific-requirements)」セクションを参照してください。

## 接続プロバイダーの関係

- サポート対象一覧に記載されている接続プロバイダーとの関係。この接続プロバイダーによる接続を円滑にする必要があります。接続プロバイダーとの間にビジネス関係が存在する必要があります。その接続プロバイダーにより与えられるサービスが ExpressRoute に対応していることを確認する必要があります。
- サポート対象一覧にない接続プロバイダーを使用する場合でも、Exchange を利用して Microsoft クラウド サービスへの接続を作成できます。
	- その接続プロバイダーがサポート対象一覧の Exchange の場所に存在するかどうかを確認します。
	- 選択した Exchange の場所までネットワークを拡張するように接続プロバイダーに依頼します。
	- 接続プロバイダーとしてその Exchange で ExpressRoute 回線を要求します。

## ネットワークと接続プロバイダーの間の物理接続

接続モデルの詳細については、接続モデルのセクションを参照してください。お客様はオンプレミスのインフラストラクチャがその説明にあるモデルの 1 つを介してサービス プロバイダー インフラストラクチャに物理的に接続されていることを確認する必要があります。

## 接続の冗長性要件

お客様のインフラストラクチャとサービス プロバイダー インフラストラクチャの間に物理的接続の冗長性要件はありません。Microsoft はレイヤー 3 の冗長性を必要とします。Microsoft は、各ピアリングを有効にするために、サービス プロバイダーを介して、Microsoft のエッジとお客様のネットワークの間に冗長性ルーティングを構築することを必要とします。ルーティング セッションが冗長的に構成されていない場合、サービス可用性 SLA は無効になります。

## IP アドレスとルーティングに関する考慮事項

お客様/接続プロバイダーは、Microsoft エッジ インフラストラクチャと冗長 BGM セッションを構築しなければなりません。IP VPN 経由の接続を選択するお客様は、通常、ルーティング構成の管理を接続プロバイダーに任せます。Exchange と併置される、あるいはポイントツーポイントのイーサネット プロバイダー経由で Microsoft に接続するお客様は、可用性 SLA 要件を満たすために、ピアリングごとに冗長 BGP セッションを構成する必要があります。接続プロバイダーはこれを付加価値サービスとして提供することがあります。上限に関する詳細については、「[ExpressRoute 回線とルーティング ドメイン](expressroute-circuit-peerings.md)」という記事のルーティング ドメイン表を参照してください。

## セキュリティとファイアウォール

セキュリティとファイアウォールについては、このドキュメントの「[Microsoft Cloud Services とネットワーク セキュリティ](../best-practices-network-security.md)」を参照してください。

## Azure パブリックと Microsoft ピアリングの NAT 構成

要件と構成に関する詳細については、「[ExpressRoute NAT の要件](expressroute-nat.md)」を参照してください。NAT の構成と管理をあなたの代わりに行うかどうかについて接続プロバイダーにご確認ください。一般的に、レイヤー 3 接続プロバイダーはあなたの代わりに NAT を管理します。

## Office 365 固有の要件

Office 365 の要件の詳細については、次のリソースを確認します。

- [Office 365 のネットワーク計画とパフォーマンスのチューニング](http://aka.ms/tune)
- [Office 365 ネットワーク トラフィック管理](https://msft.spoppe.com/teams/cpub/teams/IW_Admin/modsquad/_layouts/15/WopiFrame.aspx?sourcedoc=%7b23f09224-0668-4476-8627-aaff30931439%7d&action=edit&source=https%3A%2F%2Fmsft%2Espoppe%2Ecom%2Fteams%2Fcpub%2Fteams%2FIW%5FAdmin%2Fmodsquad%2FSitePages%2FHome%2Easpx)
- QoS の要件と構成については、[ExpressRoute サービス品質 (QoS) 要求](expressroute-qos.md)に関する記事を参照してください。あなたの VPN にサービスのクラスを複数提供するかどうかを接続プロバイダーにお問い合わせください。 

## 次のステップ

- ExpressRoute の詳細については、「[ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
- サービス プロバイダーを検索します。「[ExpressRoute パートナーとピアリングの場所](expressroute-locations.md)」を参照してください。
- [ルーティング](expressroute-routing.md)、[NAT](expressroute-nat.md)、[QoS](expressroute-qos.md) の要件を参照してください。
- ExpressRoute 接続を構成します。
	- [ExpressRoute 回線の作成](expressroute-howto-circuit-classic.md)
	- [ルーティングの構成](expressroute-howto-routing-classic.md)
	- [ExpressRoute 回線への VNet のリンク](expressroute-howto-linkvnet-classic.md)

<!---HONumber=Oct15_HO3-->