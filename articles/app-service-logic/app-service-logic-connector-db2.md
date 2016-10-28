<properties
   pageTitle="Microsoft Azure App Service での DB2 コネクタの使用 | Microsoft Azure"
   description="DB2 コネクタでロジック アプリのトリガーとアクションを使用する方法"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="plarsen"/>

# DB2 コネクタ
>[AZURE.NOTE] 本記事は、ロジック アプリの 2014-12-01-preview スキーマ バージョンを対象としています。

Microsoft Connector for DB2 は、Azure App Service を介してアプリケーションを IBM DB2 データベースに格納されているリソースに接続するための API アプリです。コネクタには、Azure Service Bus Relay を使用したオンプレミスの DB2 サーバーへの Azure ハイブリッド接続を含む、TCP/IP ネットワーク接続経由でリモート DB2 サーバー コンピューターに接続するための Microsoft クライアントが含まれています。コネクタでは、次のデータベース操作がサポートされています。

- SELECT を使用して行を読み取る
- SELECT COUNT の後に SELECT を使用して、行をポーリングして読み取る
- INSERT を使用して 1 行または複数 (バルク) 行を追加する
- UPDATE を使用して 1 行または複数 (バルク) 行を変更する
- DELETE を使用して 1 行または複数 (バルク) 行を削除する
- SELECT CURSOR の後に UPDATE WHERE CURRENT OF CURSOR を使用して、行を読み取って変更する
- SELECT CURSOR の後に UPDATE WHERE CURRENT OF CURSOR を使用して、行を読み取って削除する
- CALL を使用して、入力パラメーター、出力パラメーター、戻り値、結果セットを指定したプロシージャを実行する
- SELECT、INSERT、UPDATE、DELETE を使用したカスタム コマンドおよび複合操作

## トリガーとアクション
コネクタでは、次に示す、ロジック アプリのトリガーとアクションをサポートしています。

トリガー | アクション
--- | ---
<ul><li>データのポーリング</li></ul> | <ul><li>一括挿入</li><li>挿入</li><li>一括更新</li><li>更新</li><li>呼び出し</li><li>一括削除</li><li>削除</li><li>選択</li><li>条件付き更新</li><li>EntitySet への投稿</li><li>条件付き削除</li><li>単一エンティティの選択</li><li>削除</li><li>EntitySet に対する Upsert</li><li>カスタム コマンド</li><li>複合操作</li></ul>


## DB2 コネクタの作成
コネクタは、次の例に示すように、ロジック アプリ内で定義することも、Azure Marketplace から定義することもできます。

1. Azure のスタート画面で、**[Marketplace]** を選択します。
2. **[すべて]** ブレードで、**[すべて検索]** ボックスに「**db2**」と入力し、Enter キーを押します。
3. すべて検索の結果ウィンドウで、**[DB2 コネクタ]** を選択します。
4. DB2 コネクタの説明ブレードで、**[作成]** を選択します。
5. DB2 コネクタのパッケージ ブレードで、名前 (例:"Db2ConnectorNewOrders")、App Service プラン、その他のプロパティを入力します。
6. **[パッケージの設定]** を選択し、次のパッケージ設定を入力します。

	名前 | 必須 | 説明
--- | --- | ---
ConnectionString | あり | DB2 クライアントの接続文字列 (例: "Network Address=servername;Network Port=50000;User ID=username;Password=password;Initial Catalog=SAMPLE;Package Collection=NWIND;Default Schema=NWIND")。
テーブル | あり | OData 操作や、例を含む swagger ドキュメントの生成に必要なテーブル、ビュー、エイリアスの名前のコンマ区切りのリスト (例:"*NEWORDERS*")。
プロシージャ | あり | プロシージャと関数の名前のコンマ区切りのリスト (例:"SPORDERID")。
OnPremise | いいえ | Azure Service Bus Relay を使用してオンプレミスにデプロイします。
ServiceBusConnectionString | いいえ | Azure Service Bus Relay の接続文字列。
PollToCheckData | いいえ | ロジック アプリのトリガーで使用する SELECT COUNT ステートメント (例:"SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL")。
PollToReadData | いいえ | ロジック アプリのトリガーで使用する SELECT ステートメント (例:"SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE")。
PollToAlterData | いいえ | ロジック アプリのトリガーで使用する UPDATE または DELETE ステートメント (例:"UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;")。

