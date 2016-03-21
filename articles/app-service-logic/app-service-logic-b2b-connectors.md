<properties 
	pageTitle="Microsoft Azure App Service の企業間コネクタと API Apps | Microsoft Azure" 
	description="EDI、EDIFACT、AS2、および TPM の各コネクタを作成および構成する方法 (マイクロサービス アーキテクチャ) について説明します。" 
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
	ms.topic="article" 
	ms.date="02/11/2016" 
	ms.author="mandia"/>

# Microsoft Azure App Service の企業間コネクタと API Apps
>[AZURE.NOTE] 本記事は、ロジック アプリの 2014-12-01-preview スキーマ バージョンを対象としています。

Microsoft Azure App Service (略称: App Service) には、統合環境に不可欠な数多くの BizTalk API Apps が含まれています。これらの API Apps は、BizTalk Server 内で使用される概念およびツールに基づいていますが、Azure App Service の一部として利用できるようになりました。

これらの API Apps のカテゴリの 1 つに企業間 (B2B) API Apps があります。これらの B2B API Apps を使用すると、パートナーの追加、契約の作成、さらに、EDI、AS2、EDIFACT を使用してオンプレミスであらゆる操作を実行することができます。

これらの B2B API Apps には、"トリガー" と "アクション" という機能があります。トリガーは、パートナーからの X12 メッセージの着信などの特定のイベントに応じて新しいインスタンスを起動します。アクションは、X12 メッセージを受け取った後に、AS2 を使用してこのメッセージを送信するなどの結果を指します。


## 企業間コネクタまたは API Apps とは
企業間 (B2B) 機能には、事前に構築された既存の API Apps が含まれています。この API Apps により、AS2、EDI、および EDIFACT を使用して、異なる企業、部門、アプリケーションなどの間で通信できるようになります。

B2B API Apps には、以下のものがあります。

コネクタまたは API Apps | 説明
--- | ---
BizTalk 取引先管理 | パートナーおよび契約を使用した企業間 (B2B) リレーションシップを作成する API アプリです。このリレーションシップでは、AS2、EDIFACT、および X12 プロトコルを使用します。<br/><br/>TPM API アプリは、AS2 コネクタ、X12 API アプリ、または EDIFACT API Apps の基本要件です。 
AS2 コネクタ | AS2 トランスポートを使用してメッセージを送受信するコネクタです。このコネクタはインターネット経由で安全かつ確実にデータを転送します。
BizTalk EDIFACT | EDIFACT を使用してメッセージを送受信する API アプリです。EDIFACT は、一般に UN/EDIFACT (United Nations/Electronic Data Interchange For Administration, Commerce and Transport) とも呼ばれており、さまざまな業界で広く使用されています。
BizTalk X12 | X12 プロトコルを使用してメッセージを送受信する API アプリです。X12 は、一般に ASC X12 (Accredited Standards Committee X12) とも呼ばれており、さまざまな業界で広く使用されています。 


これらの API Apps を使用すると、さまざまな EDI メッセージング タスクを実行できます。たとえば、AS2 コネクタを使用して、顧客、社内の人事部などの部門、または AS2 を使用するすべてのユーザーとの間で、種類の異なるメッセージ (EDI、XML、フラット ファイルなど) を安全に送受信することができます。

API Apps は必要な数だけいくつでも簡単に作成することができます。1 つの API アプリを複数のシナリオまたはワークフローで再利用することもできます。

これはすべてコードを記述せずに行うことができます。それでは始めましょう。


## 前提要件
B2B API Apps を作成するには、必要なリソースがいくつかあります。他の API Apps 内で使用できるようにするには、それらのリソースを事前に作成する必要があります。要件は次のとおりです。

