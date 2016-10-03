<properties 
	pageTitle="Enterprise Integration の概要 | Microsoft Azure App Service | Microsoft Azure" 
	description="Enterprise Integration の機能を使用することにより、Logic Apps を使ってビジネス プロセスと統合シナリオを実現できます。" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2016" 
	ms.author="deonhe"/>

# Enterprise Integration Pack の概要

## Enterprise Integration Pack とは
Enterprise Integration Pack は、企業間 (B2B) の通信をシームレスに実現するクラウドベースの Microsoft ソリューションです。このパックでは、ビジネス パートナー間でのメッセージの交換に、[AS2](./app-service-logic-enterprise-integration-as2.md)、[X12](./app-service-logic-enterprise-integration-x12.md)、[EDIFACT](./app-service-logic-enterprise-integration-edifact.md) などの業界標準のプロトコルを使用します。メッセージは、暗号化とデジタル署名の両方を使用して、必要に応じてセキュリティ保護できます。

このパックを使用すれば、互いに異なるプロトコルと形式が使用されている組織の間で、メッセージを電子的に交換できます。これは、異なる形式を、どちらの組織のシステムでも解釈してアクションを実行できる形式に変換することによって行います。

BizTalk Server または Microsoft Azure BizTalk Services を使い慣れている方は、ほとんどの概念が類似しているため、Enterprise Integration の機能を使いやすいと感じるものと思われます。主な違いの 1 つは、B2B 通信で使用されるアーティファクトの保管と管理を簡単にするために、Enterprise Integration では統合アカウントを使用する点です。

Enterprise Integration Pack のアーキテクチャは、B2B アプリの設計、デプロイ、管理に使用できるすべてのアーティファクトが格納される**統合アカウント**をベースにしています。統合アカウントは基本的に、スキーマ、パートナー、証明書、マップ、契約などのアーティファクトが格納されるクラウドベースのコンテナーです。そして、これらのアーティファクトは、B2B ワークフローを構築するロジック アプリで使用できます。ロジック アプリでアーティファクトを使用する前に、統合アカウントとロジック アプリの関連付けを行っておく必要があります。これらの関連付けが終わったら、ロジック アプリは統合アカウントのアーティファクトにアクセスできるようになります。

## Enterprise Integration を使用する理由
- Enterprise Integration を使用すれば、対象のアーティファクトをすべて 1 か所 (統合アカウント) に格納できます。
- Logic Apps エンジンとそのコネクタすべてを利用することで、B2B ワークフローを構築し、サード パーティの SaaS アプリケーション、オンプレミスのアプリケーション、カスタム アプリケーションを統合できます。
- また、Azure Functions を利用することもできます。

## Enterprise Integration の使用を開始する方法
**Azure ポータル**の Logic Apps デザイナーを利用して、Enterprise Integration Pack を使用した B2B アプリを構築、管理できます。

Logic Apps の管理に [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "Logic Apps PowerShell 関連トピック") を使用することもできます。

Azure ポータルでアプリを作成する前に必要な手順の概要は、次のとおりです。![概要のイメージ](./media/app-service-logic-enterprise-integration-overview/overview-0.png)

## いくつかの一般的なシナリオ

Enterprise Integration では、次の業界標準がサポートされています。

- EDI - Electronic Data Interchange (電子データ交換)
- EAI - Enterprise Application Integration (エンタープライズ アプリケーション統合)

## 使用を開始するために必要なもの
- 統合アカウントが付属した Azure サブスクリプション
- マップとスキーマを作成するための Visual Studio 2015
- [Microsoft Azure Logic Apps Enterprise Integration Tools for Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)

## 試してみる
Logic Apps の B2B 機能を使用する、完全に動作する AS2 送信および受信ロジック アプリのサンプルをデプロイするには、[こちら](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)をクリックしてください。

## 各項目の詳細情報
- [アグリーメント](./app-service-logic-enterprise-integration-agreements.md "エンタープライズ統合契約についての詳細情報")
- [企業間 (B2B) シナリオ](./app-service-logic-enterprise-integration-b2b.md "B2B 機能を使用したロジック アプリの作成方法についての詳細情報")
- [証明書](./app-service-logic-enterprise-integration-certificates.md "Enterprise Integration の証明書についての詳細情報")
- [フラット ファイル エンコード/デコード](./app-service-logic-enterprise-integration-flatfile.md "フラット ファイル コンテンツをエンコードおよびデコードする方法についての詳細情報")
- [統合アカウント](./app-service-logic-enterprise-integration-accounts.md "統合アカウントについての詳細情報")
- [マップ](./app-service-logic-enterprise-integration-maps.md "Enterprise Integration のマップについての詳細情報")
- [パートナー](./app-service-logic-enterprise-integration-partners.md "Enterprise Integration のパートナーについての詳細情報")
- [スキーマ](./app-service-logic-enterprise-integration-schemas.md "Enterprise Integration のスキーマについての詳細情報")
- [XML メッセージの検証](./app-service-logic-enterprise-integration-xml.md "ロジック アプリを使用した XML メッセージの検証方法についての詳細情報")
- [XML 変換](./app-service-logic-enterprise-integration-transform.md "Enterprise Integration のマップについての詳細情報")
- [エンタープライズ統合コネクタ](../connectors/apis-list.md "Enterprise Integration Pack コネクタについての詳細情報")

<!---HONumber=AcomDC_0921_2016-->