7. **[OK]** を選択し、**[作成]** を選択します。
8. 完了すると、パッケージの設定は次のようになります。![][1]


## データを追加する DB2 コネクタ アクションを使用したロジック アプリ ##
"API の挿入" または "エンティティへの投稿" OData 操作を使用して DB2 テーブルにデータを追加するようにロジック アプリ アクションを定義できます。たとえば、新しい顧客注文レコードを挿入するには、ID 列で定義されたテーブルに対する SQL INSERT ステートメントを処理して、ID 値または処理された行をロジック アプリに返します (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?)))。

> [AZURE.TIP] DB2 接続 "*EntitySet への投稿*" では ID 列の値が返され、"*API の挿入*" では処理された行が返されます。

1. Azure のスタート画面で、**[+]** (プラス記号)、**[Web + モバイル]**、**[ロジック アプリ]** の順に選択します。
2. 名前 (例:"NewOrdersDb2")、App Service プラン、その他のプロパティを入力し、**[作成]** を選択します。
3. Azure のスタート画面で、先ほど作成したロジック アプリ、**[設定]**、**[トリガーとアクション]** の順に選択します。
4. [トリガーとアクション] ブレードで、[ロジック アプリのテンプレート] 内の **[最初から作成する]** を選択します。
5. [API Apps] パネルで、**[繰り返し]** を選択し、頻度と間隔を設定して、**チェックマーク**を選択します。
6. [API Apps] パネルで、**[DB2 コネクタ]** を選択し、操作の一覧を展開して **[NEWORDER に挿入する]** を選択します。
7. パラメーターの一覧を展開して、次の値を入力します。

	名前 | 値
--- | --- 
CUSTID | 10042
SHIPNAME | Lazy K Kountry Store 
SHIPADDR | 12 Orchestra Terrace
SHIPCITY | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. **チェックマーク**を選択してアクションの設定を保存し、**[保存]** を選択します。
9. 設定は次のようになります。![][3]

10. **[操作]** の下の **[すべての実行]** の一覧で、先頭の項目 (最新の実行) を選択します。
11. **[ロジック アプリの実行]** ブレードで、**[アクション]** の項目 **[db2connectorneworders]** を選択します。
12. **[ロジック アプリのアクション]** ブレードで、**[入力リンク]** を選択します。DB2 コネクタは、入力値を使用して、パラメーター化された INSERT ステートメントを処理します。
13. **[ロジック アプリのアクション]** ブレードで、**[出力リンク]** を選択します。入力は次のようになります。![][4]

#### 知っておくべきこと

- ロジック アプリのアクション名が形成される際、DB2 テーブル名は切り詰められます。たとえば、操作 "**NEWORDERS に挿入する**" は、"**NEWORDER に挿入する**" に切り詰められます。
- ロジック アプリの **[トリガーとアクション]** を保存すると、ロジック アプリによって操作が処理されます。ロジック アプリが操作を処理するまでに数秒 (たとえば、3 ～ 5 秒) の遅延が発生する場合があります。必要に応じて、**[今すぐ実行]** をクリックして操作を処理することができます。
- DB2 コネクタは、属性を使用して EntitySet メンバーを定義します。これには、メンバーが、既定の列と生成される列 (ID など) のどちらで DB2 列に対応しているかなどが含まれます。ロジック アプリでは、値が必要な DB2 列を示すために、EntitySet メンバー ID 名の横に赤いアスタリスクが表示されます。ORDID メンバーの値は入力しないでください。これは、DB2 ID 列に対応しています。入力できるのは、その他のオプションのメンバー (ITEMS、ORDDATE、REQDATE、SHIPID、FREIGHT、SHIPCTRY) の値です。これらは、既定値が設定された DB2 列に対応しています。
- DB2 コネクタは、"EntitySet への投稿" に対する応答をロジック アプリに返します。これには、準備された SQL INSERT ステートメントの DRDA SQLDARD (SQL Data Area Reply Data) から派生した ID 列の値が含まれます。DB2 サーバーは、既定値が設定された列に対して、挿入された値を返しません。


