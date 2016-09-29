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
   ms.date="09/06/2016"
   ms.author="gwallace"/>

# Application Gateway の概要

Microsoft Azure Application Gateway は、レイヤー 7 の負荷分散に基づく、Azure で管理される HTTP 負荷分散サービスを提供します。単純化して言えば、Application Gateway は、トラフィックを受け取り、定義された規則に基づいてそのトラフィックを適切なバックエンド インスタンスに転送します。

アプリケーションの負荷分散では、IT 管理者や開発者は、HTTP プロトコルに基づくネットワーク トラフィックのルーティング規則を作成することができます。この Application Gateway サービスは可用性が高く、従量課金制で使用できます。SLA と価格については、「[サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」および「[Application Gateway の価格](https://azure.microsoft.com/pricing/details/application-gateway/)」をご覧ください。

Application Gateway は現在、次の機能でレイヤー 7 アプリケーションの配信をサポートします。

- **HTTP の負荷分散** - Application Gateway の主な機能は、負荷分散を提供することです。負荷分散は、レイヤー 7 で実行され、HTTP トラフィックのみに使用されます。
- **Cookie ベースのセッション アフィニティ** - この機能は、ユーザー セッションを同じバックエンドで保持したい場合に便利です。Cookie を使用すると、Application Gateway は、ユーザー セッションの後続のトラフィックを、処理のために同じバックエンドに送ることができます。この機能は、ユーザー セッションのために何らかの情報をバックエンド サーバー上でローカルに保存する場合に重要です。
- **[Secure Sockets Layer (SSL) オフロード](application-gateway-ssl-arm.md)** - この機能は、コストのかかる HTTPS トラフィックの暗号化解除タスクを Web サーバーから切り離します。SSL 接続を Application Gateway で終了し、要求を暗号化せずにサーバーに転送することにより、Web サーバーが暗号化解除を行う負荷から解放されます。応答は、Application Gateway によって再び暗号化された後、クライアントに送信されます。この機能は、バックエンドが Azure の Application Gateway と同じセキュリティで保護された仮想ネットワーク内に配置されている場合に有用です。
- **[URL ベースのコンテンツ ルーティング](application-gateway-url-route-overview.md)** - この機能は、トラフィックごとに異なるバックエンド サーバーを使用する機能を提供します。Web サーバー上のフォルダーまたは CDN に対するトラフィックを異なるバックエンドにルーティングして、特定のコンテンツを提供しないバックエンドに対する不要な負荷を減らすことができます。
- **[マルチサイト ルーティング](application-gateway-multi-site-overview.md)** - Application Gateway では、単一のアプリケーション ゲートウェイに最大 20 個の Web サイトを統合できます。
- **[WebSocket のサポート](application-gateway-websocket.md)** - Application Gateway のもう 1 つの優れた機能として、WebSocket がネイティブでサポートされます。


## HTTP レイヤー 7 の負荷分散

Application Gateway は、HTTP トラフィックにルーティング ルールを適用して、レイヤー 7 (HTTP) の負荷分散を実現します。アプリケーション ゲートウェイを作成すると、エンドポイント (VIP) が関連付けられ、イングレス ネットワーク トラフィック用のパブリック IP として使用されます。Azure では、トランスポート レベル (TCP/UDP) で機能する Azure Load Balancer を介してレイヤー 4 の負荷分散を提供し、すべての受信ネットワーク トラフィックを Application Gateway サービスに負荷分散させます。Application Gateway は、仮想マシン、クラウド サービス、Web アプリ、外部 IP アドレスのいずれであるかにかかわらず、その構成に基づいて HTTP トラフィックをルーティングします。

HTTP レイヤー 7 の負荷分散は、次の場合に役立ちます。

- 同じユーザー/クライアントのセッションからの要求が同じバックエンド仮想マシンに到達する必要があるアプリケーション。これらのアプリケーションの例としては、ショッピング カート アプリや Web メール サーバーなどが挙げられます。
- Web サーバーのファームを SSL ターミネーションのオーバーヘッドから解放する必要があるアプリケーション。
- 実行時間の長い同じ TCP 接続で複数の HTTP 要求を異なるバックエンド サーバーにルーティング/負荷分散する必要があるアプリケーション (コンテンツ配信ネットワークなど)。
- WebSocket トラフィックをサポートする一方で、監視用の http エンドポイントがあるアプリケーション。

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

Azure Application Gateway は、基本またはカスタムの正常性プローブを使用してバックエンド インスタンスの正常性を自動的に監視します。詳細については、「[Application Gateway による正常性監視の概要](application-gateway-probe-overview.md)」を参照してください。

## 構成と管理

アプリケーション ゲートウェイは、そのエンドポイントに対して、パブリック IP、プライベート IP、またはその両方を持つことができます。アプリケーション ゲートウェイは、独自のサブネットの仮想ネットワーク内に構成されます。アプリケーション ゲートウェイ用に作成または使用されるサブネットには、他の種類のリソースを含めることはできません。サブネットに含めることができるリソースは、他のアプリケーション ゲートウェイのみです。

REST API、PowerShell コマンドレット、Azure CLI、または [Azure Portal](https://portal.azure.com/) を使用して、アプリケーション ゲートウェイを作成および管理できます。

## 次のステップ

アプリケーション ゲートウェイについて学習すると、[アプリケーション ゲートウェイを作成](application-gateway-create-gateway-portal.md)することも、[アプリケーション ゲートウェイ SSL オフロードを作成](application-gateway-ssl-arm.md)して HTTPS 接続を負荷分散することもできるようになります。

URL ベースのコンテンツ ルーティングを使用してアプリケーション ゲートウェイを作成する方法については、「[URL ベースのルーティングを使用して Application Gateway を作成する](application-gateway-create-url-route-arm-ps.md)」を参照してください。

<!---HONumber=AcomDC_0914_2016-->