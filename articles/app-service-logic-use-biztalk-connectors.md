<properties 
   pageTitle="コネクタの使用" 
   description="コネクタの使用" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="prkumar" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="prkumar"/>


#コネクタの使用#

コネクタは、データの GET や PUT、アクションの実行のために、クラウドやオンプレミスで実行されるサービスに接続するための API アプリです。Azure App Services 全体にわたって、Web アプリ、モバイル アプリ、およびロジック アプリから使用できます。

Azure App Services は、目的のサービスに接続するための、設定が不要なコネクタをいくつか提供することで、アプリケーションの実際のビジネス ロジックに集中して取り組めるようにしています。さらに、プラットフォームは、レガシ アプリケーションに接続する独自 API アプリを記述することで簡単に拡張できます。

BizTalk コネクタの主な機能には次のものがあります。

1. よく使用される SaaS サービス (Facebook、Twitter、Office 365、SalesForce) に接続するための OAuth セキュリティの処理。
2. Service Bus Relay を経由したオンプレミスの資産への接続。
3. データを送受信するための一般的なプロトコル用の接続。

##コネクタとロジック アプリ##
ロジック アプリはトリガーとアクションで構成されます。一部のコネクタは、新しいデータが使用可能かどうかを特定し、そのデータを入力値として使用するロジック アプリをトリガーするためのトリガーとして使用できます。データの検索や書き込み、コネクタでサポートされているその他のアクションを実行するために、コネクタをロジック アプリの中間でアクションとして使用することもできます。

###トリガーとしてのコネクタ###
いくつかのコネクタは、ロジック アプリのトリガーとして提供されています。これらのトリガーには、次の 2 種類があります。

1. ポーリング トリガー: このトリガーは、指定された頻度で目的のサービスをポーリングして、新しいデータをチェックします。新しいデータを利用できる場合、そのデータを入力値としてロジック アプリの新しいインスタンスが実行されます。ロジック アプリで読み取られて渡されるデータのクリーンアップなどの他のタスクをトリガーで実行して、同じデータが複数回使用されることを防いでいる場合があります。このようなコネクタには、ファイル、SQL、Azure のストレージがあります。
2. プッシュ トリガー: このトリガーは、エンドポイント上のデータまたは発生するイベントをリッスンして、ロジック アプリの新しいインスタンスをトリガーします。このようなコネクタには、HTTP リスナーや Twitter があります。

###アクションとしてのコネクタ###
コネクタは、ロジック アプリの一部を構成するアクションとして使用することもできます。これは、実行時に使用するロジック アプリ内のデータの検索に役立ちます。たとえば、ロジック アプリで顧客の注文を処理するときに、顧客に関する情報を求めて SQL データベースを検索することが必要になる場合があります。または、変換先のデータを書き込み、更新、または削除する必要があります。このために、コネクタによって提供されるアクションを使用できます。

##コネクタを使用する方法##
次の記事では、設定不要な一部のコネクタの使用方法の例を説明しています。

* [HTTP コネクタの使用]
* [HTTP コネクタの使用]
* [ボックス コネクタの使用]
* [Azure ストレージ BLOB コネクタの使用]
* [Facebook コネクタの使用]
* [Twitter コネクタの使用]
* [Salesforce コネクタの使用]
* [Office 365 コネクタの使用]
* [Sharepoint コネクタの使用]
* [SAP コネクタの使用]

その他の詳細については、コネクタ API リファレンス [http://aka.ms/appservicesconnectorreference](http://aka.ms/appservicesconnectorreference) を参照してください。


<!-- Links -->

[ボックス コネクタの使用]: app-service-logic-connector-box.md
[Facebook コネクタの使用]: app-service-logic-connector-facebook.md
[Salesforce コネクタの使用]: app-service-logic-connector-salesforce.md
[Twitter コネクタの使用]: app-service-logic-connector-twitter.md
[SAP コネクタの使用]: app-service-logic-connector-sap.md
[HTTP コネクタの使用]: app-service-logic-connector-ftp.md
[HTTP コネクタの使用]: app-service-logic-connector-http.md
[Azure ストレージ BLOB コネクタの使用]: app-service-logic-connector-azurestorageblob.md
[Office 365 コネクタの使用]: app-service-logic-connector-office365.md
[Sharepoint コネクタの使用]: app-service-logic-connector-sharepoint.md


<!--HONumber=49-->