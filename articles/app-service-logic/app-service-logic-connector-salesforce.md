<properties 
   pageTitle="Salesforce コネクタ" 
   description="Salesforce コネクタの使用方法" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="sutalasi" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


#ロジック アプリでの Salesforce コネクタの使用#

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。Salesforce コネクタでは、取引先企業や潜在顧客などのさまざまなエンティティを作成および変更できます。Salesforce に関連する一般的な統合シナリオを次に示します。

- Salesforce と ERP システム (SAP、QuickBooks など) との間のアカウントの同期	

- Salesforce から ERP システムへの受注から入金までのフロー


コネクタ パッケージの設定の一部として、ユーザーは、コネクタで管理できるエンティティを指定できます。アクション、入力パラメーター、および出力パラメーターは、動的に設定されます。Salesforce コネクタで使用できるさまざまなアクションを次に示します。
 
- [エンティティの作成] - アカウント、事例、カスタム オブジェクトなどの新しい Salesforce エンティティを作成するには、この操作を使用します。

- [エンティティの更新] - Salesforce の既存のエンティティを更新するには、このアクションを使用します。

- [エンティティの Upsert] - 既存の Salesforce エンティティを更新するか、または既存の Salesforce エンティティが存在しない場合にエンティティを作成するには、このアクションを使用します。

- [エンティティの削除] - 既存の Salesforce エンティティを削除するには、このアクションを使用します。

- [クエリの実行] - Salesforce オブジェクト クエリ言語 (SOQL) で記述された SELECT クエリを実行するには、このアクションを使用します。


##SalesForce コネクタの API アプリの作成##
1.	Azure ポータルの右下にある [+ 新規] オプションを使用して Azure Marketplace を開きます。
2.	[Web + モバイル]、[API アプリ] の順に移動し、"SalesForce" を検索します。
3.	リソース グループにホスティング プランの詳細を入力し、API アプリの名前を選択して、SalesForce コネクタを構成します。

	![][15]
4. [パッケージの設定] で、読み取りと書き込みを行う SalesForce エンティティを構成します。

これで、SalesForce コネクタの API アプリを作成できます。


##ロジック アプリの作成##
Salesforce にアカウントを作成し、このアカウントの請求先住所の詳細を更新する単純なロジック アプリを作成してみましょう。

1.	Azure ポータルにログインし、[新規作成]、[Web とモバイル]、[ロジック アプリ] の順にクリックします。

	![][1]

2.	[ロジック アプリの作成] ページで、名前、App Service プラン、場所などの必要な詳細を入力します。

	![][2]

3.	[トリガーとアクション] をクリックすると、ロジック アプリ エディターの画面が表示されます。

	![][3]

4.	[このロジックを手動で実行] を選択します。これは、このロジック アプリを手動でのみ起動できることを意味します。

    ![][4]

5.	ギャラリーで [このリソース グループの API アプリ] を展開し、使用可能なすべての API アプリを表示します。ギャラリーで [Salesforce] を選択します。Salesforce コネクタがフローに追加されます。


	![][5]

8.	SalesForce アカウントにアクセスできるようにロジック アプリを承認するには、[承認] をクリックして Salesforce のログイン資格情報を入力します。

	![][6]

9.	Salesforce のログイン ページにリダイレクトされます。認証のために Salesforce の資格情報を入力します。

	![][7]

	![][8]

10.	承認が完了すると、すべてのアクションが表示されます。

	![][9]

11.	[アカウントの作成] アクションを選択します。入力パラメーターが表示されます。

	![][10]

12.	アカウント名を入力し、[✓] をクリックします。

	![][11]

13.	ギャラリーの [最近使用した項目] セクションで [Salesforce コネクタ] を選択します。新しい Salesforce アクションが追加されます。

14.	アクションの一覧の [アカウントの更新] を選択します。[アカウントの更新] の入力パラメーターが表示されます。

	![][12]

15.	[レコード ID] の横にある [+] をクリックし、[アカウントの作成] アクションの出力の ID 値を選択します。

	![][13]

16.	[番地 (請求先)]、[市区町村 (請求先)]、[都道府県 (請求先)]、[郵便番号 (請求先)] に値を入力し、[✓] をクリックします。

	![][14]

17. ロジック アプリ エディターの画面で [OK] をクリックし、[作成] をクリックします。作成が完了するまで約 30 秒かかります。

18. 新しく作成されたロジック アプリを参照し、[実行] をクリックして実行します。

19. Salesforce アカウントに "Contoso" という名前の新しいアカウントが作成されることを確認します。

<!--Image references-->
[1]: ./media/app-service-logic-connector-salesforce/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-salesforce/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-salesforce/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-salesforce/4_Manual_Logic_App.png
[5]: ./media/app-service-logic-connector-salesforce/5_Select_Salesforce_Gallery.png
[6]: ./media/app-service-logic-connector-salesforce/6_Salesforce_Authorize.png
[7]: ./media/app-service-logic-connector-salesforce/7_Salesforce_Login.png
[8]: ./media/app-service-logic-connector-salesforce/8_Salesforce_User_Consent.png
[9]: ./media/app-service-logic-connector-salesforce/9_Salesforce_Actions.png
[10]: ./media/app-service-logic-connector-salesforce/10_Salesforce_Create_Account.png
[11]: ./media/app-service-logic-connector-salesforce/11_Create_Account_OK.png
[12]: ./media/app-service-logic-connector-salesforce/12_Salesforce_Update_Account.png
[13]: ./media/app-service-logic-connector-salesforce/13_Record_ID_from_Create.png
[14]: ./media/app-service-logic-connector-salesforce/14_Update_Account_Address.png
[15]: ./media/app-service-logic-connector-salesforce/15_Create_new_salesforce_connector.png


 

<!---HONumber=62-->