## バルク データを追加する DB2 コネクタ アクションを使用したロジック アプリ ##
API の一括挿入操作を使用して DB2 テーブルにデータを追加するようにロジック アプリ アクションを定義できます。たとえば、2 つの新しい顧客注文レコードを挿入するには、ID 列で定義されたテーブルに対して行の値の配列を使用して SQL INSERT ステートメントを処理し、処理された行をロジック アプリに返します (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?)))。

1. Azure のスタート画面で、**[+]** (プラス記号)、**[Web + モバイル]**、**[ロジック アプリ]** の順に選択します。
2. 名前 (例:"NewOrdersBulkDb2")、App Service プラン、その他のプロパティを入力し、**[作成]** を選択します。
3. Azure のスタート画面で、先ほど作成したロジック アプリ、**[設定]**、**[トリガーとアクション]** の順に選択します。
4. [トリガーとアクション] ブレードで、[ロジック アプリのテンプレート] 内の **[最初から作成する]** を選択します。
5. [API Apps] パネルで、**[繰り返し]** を選択し、頻度と間隔を設定して、**チェックマーク**を選択します。
6. [API Apps] パネルで、**[DB2 コネクタ]** を選択し、操作の一覧を展開して **[NEW に一括挿入する]** を選択します。
7. **[行]** の値を配列として入力します。たとえば、次の内容をコピーして貼り付けます。

	```
    [{"CUSTID":10081,"SHIPNAME":"Trail's Head Gourmet Provisioners","SHIPADDR":"722 DaVinci Blvd.","SHIPCITY":"Kirkland","SHIPREG":"WA","SHIPZIP":"98034"},{"CUSTID":10088,"SHIPNAME":"White Clover Markets","SHIPADDR":"305 14th Ave. S. Suite 3B","SHIPCITY":"Seattle","SHIPREG":"WA","SHIPZIP":"98128","SHIPCTRY":"USA"}]
	```

8. **チェックマーク**を選択してアクションの設定を保存し、**[保存]** を選択します。設定は次のようになります。![][6]

9. **[操作]** の下の **[すべての実行]** の一覧で、先頭の項目 (最新の実行) をクリックします。
10. **[ロジック アプリの実行]** ブレードで、**[アクション]** 項目をクリックします。
11. **[ロジック アプリのアクション]** ブレードで、**[入力リンク]** をクリックします。出力は次のようになります。[][7]
12. **[ロジック アプリのアクション]** ブレードで、**[出力リンク]** をクリックします。出力は次のようになります。![][8]

#### 知っておくべきこと

- ロジック アプリのアクション名が形成される際、DB2 テーブル名は切り詰められます。たとえば、操作 "**NEWORDERS に一括挿入する**" は、"**NEW に一括挿入する**" に切り詰められます。
- ID 列 (例: ORDID)、null 許容列 (例: SHIPDATE)、および既定値が設定された列 (例: ORDDATE、REQDATE、SHIPID、FREIGHT、SHIPCTRY) を省略すると、DB2 データベースで値が生成されます。
- "today" と "tomorrow" を指定すると、DB2 コネクタにより、"CURRENT DATE" 関数と "CURRENT DATE + 1 DAY" 関数 (REQDATE など) が生成されます。


## データの読み取り、変更、削除を行う DB2 コネクタ トリガーを使用したロジック アプリ ##
API のデータのポーリング複合操作を使用して DB2 テーブルのデータをポーリングして読み取るようにロジック アプリのトリガーを定義できます。たとえば、1 つ以上の新しい顧客注文レコードを読み取り、そのレコードをロジック アプリに返すことができます。DB2 接続のパッケージ/アプリの設定は次のようになります。

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | <値の指定なし>


また、API のデータのポーリング複合操作を使用して DB2 テーブルのデータのポーリング、読み取り、変更を行うようにロジック アプリのトリガーを定義することもできます。たとえば、1 つ以上の新しい顧客注文レコードを読み取り、行の値を更新して、(更新前に) 選択されたレコードをロジック アプリに返すことができます。DB2 接続のパッケージ/アプリの設定は次のようになります。

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;


さらに、API のデータのポーリング複合操作を使用して DB2 テーブルのデータのポーリング、読み取り、削除を行うようにロジック アプリのトリガーを定義できます。たとえば、1 つ以上の新しい顧客注文レコードを読み取り、行を削除して、(削除前に) 選択されたレコードをロジック アプリに返すことができます。DB2 接続のパッケージ/アプリの設定は次のようになります。

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | DELETE NEWORDERS WHERE CURRENT OF &lt;CURSOR&gt;

