<properties
   pageTitle="SugarCRM コネクタ"
   description="SugarCRM コネクタの使用方法"
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
   ms.date="07/02/2015"
   ms.author="sameerch"/>


#Logic App で SugarCRM コネクタを使用する#

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。SugarCRM コネクタでは、取引先企業や潜在顧客、連絡先などのさまざまなエンティティを作成および変更できます。SugarCRM に関連する一般的な統合シナリオを次に示します。

- SugarCRM と ERP システム (SAP など) 間のアカウントの同期

- Marketo と SugarCRM 間のアカウント、連絡先、潜在顧客の同期

- SugarCRM から ERP システムへの受注から入金までのフロー


コネクタ パッケージの設定の一部として、ユーザーは、コネクタで管理できるエンティティを指定できます。アクション、入力パラメーター、および出力パラメーターは、動的に設定されます。

##SugarCRM コネクタのアクション##
次に、SugarCRM コネクタで使用できるさまざまなアクションを示します。

- モジュールの作成 - このアクションを使用して、アカウント、潜在顧客、連絡先などの SugarCRM モジュールの新しいレコードを作成します。

- モジュールの更新 - このアクションを使用して、SugarCRM モジュールの既存のレコードを更新します。

- モジュールの削除 - このアクションを使用して、SugarCRM モジュールの既存のレコードを削除します。

- モジュールの一覧 - このアクションを使用して、フィルター処理されたレコードを一覧します。クエリが指定されていない場合は、すべてのレコードが返されます。

- モジュールの取得 - このアクションを使用して、指定したモジュールから単一レコードを取得します。

- レコード数の取得 - このアクションを使用して、クエリに一致するモジュール内のレコード数を取得します。クエリが指定されていない場合は、モジュール内のレコードの合計数が返されます。

- 重複するモジュールの確認 - このアクションを使用して、モジュール内で重複するレコードを確認します。

*注*: クエリでサポートされている引数の詳細については、SugarCRM REST API のドキュメントをご覧ください。

##SugarCRM コネクタの API App を作成する##
1.	Azure ポータルの右下にある [+ 新規] オプションを使用して Azure Marketplace を開きます。
2.	[Web + モバイル]、[API Apps] の順に移動し、“SugarCRM” を検索します。
3.	リソース グループにホスティング プランの詳細を入力し、API App の名前を選択して、SugarCRM コネクタを構成します。

4. SugarCRM コネクタを構成し、[作成] をクリックします。次に、コネクタを作成するために入力する必要があるパッケージ設定を示します。

	<table>
  <tr>
    <td><b>名前</b></td>
    <td><b>必須</b></td>
    <td><b>説明</b></td>
  </tr>
  <tr>
    <td>サイトの URL</td>
    <td>あり</td>
    <td>SugarCRM インスタンスの URL を指定します。例: https://abcde1234.sugarcrm.com</td>
  </tr>
  <tr>
    <td>クライアント ID</td>
    <td>あり</td>
    <td>SugarCRM で OAuth 2.0 キーのコンシューマー キーを指定します。 </td>
  </tr>
  <tr>
    <td>クライアント シークレット</td>
    <td>あり</td>
    <td>SugarCRM で OAuth 2.0 キーのコンシューマー シークレットを指定します。 </td>
  </tr>
<tr>
    <td>ユーザー名</td>
    <td>あり</td>
    <td>SugarCRM ユーザーのユーザー名を指定します。</td>
  </tr>
	<tr>
    <td>パスワード</td>
    <td>あり</td>
    <td>SugarCRM ユーザーのパスワードを指定します。</td>
  </tr>
  <tr>
    <td>モジュール名</td>
    <td>あり</td>
    <td>操作を実行する SugarCRM モジュール (アカウント、連絡先、製品など) を指定します。<br><br>例: アカウント、潜在顧客、連絡先</td>
  </tr>
</table>![][9]



##ロジック アプリの作成##
SugarCRM にアカウントを作成し、このアカウントの請求先住所の詳細を更新する単純なロジック アプリを作成してみましょう。

1.	Azure ポータルにログインし、[新規作成]、[Web と Mobile]、[Logic App] の順にクリックします。

	![][1]

2.	[ロジック アプリの作成] ページで、名前、App Service プラン、場所などの必要な詳細を入力します。

	![][2]

3.	[トリガーとアクション] をクリックすると、Logic App エディターの画面が表示されます。[このロジックを手動で実行] を選択します。これは、このロジック アプリを手動でのみ起動できることを意味します。


5.	ギャラリーで [このリソース グループの API Apps] を展開し、使用可能なすべての API Apps を表示します。ギャラリーで [SugarCRM] を選択します。[SugarCRM コネクタ] がフローに追加されます。


	![][3]

6.	[アカウントの作成] アクションを選択します。入力パラメーターが表示されます。

	![][4]

12.	[名前] に「Microsoft Account」と入力し、[✓] をクリックします。

	![][5]

13.	ギャラリーの [最近使用した項目] セクションから [SugarCRM コネクタ] を選択すると、新しい SugarCRM アクションが追加されます。

14.	アクションの一覧の [アカウントの更新] を選択します。[アカウントの更新] の入力パラメーターが表示されます。

	![][6]

15.	[レコード ID] の横にある [+] をクリックし、[アカウントの作成] アクションの出力の ID 値を選択します。

	![][7]

16.	請求先住所の情報を入力し、[✓] をクリックします。

	![][8]

17. ロジック アプリ エディターの画面で [OK] をクリックし、[作成] をクリックします。作成が完了するまで約 30 秒かかります。

18. 新しく作成された Logic App を参照し、[実行] をクリックして実行します。

19. SugarCRM アカウントに Microsoft Account という名前の新しいアカウントが作成され、さらに、このアカウントの請求先住所の情報が更新されたことを確認できます。

<!--Image references-->
[1]: ./media/app-service-logic-connector-sugarcrm/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-sugarcrm/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-sugarcrm/3_Select_SugarCRM_Gallery.png
[4]: ./media/app-service-logic-connector-sugarcrm/4_SugarCRM_Create_Account.png
[5]: ./media/app-service-logic-connector-sugarcrm/5_Create_Account_OK.png
[6]: ./media/app-service-logic-connector-sugarcrm/6_SugarCRM_Update_Account.png
[7]: ./media/app-service-logic-connector-sugarcrm/7_Record_ID_from_Create.png
[8]: ./media/app-service-logic-connector-sugarcrm/8_Update_Account_Address.png
[9]: ./media/app-service-logic-connector-sugarcrm/9_Create_new_SugarCRM_connector.png

<!---HONumber=July15_HO3-->