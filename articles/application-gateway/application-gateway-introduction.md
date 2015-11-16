<properties 
   pageTitle="Application Gateway の概要 | Microsoft Azure"
   description="このページは、ゲートウェイのサイズ、HTTP の負荷分散、cookie ベースのセッション アフィニティ、SSL オフロードを含む、Application Gateway サービスのレイヤー 7 負荷分散の概要について説明します。"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/03/2015"
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

サブスクリプションごとに最大 10 個のアプリケーション ゲートウェイを作成できるほか、各アプリケーション ゲートウェイには最大 10 個のインスタンスが存在します。Azure で管理されるサービスとしての Application Gateway の負荷分散により、Azure のソフトウェア ロード バランサーの背後でレイヤー 7 ロード バランサーをプロビジョニングできます。

次の表では、Application Gateway インスタンスごとにパフォーマンス スループットの平均値を示します。

| バック エンド ページの応答 | Small | 中 | Large|
|---|---|---|---|
| 6K | 7\.5 mbps | 13 mbps | 50 mbps |
|100k | 35 mbps | 100mbps| 200 mbps |


>[AZURE.NOTE]パフォーマンスの数値は、Application Gateway に返される Web アプリケーション HTTP 応答にも依存します。


## 監視
 
Application Gateway では、プローブ ポートを使用してバックエンド インスタンスの正常性状態を監視し、ゲートウェイの HttpSettings セクションからの HTTP 応答を定期的にテストします。このプローブでは、200 ～ 390 応答コードの範囲で正常な HTTP 応答を予想し、30 秒ごとにバック エンド IP アドレスをテストして HTTP 応答があるかどうかを確認します。

正常な HTTP 応答が受信されると、IP アドレスには健全な状態であることを示すマークが付けられます。プローブが失敗した場合、IP アドレスは正常バック エンド プールから削除され、このサーバーへのトラフィックの送信は停止します。正常性プローブは、失敗した Web インスタンスがオンラインに戻るまで、30 秒ごとにそのインスタンスへのアクセスを継続します。Web インスタンスが正常性プローブに正常に応答すると、Web インスタンスは正常バック エンド プールに戻され、この Web インスタンスへのトラフィックの送信が再開されます。

## 構成と管理

REST API や PowerShell コマンドレットを使用して、Application Gateway を作成および管理できます。


## 次のステップ

Application Gateway を作成します。「[アプリケーション ゲートウェイの作成](application-gateway-create-gateway.md)」を参照してください。

SSL オフロードを構成します。「[SSL オフロードのアプリケーション ゲートウェイの構成](application-gateway-ssl.md)」を参照してください。

<!---HONumber=Nov15_HO2-->