この例では、ロジック アプリは、DB2 テーブル内のデータをポーリングして読み取り、更新した後で、もう一度データを読み込みます。

1. Azure のスタート画面で、**[+]** (プラス記号)、**[Web + モバイル]**、**[ロジック アプリ]** の順に選択します。
2. 名前 (例:"ShipOrdersDb2")、App Service プラン、その他のプロパティを入力し、**[作成]** を選択します。
3. Azure のスタート画面で、先ほど作成したロジック アプリ、**[設定]**、**[トリガーとアクション]** の順に選択します。
4. [トリガーとアクション] ブレードで、[ロジック アプリのテンプレート] 内の **[最初から作成する]** を選択します。
5. [API Apps] パネルで、**[DB2 コネクタ]** を選択し、頻度と間隔を設定して、**チェックマーク**を選択します。
6. [API Apps] パネルで、**[DB2 コネクタ]** を選択し、操作の一覧を展開して **[NEWORDERS から選択する]** を選択します。
7. **チェックマーク**を選択してアクションの設定を保存し、**[保存]** を選択します。設定は次のようになります。![][10]
8. クリックして **[トリガーとアクション]** ブレードを閉じてから、クリックして **[設定]** ブレードを閉じます。
9. **[操作]** の下の **[すべての実行]** の一覧で、先頭の項目 (最新の実行) をクリックします。
10. **[ロジック アプリの実行]** ブレードで、**[アクション]** 項目をクリックします。
11. **[ロジック アプリのアクション]** ブレードで、**[出力リンク]** をクリックします。出力は次のようになります。![][11]


## データを削除する DB2 コネクタ アクションを使用したロジック アプリ ##
"API の削除" または "エンティティへの投稿" OData 操作を使用して DB2 テーブルからデータを削除するようにロジック アプリ アクションを定義できます。たとえば、新しい顧客注文レコードを挿入するには、ID 列で定義されたテーブルに対する SQL INSERT ステートメントを処理して、ID 値または処理された行をロジック アプリに返します (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?)))。

## DB2 コネクタを使用してデータを削除するロジック アプリの作成 ##
Azure Marketplace 内から新しいロジック アプリを作成した後、顧客注文を削除するアクションとして DB2 コネクタを使用できます。たとえば、DB2 コネクタの条件付き削除操作を使用して SQL の DELETE ステートメントを処理することができます (DELETE FROM NEWORDERS WHERE ORDID >= 10000)。

1. Azure の**スタート**画面のハブ メニューで、**[+]** (プラス記号)、**[Web + モバイル]**、**[ロジック アプリ]** の順にクリックします。
2. **[ロジック アプリの作成]** ブレードで、**名前**を入力します (たとえば、「**RemoveOrdersDb2**」)。
3. 他の設定 (サービス プラン、リソース グループなど) の値を選択または定義します。
4. 設定は次のようになります。**[作成]** をクリックします。![][12]
5. **[設定]** ブレードで、**[トリガーとアクション]** をクリックします。
6. **[トリガーとアクション]** ブレードの **[ロジック アプリのテンプレート]** の一覧で、**[最初から作成する]** をクリックします。
7. **[トリガーとアクション]** ブレードの **[API Apps]** パネルで、リソース グループ内の **[繰り返し]** をクリックします。
8. ロジック アプリのデザイン画面で、**[繰り返し]** をクリックし、**[頻度]** と **[間隔]** を設定します (たとえば、**[日]** と **1**)。次に、**チェック マーク**をクリックして、繰り返し項目の設定を保存します。
9. **[トリガーとアクション]** ブレードの **[API Apps]** パネルで、リソース グループ内の **[DB2 コネクタ]** をクリックします。
10. ロジック アプリのデザイン画面で、**[DB2 コネクタ]** アクション項目をクリックし、省略記号 (**...**) をクリックして操作の一覧を展開し、**[N から条件付きで削除する]** をクリックします。
11. [DB2 コネクタ] アクション項目で、**[エントリのサブセットを識別する式]** に「**ORDID ge 10000**」と入力します。
12. **チェックマーク**をクリックしてアクションの設定を保存し、**[保存]** をクリックします。設定は次のようになります。![][13]
13. クリックして **[トリガーとアクション]** ブレードを閉じてから、クリックして **[設定]** ブレードを閉じます。
14. **[操作]** の下の **[すべての実行]** の一覧で、先頭の項目 (最新の実行) をクリックします。
15. **[ロジック アプリの実行]** ブレードで、**[アクション]** 項目をクリックします。
16. **[ロジック アプリのアクション]** ブレードで、**[出力リンク]** をクリックします。出力は次のようになります。![][14]

