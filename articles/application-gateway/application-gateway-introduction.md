<properties
   pageTitle="Application Gateway の概要 | Microsoft Azure"
   description="このページでは、ゲートウェイのサイズ、HTTP 負荷分散、Cookie ベースのセッション アフィニティ、SSL オフロードなど、レイヤー 7 の負荷分散を提供する Application Gateway サービスの概要について説明します。"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="gwallace"/>

# Application Gateway の概要

Microsoft Azure Application Gateway は、レイヤー 7 の負荷分散に基づく、Azure で管理される HTTP 負荷分散ソリューションを提供します。

アプリケーションの負荷分散では、IT 管理者や開発者は、HTTP に基づくネットワーク トラフィックのルーティング ルールを作成することができます。この Application Gateway サービスは可用性が高く、従量課金制で使用できます。SLA と価格については、「[サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」および「[Application Gateway の価格](https://azure.microsoft.com/pricing/details/application-gateway/)」をご覧ください。

現在、Application Gateway は次のレイヤー 7 アプリケーションの配信をサポートします。

- HTTP の負荷分散
- Cookie ベースのセッション アフィニティ
- [Secure Sockets Layer (SSL) オフロード](application-gateway-ssl-arm.md)
- [URL ベースのコンテンツ ルーティング](application-gateway-url-route-overview.md)
- [複数サイトのルーティング](application-gateway-multi-site-overview.md)

## HTTP レイヤー 7 の負荷分散

Azure では、トランスポート レベル (TCP/UDP) で機能する Azure Load Balancer を介してレイヤー 4 の負荷分散を提供し、すべての受信ネットワーク トラフィックを Application Gateway サービスに負荷分散させます。Application Gateway は、HTTP トラフィックにルーティング ルールを適用して、レイヤー 7 (HTTP) の負荷分散を実現します。アプリケーション ゲートウェイを作成すると、エンドポイント (VIP) が関連付けられ、イングレス ネットワーク トラフィック用のパブリック IP として使用されます。

Application Gateway は、仮想マシン、クラウド サービス、Web アプリ、外部 IP アドレスのいずれであるかにかかわらず、その構成に基づいて HTTP トラフィックをルーティングします。

HTTP レイヤー 7 の負荷分散は、次の場合に役立ちます。

- 同じユーザー/クライアントのセッションからの要求が同じバックエンド仮想マシンに到達する必要があるアプリケーション。これらのアプリケーションの例としては、ショッピング カート アプリや Web メール サーバーなどが挙げられます。
- Web サーバーのファームを SSL ターミネーションのオーバーヘッドから解放する必要があるアプリケーション。
- 実行時間の長い同じ TCP 接続で複数の HTTP 要求を異なるバックエンド サーバーにルーティング/負荷分散する必要があるアプリケーション (コンテンツ配信ネットワークなど)。

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]


## ゲートウェイのサイズとインスタンス

現在、Application Gateway は Small、Medium、Large の 3 つのサイズで提供されています。Small サイズのインスタンスは、開発用およびシナリオのテスト用です。

サブスクリプションごとに最大 50 個のアプリケーション ゲートウェイを作成でき、アプリケーション ゲートウェイごとに最大 10 個のインスタンスを使用できます。Azure で管理されるサービスとしての Application Gateway の負荷分散により、Azure のソフトウェア ロード バランサーの背後でレイヤー 7 ロード バランサーをプロビジョニングできます。

次の表では、アプリケーション ゲートウェイ インスタンスごとにパフォーマンス スループットの平均値を示します。


| バックエンド ページの応答 | Small | 中 | Large|
|---|---|---|---|
| 6 K | 7\.5 Mbps | 13 Mbps | 50 Mbps |
|100 K | 35 Mbps | 100 Mbps| 200 Mbps |


>[AZURE.NOTE] これらの値は、アプリケーション ゲートウェイ スループットのおおよその値です。実際のスループットは、平均ページ サイズ、バックエンド インスタンスの場所、ページの処理時間など、さまざまな環境の違いによって異なります。


## 正常性の監視

Azure Application Gateway は、バックエンド インスタンスの正常性を自動的に監視します。詳細については、「[Application Gateway による正常性監視の概要](application-gateway-probe-overview.md)」を参照してください。





## 構成と管理

REST API や PowerShell コマンドレット、[Azure Portal](https://portal.azure.com/) を使用して、アプリケーション ゲートウェイを作成および管理できます。


## 次のステップ

アプリケーション ゲートウェイについて学習すると、[アプリケーション ゲートウェイを作成](application-gateway-create-gateway-portal.md)することも、[アプリケーション ゲートウェイ SSL オフロードを作成](application-gateway-ssl-arm.md)して HTTPS 接続を負荷分散することもできるようになります。

URL ベースのコンテンツ ルーティングを使用してアプリケーション ゲートウェイを作成する方法については、「[URL ベースのルーティングを使用して Application Gateway を作成する](application-gateway-create-url-route-arm-ps.md)」を参照してください。

<!---HONumber=AcomDC_0831_2016-->