---
title: 使用可能なコネクタと API Apps の一覧 | Microsoft Docs
description: Azure App Service のコネクタと API Apps に関するページを参照してください。
services: logic-apps
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: cgronlun

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: mandia

---
# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>Logic Apps で使用するコネクタと API Apps の一覧
> [!NOTE]
> 本記事は、ロジック アプリの 2014-12-01-preview スキーマ バージョンを対象としています。 Logic Apps の一般公開 (GA) バージョンについては、 [新しいコネクタの一覧](../connectors/apis-list.md)をご覧ください。
> 
> 

このトピックでは、マイクロソフトによって作成された、Logic Apps 内で使用できるすべてのコネクタと API Apps を示します。

料金情報および各サービス レベルに含まれる新機能の一覧については、「 [Azure App Service の価格](https://azure.microsoft.com/pricing/details/app-service/)」を参照してください。

> [!NOTE]
> Azure アカウントにサインアップせずに Logic Apps を体験するには、[Logic Apps の試用](https://tryappservice.azure.com/?appservice=logic)に関するページにアクセスしてください。 App Service で短時間有効な簡易版のロジック アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="core-connectors"></a>コアのコネクタ
次の表に、コアのコネクタとして使用できる、マイクロソフトによって作成された使用可能なすべてのコネクタと API Apps を示します。

| 名前 | Description |
| --- | --- |
| [Bing 翻訳ツール](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) |Bing を使用して、テキストを別の言語に翻訳します。 |
| [HTTP](app-service-logic-connector-http.md) |HTTP リスナーは、HTTP サーバーとして機能するエンドポイントを開き、着信 HTTP または HTTPS 要求をリッスンします。 HTTP アクションに API アプリは必要ありません。HTTP アクションは Logic Apps 内でネイティブにサポートされます。 |
| [Slack](app-service-logic-connector-slack.md) |Slack に接続し、Slack チャネルにメッセージを投稿します。 |

## <a name="enterprise-integration-connectors"></a>エンタープライズ統合コネクタ
次の表に、エンタープライズ統合コネクタとして使用できる、マイクロソフトによって作成された使用可能なすべてのコネクタと API Apps を示します。

| 名前 | Description |
| --- | --- |
| [BizTalk ルール](app-service-logic-use-biztalk-rules.md) |BizTalk ルールは組織内のビジネス ロジックを定義し、制御します。 ビジネス ポリシーは、関連付けられているアプリケーションの再コンパイルや再デプロイなしで更新できます。 |
| [BizTalk XPath Extractor](app-service-logic-xpath-extract.md) |選択した XPath に基づいて XML コンテンツを検索し、データを抽出します。 |
| [DB2 コネクタ](app-service-logic-connector-db2.md) |Windows オペレーティング システムを実行している Azure 仮想マシン上およびオンプレミスの IBM DB2 データベースに接続します。 Informix Structured Query Language (Informix 構造化クエリ言語) のコマンドに Web API と OData API の操作をマップできます。 <br/><br/>トリガーはありません。 アクションには、テーブルの選択、挿入、更新、削除、およびカスタム ステートメントが含まれます。<br/><br/>このコネクタには、TCP/IP ネットワーク経由で Informix サーバーに接続するための Microsoft Client for DRDA も含まれています。 |
| [ファイル](app-service-logic-connector-file.md) |このコネクタを使用すると、オンプレミスのファイル システムやネットワークに接続して、ファイルのアップロード、削除、一覧の取得などのさまざまなファイル タスクを実行できます。 |
| [Informix](app-service-logic-connector-informix.md) |Windows オペレーティング システムを実行している Azure 仮想マシン上およびオンプレミスの IBM Informix データベースに接続します。 Informix Structured Query Language (Informix 構造化クエリ言語) のコマンドに Web API と OData API の操作をマップできます。<br/><br/>トリガーはありません。 アクションには、テーブルの選択、挿入、更新、削除、およびカスタム ステートメントが含まれます。<br/><br/>オンプレミスで使用する場合は、VPN または Azure ExpressRoute を使用できます。 このコネクタには、TCP/IP ネットワーク経由で Informix サーバーに接続するための Microsoft Client for DRDA も含まれています。 |
| [Microsoft SQL Server](app-service-logic-connector-sql.md) |オンプレミスの SQL Server または Azure SQL Database に接続します。 SQL データベース テーブルのエントリを作成、更新、取得、削除できます。 |
| MQ |Windows オペレーティング システムを実行している Azure 仮想マシン上およびオンプレミスの IBM WebSphere MQ Server Version 8 に接続します。 オンプレミスで使用する場合は、VPN または Azure ExpressRoute を使用できます。 コネクタには、Microsoft Client for MQ も含まれています。<br/><br/>トリガーはありません。 アクションはありません。<br/><br/>**注**: 現在のところ、Logic Apps で使用することはできません。 |

## <a name="connectors-as-triggers"></a>トリガーとしてのコネクタ
いくつかのコネクタは、Logic Apps のトリガーとして提供されています。 これらのトリガーには、次の 2 種類があります。

1. ポーリング トリガー: このトリガーは、指定された頻度でサービスをポーリングして、新しいデータをチェックします。 新しいデータを利用できる場合、そのデータを入力値としてロジック アプリの新しいインスタンスが実行されます。 同じデータが複数回使用されることを防ぐために、ロジック アプリで読み取られて渡されるデータのクリーンアップをトリガーで実行される場合があります。 このようなコネクタには、ファイル、SQL、Azure Storage があります。
2. プッシュ トリガー: このトリガーは、エンドポイント上のデータまたは発生するイベントをリッスンします。 その後、ロジック アプリの新しいインスタンスをトリガーします。 このようなコネクタには、HTTP リスナーや Twitter があります。

## <a name="connectors-as-actions"></a>アクションとしてのコネクタ
コネクタは、ロジック アプリ内でアクションとして使用することもできます。 アクションは、実行時に使用するロジック アプリ内のデータの検索に役立ちます。 たとえば、注文を処理するときに、顧客に関する情報を求めて SQL データベースのデータを検索することが必要になる場合があります。 または、変換先のデータの書き込み、更新、または削除が必要になる場合があります。 これらを実行するために、コネクタによって提供されるアクションを使用できます。 アクションは、Swagger メタデータで定義されているとおりに、API Apps 内の操作と対応付けられています。

## <a name="create-your-own-connectors-and-api-apps"></a>独自のコネクタと API Apps の作成
[Connectors and API Apps Reference (コネクタと API Apps リファレンス)](http://aka.ms/appservicesconnectorreference)  
[Azure App Service API アプリ トリガー](../app-service-api/app-service-api-dotnet-triggers.md)  
[Logic Apps のリファレンス](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>コネクタと API Apps の詳細
[コネクタと BizTalk API Apps とは](app-service-logic-what-are-biztalk-api-apps.md)  
[Azure App Service での Hybrid Connection Manager の使用](app-service-logic-hybrid-connection-manager.md)  
[組み込み API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)

<!--HONumber=Oct16_HO2-->


