<properties 
	pageTitle="Microsoft Azure App Service の BizTalk 統合 API Apps | Azure" 
	description="マイクロサービス アーキテクチャの BizTalk 統合 API Apps を作成および構成する方法について説明します。" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="mandia"/>


# Microsoft Azure App Service の BizTalk 統合 API Apps
Microsoft Azure App Service (略称: App Service) には、統合環境に不可欠な数多くの BizTalk API Apps が含まれています。これらの API Apps は、BizTalk Server 内で使用される概念およびツールに基づいていますが、Azure App Service の一部として利用できるようになりました。

これらの API Apps のカテゴリの 1 つに BizTalk 統合 API Apps があります。BizTalk API Apps を使用すると、ビジネス ルールの追加、XML メッセージの変換と検証、フラット ファイルと JSON データのエンコードなどを簡単に実行できます。オンプレミスの BizTalk Server を使用しているのと変わりません。

これらの統合 API Apps は、"アクション" 機能を提供します。アクションとは結果です。XML メッセージの受信後、XML スキーマを基準として XML メッセージを検証するのに似ています。


## 統合 API Apps とは
BizTalk 統合 API Apps は、データ処理を実行し、出力を生成することができる、組み込み済の API Apps です。たとえば、これらの API Apps には、さまざまなファイル形式と連携して、ビジネス ロジックやアプリケーション ロジックを適用できるものがあります。統合 API Apps とは次のとおりです。

