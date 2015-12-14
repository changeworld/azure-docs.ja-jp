<properties
   pageTitle="Logic Apps での Oracle Database コネクタの使用 | Microsoft Azure App Service"
   description="Oracle Database コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/30/2015"
   ms.author="sameerch"/>


# Oracle Database コネクタの使用開始とロジック アプリへの追加
オンプレミスの Oracle Database サーバーに接続して、情報やデータの作成と変更を行います。コネクタを Logic Apps で使用して、"ワークフロー" の一部としてデータを取得、処理、またはプッシュできます。Oracle コネクタをワークフローで使用すると、さまざまなシナリオを実現できます。たとえば、次のようなことができます。

- Web またはモバイル アプリケーションを使用して、Oracle Database に存在するデータの一部を公開する。
- ストレージ用の Oracle Database テーブルにデータを挿入する。たとえば、従業員レコードの入力や販売注文の更新などを実行できます。
- データを Oracle から抽出してビジネス プロセスで使用する。たとえば、顧客レコードを取得し、SalesForce にそれらの顧客レコードを書き込むことができます。


## トリガーとアクション
*トリガー*とは、発生するイベントを指します。たとえば、注文が更新された、新しい顧客が追加された、といったイベントがあります。*アクション*は、トリガーの結果です。たとえば、注文が更新されたときに、営業担当者にアラートを送信します。または、新しい顧客が追加されたときに、ウェルカム メールを新しい顧客に送信します。

Oracle Database コネクタは、ロジック アプリでトリガーまたはアクションとして使用でき、JSON 形式と XML 形式のデータをサポートします。パッケージ設定に含まれるすべてのテーブルに対して (詳細はこのトピックで後述します)、JSON アクションと XML アクションのセットがあります。XML のトリガーまたはアクションを使用している場合は、[変換 API App](app-service-logic-transform-xml-documents.md)を使用して、データを別の XML データ形式に変換できます。

Oracle Database コネクタでは、次のトリガーとアクションを使用できます。

トリガー | アクション
--- | ---
データのポーリング | <ul><li>テーブルに挿入</li><li>テーブルを更新</li><li>テーブルから選択</li><li>テーブルから削除</li><li>ストアド プロシージャを呼び出す</li>


## Oracle Database コネクタを作成する

コネクタは、ロジック アプリ内で作成することも、Azure Marketplace から直接作成することもできます。Marketplace からコネクタを作成するには、次の操作を実行します。

1. Azure のスタート画面で、**[Marketplace]** を選択します。
2. **[API Apps]** を選択し、"Oracle Database コネクタ" を検索します。
3. 名前、App Service プラン、その他のプロパティを入力します。
4. 次のパッケージの設定を入力します。

	名前 | 必須 | 説明
