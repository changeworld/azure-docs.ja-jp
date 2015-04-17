<properties 
	pageTitle="コネクタと BizTalk API Apps とは" 
	description="API Apps、コネクタ、および BizTalk API Apps について説明します。" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="joshtwist" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="jtwist"/>

# コネクタと BizTalk API Apps とは

Azure App Services は、API Apps を介した拡張性と共通の接続性の原則の上に構築されます。 *Connector* は、接続に焦点を絞った API App の一種です。他の API アプリと同様に、コネクタは Web アプリ、モバイル アプリ、およびロジック アプリから使用されます。コネクタは、既存のサービスとの接続を容易にし、認証の管理、監視、分析などに役立ちます。

すべての開発者が独自の API アプリを作成して個人でデプロイし、今後 Marketplace を介して共有して収益化できるようになります。 

![API Apps Marketplace](./media/app-service-learn-about-flows-preview/Marketplace.png)

開発者が Azure App Services を使用してソリューションを構築するのを促進する目的で、Microsoft Azure チームによって、一般的なシナリオを満たすためにコネクタがいくつか Marketplace に追加されました。さらに、App Services の守備範囲を複雑で高度な統合シナリオまで拡張するには、Premium、および BizTalk の機能も使用できます。

[Microsoft Azure App Service のコネクタと API Apps の一覧](app-service-logic-connectors-list.md)


## プロトコル コネクタ
App Services は Web から生まれ、HTTP を使用し、Swagger などのオープンな形式でのメタデータを共有する通信に向いています。もちろん、企業はさまざまなプロトコルで通信する必要があります。プロトコル コネクタは、HTTP 呼び出しと同じくらい簡単に FTP、SFTP、POP3 や IMAP、SMTP、および SOAP サービスを使用するサービスと通信して、ギャップを埋めることができます。  

[Microsoft Azure App Service のプロトコル コネクタ](app-service-logic-protocol-connectors.md)


## SaaS コネクタ
App Service の SaaS コネクタによって、Office 365、SalesForce、Sugar CRM、OneDrive、DropBox、Marketo、Facebook さえも含む今日の SaaS のビッグ ネームの一部に接続するためのターンキー型が提供されます。

[Microsoft Azure App Service のソーシャル コネクタ](app-service-logic-social-connectors.md)

[Microsoft Azure App Service のアプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)


## Premium コネクタ 
Premium コネクタによって、企業と SAP、Siebel、Oracle、DB2 などの接続まで、App Services の守備範囲が広がります。

[Microsoft Azure App Service のエンタープライズ コネクタ](app-service-logic-enterprise-connectors.md)


## BizTalk API Apps
ビジネス クリティカルなアプリケーションの構築に必要なのは、接続だけではありません。BizTalk API Apps は、マイクロソフトの業界屈指の統合プラットフォームである BizTalk Server を基礎にして、Web アプリ、モバイル アプリ、ロジック アプリにスナップできる高度な統合機能を簡単に提供しています。バッチ処理とデバッチ処理、VETR (検証、抽出、変換、ルーティング)、および X12、EDIFACT、AS2 などの EDI 形式のサポートが含まれます。

[Microsoft Azure App Service の企業間コネクタと API Apps](app-service-logic-b2b-connectors.md)<br/>

[Microsoft Azure App Service の BizTalk 統合 API Apps](app-service-logic-integration-connectors.md)

<!--HONumber=49-->