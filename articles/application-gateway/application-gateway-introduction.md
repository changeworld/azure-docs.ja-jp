<properties 
   pageTitle="Application Gateway の概要 | Microsoft Azure"
   description="このページは、ゲートウェイのサイズ、HTTP の負荷分散、cookie ベースのセッション アフィニティ、SSL オフロードを含む、Application Gateway サービスのレイヤー 7 負荷分散の概要について説明します。"
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


Microsoft Azure Application Gateway は、レイヤー 7 の負荷分散に基づく、Azure で管理される HTTP の負荷分散ソリューションを提供します。

アプリケーションの負荷分散では、IT 管理者や開発者は、HTTP に基づくネットワーク トラフィックのルーティング ルールを作成することができます。この Application Gateway サービスは可用性が高く、従量課金制で使用できます。価格と SLA については、[SLA](http://azure.microsoft.com/support/legal/sla/) と[価格](https://azure.microsoft.com/pricing/details/application-gateway/)のページを参照してください。

Application Gateway は現在、次のレイヤー 7 アプリケーションの配信をサポートします。

- HTTP の負荷分散
- cookie ベースのセッション アフィニティ
- SSL オフロード

![Application Gateway](./media/application-gateway-introduction/appgateway1.png)

HTTP レイヤー 7 の負荷分散は、次の場合に役立ちます。

- 同じバックエンド VM に到達するために同じユーザー/クライアントのセッションからの要求を必要とするアプリケーション。この例としては、ショッピング カート アプリや Web メール サーバーなどが挙げられます。
- Web サーバーのファームを SSL 終了オーバーヘッドから解放する必要があるアプリケーション。
- 実行時間の長い同じ TCP 接続で複数の HTTP 要求を異なるバックエンド サーバーにルーティング/負荷分散する必要があるアプリケーション (CDN など)。


## ゲートウェイのサイズとインスタンス

Application Gateway は現在、Small、Medium、および Large の 3 つのサイズで提供されています。Small サイズのインスタンスは、開発用およびシナリオのテスト用です。

サブスクリプションごとに最大 50 個のアプリケーション ゲートウェイを作成できるほか、各アプリケーション ゲートウェイには最大 10 個のインスタンスが存在します。Azure で管理されるサービスとしての Application Gateway の負荷分散により、Azure のソフトウェア ロード バランサーの背後でレイヤー 7 ロード バランサーをプロビジョニングできます。

次の表では、Application Gateway インスタンスごとにパフォーマンス スループットの平均値を示します。


| バック エンド ページの応答 | Small | 中 | Large|
|---|---|---|---|
| 6K | 7\.5 mbps | 13 mbps | 50 mbps |
|100k | 35 mbps | 100 mbps| 200 mbps |


>[AZURE.NOTE]これはアプリケーション ゲートウェイ スループットのおおよそのガイダンスです。実際のスループットは、平均ページ サイズ、バックエンド インスタンスの場所、ページの処理時間など、さまざまな環境の違いによって異なります。

## 正常性の監視
 

Azure Application Gateway はバックエンド インスタンスの状態を自動で監視します。詳細については、[プローブと Application Gateway による正常性の監視](application-gateway-probe-overview.md)に関するページを参照してください。

## 構成と管理

REST API や PowerShell コマンドレットを使用して、アプリケーション ゲートウェイを作成および管理できます。



## 次のステップ

Application Gateway を作成します。「[アプリケーション ゲートウェイの作成](application-gateway-create-gateway.md)」を参照してください。

SSL オフロードを構成します。「[SSL オフロードのアプリケーション ゲートウェイの構成](application-gateway-ssl.md)」を参照してください。

<!---HONumber=AcomDC_0107_2016-->