--- | --- | ---
データ ソース | はい | Oracle クライアントがインストールされているコンピューターの tnsnames.ora ファイルで指定されているデータ ソース (ネット サービス) の名前です。データ ソース名と tnsnames.ora の詳細については、「[Oracle クライアントの構成](http://msdn.microsoft.com/library/dd787872.aspx)」を参照してください。
ユーザー名 | はい | Oracle サーバーに接続するユーザー名を入力します。
パスワード | あり | ユーザー名のパスワードを入力します。
Service Bus の接続文字列 | はい | オンプレミスに接続する場合は、Service Bus Relay の接続文字列を入力します。<br/><br/>[ハイブリッド接続マネージャーの使用](app-service-logic-hybrid-connection-manager.md)<br/>[Service Bus 料金](http://azure.microsoft.com/pricing/details/service-bus/)
テーブル | いいえ | コネクタで変更できるデータベース内のテーブルを入力します。たとえば、「*OrdersTable,EmployeeTable*」と入力します。
ストアド プロシージャ | いいえ | コネクタで呼び出すことができるデータベースのストアド プロシージャを入力します。たとえば、「*IsEmployeeEligible,CalculateOrderDiscount*」と入力します。
関数 | いいえ | コネクタで呼び出すことができるデータベースの関数を入力します。たとえば、「*IsEmployeeEligible,CalculateOrderDiscount*」と入力します。
パッケージ エンティティ | いいえ | コネクタで呼び出すことができるデータベースのパッケージを入力します。たとえば、「*PackageOrderProcessing.CompleteOrder,PackageOrderProcessing.GenerateBill*」と入力します。
データを使用できるステートメント | いいえ | 任意のデータをポーリングに使用できるかどうかを判断するステートメントを入力します。たとえば、「*SELECT * from table\_name*」と入力します。
ポーリングの種類 | いいえ | ポーリングの種類を入力します。入力可能な値は、"Select"、"Procedure"、"Function"、"Package" です。
ポーリング ステートメント | いいえ | Oracle Server Database をポーリングするステートメントを入力します。たとえば、「*SELECT * from table\_name*」と入力します。
ポスト ポーリング ステートメント | いいえ | ポーリング後に実行するステートメントを入力します。たとえば、「*DELETE * from table\_name*」と入力します。

5. 完了すると、パッケージの設定は次のようになります。<br/> ![][1]


## コネクタをトリガーとして使用する
Oracle テーブルからデータをポーリングし、そのデータを別のテーブルに追加してデータを更新する、簡単なロジック アプリを見てみましょう。

### トリガーを追加する
1. ロジック アプリを作成または編集するときに、トリガーとして作成した Oracle コネクタを選択します。使用できるトリガーが一覧表示されます: **[データのポーリング (JSON)]** および **[データのポーリング (XML)]**) <br/> ![][5]

2. **[データのポーリング (JSON)]** トリガーを選択し、頻度を入力して、[✓] をクリックします: <br/> ![][6]

3. これで、トリガーは、ロジック アプリで構成されたものとして表示されます。トリガーの出力が表示されます。これは、後続のアクションの入力として使用できます。<br/> ![][7]

## コネクタをアクションとして使用する
Oracle テーブルからデータをポーリングし、そのデータを別のテーブルに追加してデータを更新する、簡単なロジック アプリを使用します。

Oracle コネクタをアクションとして使用するには、Oracle コネクタの作成時に入力したテーブル名やストアド プロシージャ名を入力します。

1. ギャラリーからアクションと同じ Oracle コネクタを選択します。いずれかの挿入アクションを選択します (*Insert Into TempEmployeeDetails (JSON)* など)。<br/> ![][8]

2. 挿入されるレコードの入力値を指定し、[✓] をクリックします: <br/> ![][9]

3. ギャラリーから、作成済みの同じ Oracle コネクタを選択します。アクションとして、同じテーブルに対する更新アクションを選択します (*例: Update TempEmployeeDetails*)。<br/> ![][11]

4. 更新アクション用の入力値を指定し、[✓] をクリックします: <br/> ![][12]

ポーリングされるテーブルに新しいレコードを追加すると、ロジック アプリをテストできます。

## ハイブリッド構成

> [AZURE.NOTE]この手順は、ファイアウォールの背後にあるオンプレミスの Oracle を使用している場合のみ必要です。

App Service では、 ハイブリッド構成マネージャーを使用して、オンプレミス システムに安全に接続します。コネクタでオンプレミスの Oracle を使用する場合は、ハイブリッド接続マネージャーが必要です。

「[ハイブリッド接続マネージャーの使用](app-service-logic-hybrid-connection-manager.md)」を参照してください。

## コネクタでできること
コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure Logic Apps の使用を開始する場合は、「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」を参照してください。App Service で有効期間の短いスターター ロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

「[Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。


<!--Image references-->
[1]: ./media/app-service-logic-connector-oracle/Create.png
[5]: ./media/app-service-logic-connector-oracle/LogicApp1.png
[6]: ./media/app-service-logic-connector-oracle/LogicApp2.png
[7]: ./media/app-service-logic-connector-oracle/LogicApp3.png
[8]: ./media/app-service-logic-connector-oracle/LogicApp4.png
[9]: ./media/app-service-logic-connector-oracle/LogicApp5.png
[10]: ./media/app-service-logic-connector-oracle/LogicApp6.png
[11]: ./media/app-service-logic-connector-oracle/LogicApp7.png
[12]: ./media/app-service-logic-connector-oracle/LogicApp8.png

<!---HONumber=AcomDC_1203_2015-->