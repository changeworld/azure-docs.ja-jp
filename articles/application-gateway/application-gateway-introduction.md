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
   ms.date="06/24/2015"
   ms.author="joaoma"/>

# Application Gateway の技術概要 


Microsoft Azure Application Gateway は、Azure VPN Gateway と同様に、Azure で管理されるサービスです。Application Gateway は、IIS Application Request Routing (ARR) に基づく、Azure で管理される HTTP の負荷分散ソリューションを提供します。このアプリケーション ゲートウェイ サービスは可用性が高く、従量課金制で使用できます。価格と SLA については、[SLA](http://azure.microsoft.com/support/legal/sla/) と[価格](https://azure.microsoft.com/pricing/details/application-gateway/)のページを参照してください。

Application Gateway は現在、次のレイヤー 7 アプリケーションの配信をサポートします。

- HTTP の負荷分散
- cookie ベースのセッション アフィニティ
- SSL オフロード

![Application Gateway](./media/application-gateway-introduction/appgateway1.png)

## HTTP レイヤー 7 の負荷分散
Azure では、ソフトウェアのロード バランサーを使用したレイヤー 4 の負荷分散を提供します。これは、負荷分散された VIP (パブリックまたは内部) があるすべてのクラウド サービスで暗黙的に発生します。ただし、レイヤー 7 (HTTP) ベースの負荷分散に使用できるアプリケーションは数多く存在します。


HTTP レイヤー 7 の負荷分散は、次の場合に役立ちます。


- 同じバックエンド VM に到達するために同じユーザー/クライアントのセッションからの要求を必要とするアプリケーション。この例としては、ショッピング カート アプリや Web メール サーバーなどが挙げられます。
- Web サーバーのファームを SSL 終了オーバーヘッドから解放する必要があるアプリケーション。
- 実行時間の長い同じ TCP 接続で複数の HTTP 要求を異なるバックエンド サーバーにルーティング/負荷分散する必要があるアプリケーション (CDN など)。

## ゲートウェイのサイズとインスタンス

Application Gateway は現在、Small、Medium、および Large の 3 つのサイズで提供されています。Small サイズのインスタンスは、開発用およびシナリオのテスト用です。

サブスクリプションごとに最大 10 個のアプリケーション ゲートウェイを作成できるほか、各アプリケーション ゲートウェイには最大 10 個のインスタンスが存在します。Azure で管理されるサービスとしての Application Gateway の負荷分散により、Azure のソフトウェア ロード バランサーの背後でレイヤー 7 ロード バランサーをプロビジョニングできます。

## 構成と管理

REST API や PowerShell コマンドレットを使用して、アプリケーション ゲートウェイを作成および管理できます。

## 次のステップ

アプリケーション ゲートウェイを作成します。「[アプリケーション ゲートウェイの作成](application-gateway-create-gateway.md)」を参照してください。

SSL オフロードを構成します。「[SSL オフロードのアプリケーション ゲートウェイの構成](application-gateway-ssl.md)」を参照してください。

<!---HONumber=July15_HO4-->