要件 | 説明
--- | ---
Azure SQL Database | パートナー、スキーマ、証明書、契約などの B2B 項目を格納します。B2B API Apps ごとに専用の Azure SQL Database が必要です。<br/><br/>**注**: 接続文字列をこのデータベースにコピーしてください。<br/><br/>[Azure SQL Database を作成する方法](../sql-database/sql-database-get-started.md)
Azure BLOB Storage コンテナー | AS2 アーカイブが有効な場合に、メッセージのプロパティを格納します。AS2 メッセージをアーカイブする必要がない場合は、Storage コンテナーは必要ありません。<br/><br/>**注**: アーカイブを有効にする場合は、接続文字列をこの Blob Storage にコピーしてください。<br/><br/>[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)
Service Bus 名前空間とそのキー値 | X12 および EDIFACT のバッチ処理データを格納します。バッチ処理が不要な場合は、Service Bus 名前空間は必要ありません。<br/><br/>**注**: バッチ処理を有効にする場合は、これらの値をコピーしてください。<br/><br/>[Service Bus 名前空間を作成する方法](http://msdn.microsoft.com/library/azure/hh690931.aspx)
TPM インスタンス | BizTalk 取引先管理 (TPM) インスタンスは、AS2 コネクタ、X12 API アプリ、または EDIFACT API アプリを作成するために必要です。TPM API アプリを作成すると、TPM インスタンスが作成されます。<br/><br/>**注**: TPM API アプリの名前を確認してください。 


## API Apps の作成
B2B API Apps は、Azure ポータルまたは REST API を使用して作成できます。


### REST API を使用した API Apps の作成
[REST API の使用方法に関するドキュメントを参照してください。](http://go.microsoft.com/fwlink/p/?LinkId=529766)


### Azure ポータルでの B2B API Apps の作成
Azure ポータルでは、Logic Apps、Web Apps、または Mobile Apps を作成するときに、B2B API Apps を作成することができます。または、独自のブレードを使用して作成することもできます。どちらの方法も簡単なため、ニーズや好みに応じて使い分けることができます。独自のプロパティを持つ B2B API Apps を最初に作成するユーザーもいます。その後、Logic Apps/Web Apps/Mobile Apps を作成し、作成した B2B API Apps を追加します。

次の手順では、[API Apps] ブレードを使用して B2B API Apps を作成します。


#### BizTalk 取引先管理 (TPM) API Apps の作成

> [AZURE.NOTE] BizTalk 取引先管理 (TPM) インスタンスは、AS2 コネクタ、X12 API アプリ、または EDIFACT API アプリを作成するために必要です。TPM API アプリを作成すると、TPM インスタンスが作成されます。

TPM インスタンスを作成する手順は以下のとおりです。

1. Azure ポータルのスタート画面 (ホーム ページ) で、**[Marketplace]** を選択します。**[API Apps]** に、既存のすべての API Apps とコネクタが一覧表示されます。**[検索]** で特定の B2B API Apps を見つけることもできます。
2. **[BizTalk 取引先管理]** を選択します。新しいブレードで **[作成]** を選択します。 
3. 次のプロパティを入力します。 

	プロパティ | 説明
--- | ---
名前 | TPM インスタンスの任意の名前を入力します。たとえば、*AccountsPayableTPM* という名前を付けることができます。
パッケージの設定 | 作成した Azure SQL Database への **ADO.NET データベース接続文字列**を入力します。<br/><br/>接続文字列をコピーしても、パスワードは接続文字列に追加されません。接続文字列にパスワードを必ず入力してください。
App Service プラン | 支払プランの一覧を表示します。プランは必要なリソースの増減に応じて変更することができます。
価格レベル | Azure サブスクリプション内での価格のカテゴリの一覧を表示する読み取り専用プロパティです。 
リソース グループ | 新しいリソース グループを作成するか、既存のグループを使用します。Logic Apps、Web Apps、Mobile Apps のすべての API Apps とコネクタが同じリソース グループに属している必要があります。<br/><br/>[リソース グループの使用](../resource-group-overview.md)に関するページに、このプロパティについての説明があります。 
[サブスクリプション] | 現在のサブスクリプションを一覧表示する読み取り専用のプロパティです。
場所 | Azure サービスをホストする地理的な場所です。 
スタート画面への追加 | B2B API アプリをスタート画面 (ホーム ページ) に追加する場合に選択します。

4. **[作成]** を選択します。

TPM API アプリ (TPM インスタンス) が作成されたら、AS2 コネクタ、X12 API アプリ、または EDIFACT API Apps を作成できます。


#### AS2 コネクタの作成

1. Azure ポータルのスタート画面 (ホーム ページ) で、**[Marketplace]** を選択します。**[API Apps]** に、既存のすべての API Apps とコネクタが一覧表示されます。**[検索]** で特定の B2B API Apps を見つけることもできます。
2. **[AS2 コネクタ]** を選択します。新しいブレードで **[作成]** を選択します。 
3. 次のプロパティを入力します。 

	プロパティ | 説明
--- | ---
名前 | AS2 コネクタの任意の名前を入力します。たとえば、*AS2Connector* という名前を付けることができます。
パッケージの設定 | TPM インスタンス名など、その API アプリに固有の設定を入力します。<br/><br/>特定のプロパティについては、このトピックの [AS2 パッケージ設定の追加](#AddAS2Conn)に関する説明を参照してください。 
App Service プラン | 支払プランの一覧を表示します。プランは必要なリソースの増減に応じて変更することができます。
価格レベル | Azure サブスクリプション内での価格のカテゴリの一覧を表示する読み取り専用プロパティです。 
リソース グループ | 新しいリソース グループを作成するか、既存のグループを使用します。[リソース グループの使用](../resource-group-overview.md)に関するページに、このプロパティについての説明があります。 
[サブスクリプション] | 現在のサブスクリプションを一覧表示する読み取り専用のプロパティです。
場所 | Azure サービスをホストする地理的な場所です。 
スタート画面への追加 | B2B API アプリをスタート画面 (ホーム ページ) に追加する場合に選択します。

	**<a name="AddAS2Conn"></a>AS2 コネクタのパッケージ設定**

	プロパティ | 説明
--- | --- 
データベース接続文字列 | 作成した Azure SQL Database への ADO.NET 接続文字列を入力します。接続文字列をコピーしても、パスワードは接続文字列に追加されません。接続文字列を貼り付ける前に、接続文字列にパスワードを必ず入力してください。
受信メッセージのアーカイブを有効にする | 省略可能。このプロパティを有効にすると、パートナーから受け取った受信 AS2 メッセージのメッセージ プロパティが格納されます。 
Azure BLOB Storage の接続文字列 | 作成した Azure BLOB Storage コンテナーへの接続文字列を入力します。アーカイブが有効な場合は、エンコードまたはデコードされたメッセージがこの Storage コンテナーに格納されます。
TPM インスタンス名 | 前に作成した **BizTalk 取引先管理** API アプリの名前を入力します。AS2 コネクタを作成すると、このコネクタではこの特定の TPM インスタンス内の AS2 契約のみを実行します。

4. **[作成]** を選択します。


#### X12 API アプリまたは EDIFACT API Apps の作成

1. Azure ポータルのスタート画面 (ホーム ページ) で、**[Marketplace]** を選択します。**[API Apps]** に、既存のすべての API Apps とコネクタが一覧表示されます。**[検索]** で特定の B2B API Apps を見つけることもできます。
2. **[BizTalk X12]** または **[BizTalk EDIFACT]** を選択します。新しいブレードで **[作成]** を選択します。 
3. 次のプロパティを入力します。 

	プロパティ | 説明
--- | ---
名前 | B2B API アプリの任意の名前を入力します。たとえば、*EDI850APIApp* という名前を付けることができます。
パッケージの設定 | TPM インスタンス名など、その API アプリに固有の設定を入力します。<br/><br/>特定のプロパティについては、このトピックの [X12 または EDIFACT のパッケージ設定](#AddX12)に関する説明を参照してください。 
App Service プラン | 支払プランの一覧を表示します。プランは必要なリソースの増減に応じて変更することができます。
価格レベル | Azure サブスクリプション内での価格のカテゴリの一覧を表示する読み取り専用プロパティです。 
リソース グループ | 新しいリソース グループを作成するか、既存のグループを使用します。[リソース グループの使用](../resource-group-overview.md)に関するページに、このプロパティについての説明があります。 
[サブスクリプション] | 現在のサブスクリプションを一覧表示する読み取り専用のプロパティです。
場所 | Azure サービスをホストする地理的な場所です。 
スタート画面への追加 | B2B API アプリをスタート画面 (ホーム ページ) に追加する場合に選択します。

	**<a name="AddX12"></a>X12 API Apps および EDIFACT API Apps のパッケージ設定**

	プロパティ | 説明
--- | --- 
データベース接続文字列 | 作成した Azure SQL Database への ADO.NET 接続文字列を入力します。接続文字列をコピーしても、パスワードは接続文字列に追加されません。接続文字列を貼り付ける前に、接続文字列にパスワードを必ず入力してください。
Service Bus 名前空間 | 作成した Service Bus 名前空間を入力します。バッチ処理が有効な場合にのみ必要です。 
Service Bus 名前空間の共有アクセス キー名 | 作成した Service Bus 名前空間のアクセス キーを入力します。バッチ処理が有効な場合にのみ必要です。 
Service Bus 名前空間共有アクセス キー値 | 作成した Service Bus 名前空間のアクセス キー値を入力します。バッチ処理が有効な場合にのみ必要です。 
TPM インスタンス名 | 前に作成した **BizTalk 取引先管理** API アプリの名前を入力します。X12 API Apps または EDIFACT API Apps を作成すると、この API アプリでは、この特定の TPM インスタンス内の X12/EDFIACT 契約のみを実行します。

4. **[作成]** を選択します。


## パートナー、契約、証明書、スキーマの追加 
Azure ポータルで、TPM API アプリを開きます。**[コンポーネント]** セクションで、パートナー、契約、証明書、スキーマを追加します。

AS2 コネクタ、X12 API Apps、および EDIFACT API Apps にも契約を追加することができます。


## API Apps の監視
Azure ポータルで、TPM API アプリを開きます。**[操作]** セクションでは、さまざまな管理操作を表示できます。たとえば、次のようなことができます。

- 情報イベントやエラー イベントの表示
- ワーカー プロセス (w3wp) のメモリ使用量とスレッド数の表示
- アプリケーションと Web サーバーのログの表示

詳細については、「[ロジック アプリを監視する](app-service-logic-monitor-your-logic-apps.md)」を参照してください。


## アプリケーションへの API Apps の追加 
Microsoft Azure App Service では、これらの B2B API Apps を使用できるさまざまな種類のアプリケーションを公開しています。新しい B2B API Apps を作成することや、既存の B2B API Apps を Logic Apps、Mobile Apps、または Web Apps に追加することができます。

アプリ内でギャラリーから B2B API Apps を選択するだけで、選択した B2B API Apps が自動的にアプリに追加されます。

> [AZURE.IMPORTANT] 以前に作成したコネクタおよび API Apps を追加するには、同じリソース グループに Logic Apps、Mobile Apps、または Web Apps を作成します。

次の手順で、B2B API Apps を Logic Apps、Mobile Apps、または Web Apps に追加します。

1. Azure ポータルのスタート画面 (ホーム ページ) で、**Marketplace** に移動し、Logic Apps、Mobile Apps、または Web Apps を検索します。 

	新しいアプリを作成する場合は、Logic Apps、Mobile Apps、または Web Apps を検索します。アプリを選択し、新しいブレードで **[作成]** を選択します。[ロジック アプリの作成](app-service-logic-create-a-logic-app.md)に関するページに、この手順が記載されています。

2. アプリを開き、**[トリガーとアクション]** を選択します。

3. **ギャラリー**から B2B API アプリを選択します。選択した B2B API アプリがアプリに自動的に追加されます。新しい B2B API アプリを作成することもできます。

	> [AZURE.IMPORTANT] AS2 コネクタ、X12 API Apps、EDIFACT API Apps には TPM インスタンスが必要です。そのため、新しい B2B API Apps を作成する場合は、最初に TPM API アプリを作成し、次に AS2 コネクタ、X12 API アプリ、または EDIFACT API アプリを作成します。

4. **[OK]** を選択して変更を保存します。

>[AZURE.NOTE] Azure アカウントにはサインアップせずに Azure Logic Apps を体験することもできます。「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」にアクセスしてください。App Service で短時間有効な簡易版のロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 他の B2B リソース

[B2B プロセスの作成](app-service-logic-create-a-b2b-process.md)<br/> [取引先契約の作成](app-service-logic-create-a-trading-partner-agreement.md)<br/> [コネクタと BizTalk API Apps とは](app-service-logic-what-are-biztalk-api-apps.md)


## Logic Apps と Web Apps に関するガイド
[Logic Apps とは](app-service-logic-what-are-logic-apps.md)<br/> [Azure App Service の Websites と Web Apps](../app-service-web/app-service-web-overview.md)


## その他のコネクタ

[コネクタと API Apps の一覧](app-service-logic-connectors-list.md)<br/><br/> [コネクタと BizTalk API Apps とは](app-service-logic-what-are-biztalk-api-apps.md)

<!---HONumber=AcomDC_0224_2016-->