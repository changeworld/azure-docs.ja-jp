<properties 
	pageTitle="コネクタと BizTalk API Apps とは" 
	description="API Apps、コネクタ、および BizTalk API Apps について説明します。" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/18/2016" 
	ms.author="mandia"/>

# コネクタと BizTalk API Apps とは
>[AZURE.NOTE] 本記事は、ロジック アプリの 2014-12-01-preview スキーマ バージョンを対象としています。

Azure App Services は、API Apps を介した拡張性と共通の接続性の原則の上に構築されます。*コネクタ*は、接続に焦点を絞った API アプリの一種です。他の API アプリと同様に、コネクタは Web アプリ、モバイル アプリ、およびロジック アプリから使用されます。コネクタは、既存のサービスとの接続を容易にし、認証の管理、監視、分析などに役立ちます。

開発者は独自の API アプリを作成し、個人的にデプロイできます。将来的には、開発者は独自に作成した API アプリを Marketplace で共有して収益化できます。

![API Apps Marketplace](./media/app-service-logic-what-are-biztalk-api-apps/Marketplace.png)

開発者が Azure App Service を利用してソリューションを構築しやすいように、Azure チームによって、一般的なシナリオに対応するコネクタがいくつか Marketplace に追加されています。さらに、App Service の対応範囲を複雑で高度な統合シナリオにまで広げるために、Premium と BizTalk の機能も提供されています。

Azure App Service では、さまざまなサービス レベルを提供しています。どのレベルにもすべてのコネクタと API アプリが含まれ、その全機能を使用できます。

[App Service の料金](https://azure.microsoft.com/pricing/details/app-service/)に関するページでは、これらのサービス レベルについて説明し、各レベルに含まれる機能を表にして示しています。次のセクションでは、BizTalk API アプリとコネクタのさまざまなカテゴリについて説明します。


## ハイブリッド コネクタ 
ハイブリッド コネクタによって、企業と [SAP](app-service-logic-connector-sap.md)、[Oracle](app-service-logic-connector-oracle.md)、[DB2](app-service-logic-connector-db2.md)、[Informix](app-service-logic-connector-informix.md)、WebSphere MQ の接続まで、App Services の対応範囲が広がります。

## EAI と EDI サービス
ビジネス クリティカルなアプリの構築に必要なのは、接続だけではありません。BizTalk API Apps は、マイクロソフトの業界屈指の統合プラットフォームである BizTalk Server を基礎にして、Web アプリ、モバイル アプリ、ロジック アプリにスナップできる高度な統合機能を簡単に提供しています。これらの統合機能の一部には、[検証](app-service-logic-xml-validator.md)、[抽出](app-service-logic-xpath-extract.md)、[変換](app-service-logic-transform-xml-documents.md)、[エンコーダー](app-service-logic-connector-jsonencoder.md)、[取引先管理](app-service-logic-connector-tpm.md)、EDI 形式 ([X12](app-service-logic-connector-x12.md)、[EDIFACT](app-service-logic-connector-edifact.md)、[AS2](app-service-logic-connector-as2.md) など) のサポートが含まれます。

その他のリソース: [企業間コネクタと API Apps](app-service-logic-b2b-connectors.md) [B2B プロセスを作成する](app-service-logic-create-a-b2b-process.md) [取引先契約の作成](app-service-logic-create-a-trading-partner-agreement.md) [B2B メッセージを追跡する](app-service-logic-track-b2b-messages.md)


## ルール
ビジネス ルールには、ビジネス プロセスを制御するポリシーと意思決定がカプセル化されます。通常、ルールは動的で、ビジネス プランや規則などのさまざまな理由により、時間の経過と共に変化します。[App Services の BizTalk ルール](app-service-logic-use-biztalk-rules.md)では、アプリケーション コードからこれらのポリシーを分離して、より簡単かつ迅速にプロセスを変更できるようにします。

## コネクタと API のアプリの一覧
Standard コネクタ、BizTalk EAI、Premium コネクタなどの各カテゴリに含まれるコネクタと API Apps の完全な一覧については、[コネクタと API Apps の一覧](app-service-logic-connectors-list.md)を参照してください。
 

<!---HONumber=AcomDC_0224_2016-->