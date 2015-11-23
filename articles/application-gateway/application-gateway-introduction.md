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

## HTTP レイヤー 7 の負荷分散

Azure では、トランスポート レベル (TCP/UDP) で機能する Azure のロード バランサーを介してレイヤー 4 の負荷分散を提供し、すべての受信ネットワーク トラフィックを Application Gateway サービスに負荷分散させます。Application Gateway は、HTTP トラフィックにルーティング ルールを適用して レベル 7 (HTTP) の負荷分散を提供します。アプリケーション ゲートウェイを作成すると、エンドポイント (VIP) が関連付けられ、受信ネットワーク トラフィック用のパブリック IP として使用されます。

Application Gateway は、仮想マシン、クラウド サービス、Web アプリ、外部 IP アドレスのいずれであるかに関わらず、その構成に基づいて HTTP トラフィックをルーティングします。

次の図に、Application Gateway のトラフィックの流れを示します。

 
![Application Gateway2](./media/application-gateway-introduction/appgateway2.png)

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
|100k | 35 mbps | 100mbps| 200 mbps |


>[AZURE.NOTE]これはアプリケーション ゲートウェイ スループットのおおよそのガイダンスです。実際のスループットは、平均ページ サイズ、バックエンド インスタンスの場所、ページの処理時間など、さまざまな環境の違いによって異なります。

## 正常性の監視
 

Azure Application Gateway はバックエンド インスタンスの状態を 30 秒おきに監視します。構成の *BackendHttpSettings* 要素で構成されているポートで各インスタンスに HTTP 正常性プローブ要求を送信します。正常性プローブは、200 ～ 399 の範囲の応答状態コードで正常な HTTP 応答を予測します。

正常な HTTP 応答が届くと、バックエンド サーバーには「正常」の印が付けられ、引き続き Azure Application Gateway からトラフィックを受信します。プローブが失敗した場合、バックエンド インスタンスは正常プールから削除され、このサーバーへのトラフィックの送信は停止します。正常性プローブは引き続き 30 秒おきに失敗したバックエンド インスタンスに送信され、現在の正常性を確認します。バックエンド インスタンスが正常性プローブに正常に応答すると、「正常」としてバック エンド プールに戻され、この インスタンスへのトラフィックの送信が再開されます。

## 構成と管理

REST API や PowerShell コマンドレットを使用して、Application Gateway を作成および管理できます。



## 次のステップ

Application Gateway を作成します。「[アプリケーション ゲートウェイの作成](application-gateway-create-gateway.md)」を参照してください。

SSL オフロードを構成します。「[SSL オフロードのアプリケーション ゲートウェイの構成](application-gateway-ssl.md)」を参照してください。

<!---HONumber=Nov15_HO3-->