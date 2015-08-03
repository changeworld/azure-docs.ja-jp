<properties
   pageTitle="QuickBooks コネクタ"
   description="QuickBooks コネクタの使用方法"
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


#Logic App で QuickBooks コネクタを使用する#

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。QuickBooks コネクタを使用すると、さまざまな QuickBooks エンティティを作成、変更できます。次に、QuickBooks コネクタでサポートされている QuickBooks エンティティの一覧を示します。

<Table>
<TR><TD><B>エンティティ</TD><TD><B>説明</TR> <TR> <TD> アカウント </TD> <TD> アカウントは、勘定科目表の構成要素で、元帳の一部です。特定の用途に対して割り当てられた合計金額を記録するために使用されます。 </TD> </TR> <TR> <TD> CreditMemo </TD> <TD> CreditMemo は、販売された商品またはサービスに対する返金、クレジットの支払い、一部の支払いを表す金融取引です。</TD> </TR> <TR> <TD> 顧客 </TD> <TD> 顧客は、ビジネスが提供するサービスまたは製品のコンシューマーです。</TD> </TR> <TR> <TD> 見積もり </TD> <TD> 見積もりは、ビジネスが顧客に商品やサービスの販売を提案する際の、価格案を含む金融取引の提案を表します。</TD> </TR> <TR> <TD> 請求書 </TD> <TD> 請求書は、顧客が製品またはサービスに対して後から支払う販売様式を表します。ここで、請求書データ モデルの使用に関する追加情報を参照できます。</TD> </TR> <TR> <TD> 項目 </TD> <TD> 項目は、会社が購入、販売、再販売するもの (製品、送料、手数料、割引、消費税 (該当する場合) など) です。1 つの項目は、請求書や他の販売様式で一行で表示されます。</TD> </TR> <TR> <TD> SalesReceipt </TD> <TD> このエンティティは、顧客に与えられるレシートを表します。 </TD> </TR> </Table>


##QuickBooks アクション ##
次に、QuickBooks コネクタで使用できるさまざまなアクションを示します。<table> <tbody> <tr><td> <strong>アクション</strong> </td> <td> <strong>説明</strong> </td> </tr> <tr> <td> エンティティの読み取り </td> <td> エンティティ オブジェクトを読み取ります。</td> </tr> <tr> <td> エンティティの作成または更新 </td> <td> エンティティ オブジェクトを作成または更新します。オブジェクトが既に存在する場合は更新され、それ以外の場合は新しいオブジェクトが作成されます。</td> </tr> <tr> <td> 削除 </td> <td> このアクションは、選択したエンティティから特定のオブジェクトを削除します。</td> </tr> <tr> <td> クエリ </td> <td> クエリ操作は、エンティティに対してガイド付きクエリを作成するためのメソッドです。 </td> </tr> </tbody> </table>

##QuickBooks コネクタの API App を作成する##
1.	Azure ポータルの右下にある [+ 新規] オプションを使用して Azure Marketplace を開きます。
2.	[Web + モバイル]、[API Apps] の順に移動し、“QuickBooks” を検索します。
3.	リソース グループにホスティング プランの詳細を入力し、API App の名前を選択して、QuickBooks コネクタを構成します。

	![][13]
4. [パッケージ設定] で、読み取りと書き込みに関心がある QuickBooks エンティティを構成します。

これで、QuickBooks コネクタの API App を作成できます。


##ロジック アプリの作成##
QuickBooks にアカウントを作成し、同じアカウントの [カテゴリの種類] を更新する、単純なロジック アプリを作成してみましょう。

1.	Azure ポータルにログインし、[新規作成]、[Web と Mobile]、[Logic App] の順にクリックします。

	![][1]

2.	[ロジック アプリの作成] ページで、名前、App Service プラン、場所などの必要な詳細を入力します。

	![][2]

3.	[トリガーとアクション] をクリックすると、ロジック アプリ エディターの画面が表示されます。

	![][3]

4.	[このロジックを手動で実行] を選択します。これは、このロジック アプリを手動でのみ起動できることを意味します。


5.	ギャラリーで [このリソース グループの API Apps] を展開し、使用可能なすべての API Apps を表示します。ギャラリーから [QuickBooks コネクタ] を選択すると、[QuickBooks コネクタ] がフローに追加されます。


6.	QuickBooks がオンラインの場合は、ユーザーの代わりに操作を実行するロジック アプリの認証と承認を行います。承認を開始するには、QuickBooks コネクタの [承認] をクリックします。

	![][4]

7.	[承認] をクリックすると、QuickBooks の承認ダイアログが開きます。操作を実行する QuickBooks アカウントのログインの詳細情報を入力します。

	![][5]

8. 同意ダイアログの [承認] をクリックして、ユーザーの代わりに操作を実行するように、アカウントにロジック アプリのアクセス権を付与します。

	![][6]

9.	承認が完了すると、すべてのアクションが表示されます。

	![][7]

10.	[アカウントの作成または更新] アクションを選択すると、入力パラメーターが表示されます。

	![][8]

11.	[名前] と [アカウントの種類] を入力し、[✓] をクリックします。

	![][9]

12.	ギャラリーの [最近使用した項目] セクションから [QuickBooks コネクタ] を選択すると、新しい QuickBooks アクションが追加されます。

13.	アクションの一覧から [アカウントの作成または更新] を選択すると、そのアクションの入力パラメーターが表示されます。

	![][10]

14.	[ID] の横にある [+] をクリックし、[アカウントの作成] アクションの出力の ID 値を選択します。

	![][11]

15.	[アカウントの種類] に新しい値を入力し、[✓] をクリックします。

	![][12]

16. ロジック アプリ エディターの画面で [OK] をクリックし、[作成] をクリックします。作成が完了するまで約 30 秒かかります。

17. 新しく作成された Logic App を参照し、[実行] をクリックして実行します。

18. QuickBooks アカウントに "Contoso" という名前の新しいアカウントが作成されることを確認します。

<!--Image references-->
[1]: ./media/app-service-logic-connector-quickbooks/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-quickbooks/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-quickbooks/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-quickbooks/4_QuickBooks_Authorize.png
[5]: ./media/app-service-logic-connector-quickbooks/5_QuickBooks_Login.png
[6]: ./media/app-service-logic-connector-quickbooks/6_QuickBooks_User_Consent.png
[7]: ./media/app-service-logic-connector-quickbooks/7_QuickBooks_Actions.png
[8]: ./media/app-service-logic-connector-quickbooks/8_QuickBooks_Create_Account.png
[9]: ./media/app-service-logic-connector-quickbooks/9_Create_Account_OK.png
[10]: ./media/app-service-logic-connector-quickbooks/10_QuickBooks_Update_Account.png
[11]: ./media/app-service-logic-connector-quickbooks/11_Record_ID_from_Create.png
[12]: ./media/app-service-logic-connector-quickbooks/12_Update_Account_Address.png
[13]: ./media/app-service-logic-connector-quickbooks/13_Create_new_quickbooks_connector.png

<!---HONumber=July15_HO4-->