API Apps | 説明
--- | ---
<ul><li>X12 API アプリ</li><li>AS2 コネクタ</li><li>EDIFACT API アプリ</li><li>取引先管理 API アプリ</li> | これらの API アプリは、企業間取引の機能を提供しています。詳細については、[企業間コネクタ](app-service-logic-b2b-connectors.md)に関するページを参照してください。
BizTalk Flat File Encoder | **アクション** API アプリ。フラット ファイル データ (Excel、csv など) と XML データを連携します (相互運用性)。フラット ファイル インスタンスを XML に、またはその逆方向に変換できます。
BizTalk JSON Encoder | **アクション** API アプリ。JSON データと XML データを連携します (相互運用性)。JSON インスタンスを XML に、またはその逆方向に変換できます。
BizTalk ルール | **アクション** API アプリ。ビジネス ロジックまたは "ルール" を実装して適用し、ビジネス オペレーションを制御できます。ルールは動的であり、いつでも変更できます。BizTalk ルールにより、コードを記述しなくてもビジネス ロジックを有効できます。アプリケーションに影響を与えることなく簡単に更新できます。
BizTalk 変換 | **アクション** API アプリ。データをある形式から別の形式に変換します。文字列の操作、算術式の完成、日付と時刻の書式設定などに、さまざまな組み込み関数が使用できます。 
BizTalk XML Validator | **アクション** API アプリ。定義済みの XML スキーマを基準として XML データを検証します。フラット ファイル インスタンス、JSON インスタンス、または既存のコネクタに基づいたスキーマを使用できます。 
BizTalk XPath Extractor | **アクション** API アプリ。特定の XPath に基づいて、XML コンテンツからデータを参照して抽出します。
待機 | 入力した期間中、または特定の時刻まで実行を延期します。ロジック アプリに追加すると、アプリ全体の実行を遅延させることができます。


	> [AZURE.NOTE] 属性 ("<authorid= ”1”>abc</author>" など) を持つ単純なノードが入力 xml にある場合、ライブラリの JSON の出力は { “author”: { “@id” : “1”, “#text”: “abc”}}. "Id" 属性を処理するために、新しい "#text" キーがノードのテキスト コンテンツに追加されます。 この種のノードを処理するには、定数のキーを追加します。 Newtonsoft.Json ライブラリではこのように設計されています。 定数のキーを SQL に挿入するとき、"JsonOutput.Author" ではなく、"JSONOutput.Author.#text" を使用してください。

これらの API Apps を使用して、さまざまなメッセージ処理やデータのタスクを完了することができます。たとえば、BizTalk ルール API アプリを使用すると、注文を受信し、特定の数量が注文されたときに割引を適用することができます。または、郵便番号に従って、特定の税率を課すことができます。

API アプリは必要な数だけいくつでも簡単に作成することができます。複数のシナリオまたはワークフロー内で API Apps を再利用することもできます。

たとえば、顧客が製品を 100 個注文すると、10% の割引を適用するビジネス ポリシーがあるとします。アプリでは、BizTalk ルール API アプリを追加できます。このアプリは注文された数量を確認して、数量が 100 を超えている場合は 10% 割引を適用します。

このビジネス ポリシーを拡張することもできます。North Carolina での売上の増加を目標にしているとします。100 個以上の注文について 10% 割り引くだけでなく、North Carolina から注文された場合は送料も無料にします。既存の BizTalk ルールにこの North Carolina の条件を簡単に追加することができます。

API Apps を使用して、コードを記述することなくこれを実行できます。それでは始めましょう。


## API Apps の作成
統合 API Apps は、Azure ポータルまたは REST API を使用して作成できます。


### REST API を使用して API Apps を作成する
[REST API](http://go.microsoft.com/fwlink/p/?LinkId=529766) に関するページを参照してください。


### Azure ポータルで統合 API Apps を作成する
Azure ポータルで、Logic Apps、Web アプリ、またはモバイル アプリを作成するときに BizTalk 統合 API アプリを作成できます。または、独自のブレードを使用して作成することもできます。どちらの方法も簡単なため、ニーズや好みに応じて使い分けることができます。特定のプロパティを持つすべての統合 API アプリを最初に作成するユーザーもいます。次に Logic Apps、Web アプリ、またはモバイル アプリを作成し、作成した統合 API Apps を追加します。

次の手順では、API Apps のブレードを使用して、BizTalk 統合 API Apps を作成します。

1. Azure ポータルのスタート画面 (ホーム ページ) で、**[Marketplace]** を選択します。**[API Apps]** に、既存のすべての API Apps とコネクタが一覧表示されます。特定の BizTalk API アプリを**検索**することもできます。
2. API アプリを選択します。新しいブレードで **[作成]** を選択します。 
3. 次のプロパティを入力します。 

	プロパティ | 説明
--- | ---
名前 | API アプリに対して任意の名前を入力します。たとえば、*RulesDiscountTaxCode* や *APIAppValidateXML* などの名前にできます。
App Service プラン | 支払プランの一覧を表示します。プランは必要なリソースの増減に応じて変更することができます。
価格レベル | Azure サブスクリプション内での価格のカテゴリの一覧を表示する読み取り専用プロパティです。 
リソース グループ | 新しいリソース グループを作成するか、既存のグループを使用します。ロジック アプリ、Web アプリ、モバイル アプリのすべての API Apps とコネクタが同じリソース グループに属している必要があります。<br/><br/>[リソース グループの使用](../resource-group-overview.md)に関するページに、このプロパティについての説明があります。 
[サブスクリプション] | 現在のサブスクリプションを一覧表示する読み取り専用のプロパティです。
場所 | Azure サービスをホストする地理的な場所です。 
スタート画面への追加 | これを選択して、統合 API Apps をスタート画面 (ホーム ページ) に追加します。


## BizTalk API Apps を構成する
Azure の管理ポータルで、BizTalk API アプリを開きます。**[コンポーネント]** セクションで、API アプリの完成に必要なその他のコンポーネントを追加することができます。

	API App | Tasks
--- | ---
BizTalk Flat File Encoder | Excel や csv ファイルなど、XML に変換するフラット ファイルを入力します。または、フラット ファイルに変換する XML ファイルを入力します。
BizTalk JSON Encoder | XML に変換する JSON ファイルを入力します。または、JSON に変換する XML ファイルを入力します。 
BizTalk ルール | ボキャブラリを追加し、IF THEN ルールを作成します。「[BizTalk のルールを使用する](app-service-logic-use-biztalk-rules.md)」を参照してください。 
BizTalk 変換 | マップを追加し、入力 XML スキーマと出力 XML スキーマを入力します。出力 XML スキーマに一致するように受信メッセージやデータを操作するには、組み込み関数を使用します。「[XML ドキュメントを変換する](app-service-logic-transform-xml-documents.md)」を参照してください。 
BizTalk XML Validator | 定義済みの XML スキーマを基準にして検証する XML を入力します。フラット ファイル インスタンス、JSON インスタンス、または既存のコネクタに基づいたスキーマを使用できます。 
BizTalk XPath Extractor | 特定の XPath に基づいて、XML コンテンツからデータを参照して抽出します。
待機 | Web アプリ、モバイル アプリ、またはロジック アプリを実行する期間または特定の時刻を入力します。


## API Apps の監視
Azure の管理ポータルで、BizTalk API アプリを開きます。**[操作]** セクションでは、さまざまな管理操作を表示できます。たとえば、次のようなことができます。

- 情報イベントやエラー イベントの表示
- ワーカー プロセス (w3wp) のメモリ使用量とスレッド数の表示
- アプリケーションおよび web サーバーのログを表示します。

詳細については、「[ロジック アプリを監視する](app-service-logic-monitor-your-logic-apps.md)」を参照してください。


## アプリケーションに BizTalk API Apps を追加する 
Microsoft Azure App Service は、これらの統合 API アプリを使用できるさまざまな種類のアプリケーションを公開しています。新しい BizTalk API Apps を作成するか、既存の BizTalk API Apps をロジック アプリ、モバイル アプリ、または Web アプリに追加することができます。

アプリケーション内でギャラリーから BizTalk API アプリを選択するだけで、選択した BizTalk API アプリがアプリケーションに自動的に追加されます。

次の手順で、BizTalk API アプリをロジック アプリ、モバイル アプリ、または Web アプリに追加します。

1. Azure ポータルのスタート画面 (ホーム ページ) で、**Marketplace** に移動し、Logic Apps、Mobile Apps、または Web Apps を検索します。 

	新しいアプリを作成する場合は、ロジック アプリ、モバイル アプリ、または Web アプリを検索します。アプリを選択し、新しいブレードで **[作成]** を選択します。[ロジック アプリの作成](app-service-logic-create-a-logic-app.md)に関するページに、この手順が記載されています。

2. アプリを開き、**[トリガーとアクション]** を選択します。

3. **ギャラリー**から BizTalk API アプリを選択します。これは自動的にアプリケーションに追加されます。

4. **[OK]** を選択して変更を保存します。


## 統合 API Apps の他のリソース
[VETR を使用して EAI ロジック アプリを作成する](app-service-logic-create-EAI-logic-app-using-VETR.md)<br/>
[XML ドキュメントを変換する](app-service-logic-transform-xml-documents.md)<br/>
[BizTalk のルールを使用する](app-service-logic-use-biztalk-rules.md)<br/>
[コネクタと BizTalk API Apps とは](app-service-logic-what-are-biztalk-api-apps.md)


## Logic Apps と Web Apps に関するガイド
[Logic Apps とは](app-service-logic-what-are-logic-apps.md)<br/>
[Azure App Service の Websites と Web Apps](../app-service-web/app-service-web-app-azure-portal.md)


## その他のコネクタ
[エンタープライズ コネクタ](app-service-logic-enterprise-connectors.md)<br/>
[企業間コネクタ](app-service-logic-b2b-connectors.md)<br/>
[ソーシャル コネクタ](app-service-logic-social-connectors.md)<br/> [プロトコル コネクタ](app-service-logic-protocol-connectors.md)<br/>
[アプリ + データ サービス コネクタ](app-service-logic-data-connectors.md)<br/>
[コネクタと API Apps の一覧](app-service-logic-connectors-list.md)<br/><br/>
[コネクタと BizTalk API Apps とは](app-service-logic-what-are-biztalk-api-apps.md)
<!--HONumber=54--> 