**注:** ロジック アプリ デザイナーによってテーブル名は切り詰められます。たとえば、操作 "**NEWORDERS から条件付きで削除する**" は、"**N から条件付きで削除する**" に切り詰められます。


> [AZURE.TIP] 次の SQL ステートメントを使用して、サンプルのテーブルおよびストアド プロシージャを作成します。

次の DB2 SQL DDL ステートメントを使用して、サンプルの NEWORDERS テーブルを作成できます。
 
 	CREATE TABLE ORDERS (  
 		ORDID INT NOT NULL GENERATED BY DEFAULT AS IDENTITY (START WITH 10000, INCREMENT BY 1) ,  
 		CUSTID INT NOT NULL ,  
 		EMPID INT NOT NULL DEFAULT 10000 ,  
 		ORDDATE DATE NOT NULL DEFAULT CURRENT DATE ,  
 		REQDATE DATE DEFAULT CURRENT DATE ,  
 		SHIPDATE DATE ,  
 		SHIPID INT NOT NULL DEFAULT 10000,  
 		FREIGHT DECIMAL (9,2) NOT NULL DEFAULT 0.00 ,  
 		SHIPNAME CHAR (40) NOT NULL ,  
 		SHIPADDR CHAR (60) NOT NULL ,  
 		SHIPCITY CHAR (20) NOT NULL ,  
 		SHIPREG CHAR (15) NOT NULL ,  
 		SHIPZIP CHAR (10) NOT NULL ,  
 		SHIPCTRY CHAR (15) NOT NULL DEFAULT 'USA' ,  
 		PRIMARY KEY(ORDID)  
 		)  
 
 	CREATE UNIQUE INDEX XORDID ON ORDERS (ORDID ASC)  



次の DB2 DDL ステートメントを使用して、サンプルの SPORDERID ストアド プロシージャを作成できます。
 
 	CREATE OR REPLACE PROCEDURE NWIND.SPORDERID (IN ORDERID VARCHAR(128))  
 		DYNAMIC RESULT SETS 1  
 	P1: BEGIN  
 		DECLARE CURSOR1 CURSOR WITH RETURN FOR  
 			SELECT * FROM NWIND.NEWORDERS  
 				WHERE ORDID = ORDERID;  
 		OPEN CURSOR1;  
 	END P1  
 	') 


## ハイブリッド構成 (省略可能)

> [AZURE.NOTE] この手順は、ファイアウォールの内側にあるオンプレミスの DB2 コネクタを使用する場合にのみ、実行する必要があります。

App Service では、 ハイブリッド構成マネージャーを使用して、オンプレミス システムに安全に接続します。コネクタでオンプレミスの IBM DB2 Server for Windows を使用する場合は、ハイブリッド接続マネージャーが必要です。

「[ハイブリッド接続マネージャーの使用](app-service-logic-hybrid-connection-manager.md)」を参照してください。


## コネクタでできること
コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

REST API を使用した API Apps を作成します。[コネクタと API Apps のリファレンス](http://go.microsoft.com/fwlink/p/?LinkId=529766)に関するページを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。


<!--Image references-->
[1]: ./media/app-service-logic-connector-db2/ApiApp_Db2Connector_Create.png
[2]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Create.png
[3]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_TriggersActions.png
[4]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Outputs.png
[5]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Create.png
[6]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_TriggersActions.png
[7]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Inputs.png
[8]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Outputs.png
[9]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Create.png
[10]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_TriggersActions.png
[11]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Outputs.png
[12]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Create.png
[13]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_TriggersActions.png
[14]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Outputs.png

<!---HONumber=AcomDC_0803_2016-->