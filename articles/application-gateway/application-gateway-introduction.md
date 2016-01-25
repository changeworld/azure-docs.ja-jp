<properties
   pageTitle="Application Gateway の概要 | Microsoft Azure"
   description="このページでは、ゲートウェイのサイズ、HTTP 負荷分散、Cookie ベースのセッション アフィニティ、SSL オフロードなど、レイヤー 7 の負荷分散を提供する Application Gateway サービスの概要について説明します。"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/09/2015"
   ms.author="joaoma"/>

# Application Gateway とは


Microsoft Azure Application Gateway は、レイヤー 7 の負荷分散に基づく、Azure で管理される HTTP 負荷分散ソリューションを提供します。

アプリケーションの負荷分散では、IT 管理者や開発者は、HTTP に基づくネットワーク トラフィックのルーティング ルールを作成することができます。この Application Gateway サービスは可用性が高く、従量課金制で使用できます。SLA と価格については、「[サービス レベル アグリーメント](http://azure.microsoft.com/support/legal/sla/)」および「[Application Gateway の価格](https://azure.microsoft.com/pricing/details/application-gateway/)」をご覧ください。

現在、Application Gateway は次のレイヤー 7 アプリケーションの配信をサポートします。

- HTTP の負荷分散
- Cookie ベースのセッション アフィニティ
- Secure Sockets Layer (SSL) オフロード

![Application Gateway](./media/application-gateway-introduction/appgateway1.png)

HTTP レイヤー 7 の負荷分散は、次の場合に役立ちます。

- 同じバックエンド VM に到達するために同じユーザー/クライアントのセッションからの要求を必要とするアプリケーション。この例としては、ショッピング カート アプリや Web メール サーバーなどが挙げられます。
- Web サーバーのファームを SSL 終了オーバーヘッドから解放する必要があるアプリケーション。
- 実行時間の長い同じ TCP 接続で複数の HTTP 要求を異なるバックエンド サーバーにルーティング/負荷分散する必要があるアプリケーション (コンテンツ配信ネットワークなど)。


## ゲートウェイのサイズとインスタンス

現在、Application Gateway は Small、Medium、Large の 3 つのサイズで提供されています。Small サイズのインスタンスは、開発用およびシナリオのテスト用です。

サブスクリプションごとに最大 50 個のアプリケーション ゲートウェイを作成でき、アプリケーション ゲートウェイごとに最大 10 個のインスタンスを使用できます。Azure で管理されるサービスとしての Application Gateway の負荷分散により、Azure のソフトウェア ロード バランサーの背後でレイヤー 7 ロード バランサーをプロビジョニングできます。

次の表では、Application Gateway インスタンスごとにパフォーマンス スループットの平均値を示します。


| バックエンド ページの応答 | Small | 中 | Large|
|---|---|---|---|
| 6K | 7\.5 Mbps | 13 Mbps | 50 Mbps |
|100K | 35 Mbps | 100 Mbps| 200 Mbps |


>[AZURE.NOTE]これはアプリケーション ゲートウェイ スループットのおおよそのガイダンスです。実際のスループットは、平均ページ サイズ、バックエンド インスタンスの場所、ページの処理時間など、さまざまな環境の違いによって異なります。

## 正常性の監視


Azure Application Gateway は、バックエンド インスタンスの正常性を自動的に監視します。詳細については、「[Application Gateway health monitoring overview](application-gateway-probe-overview.md)」をご覧ください。

## 構成と管理

REST API や PowerShell コマンドレットを使用して、アプリケーション ゲートウェイを作成および管理できます。



## 次のステップ

アプリケーション ゲートウェイを作成します。「[アプリケーション ゲートウェイの作成](application-gateway-create-gateway.md)」をご覧ください。

SSL オフロードを構成します。「[SSL オフロード用の Application Gateway を構成する](application-gateway-ssl.md)」をご覧ください。

<!---HONumber=AcomDC_0114_2016-->