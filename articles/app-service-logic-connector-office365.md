<properties 
   pageTitle="Office 365 コネクタ" 
   description="Office 365 コネクタの使用方法" 
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


# ロジック アプリでの Office 365 コネクタの使用#

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。Office 365 コネクタでは、Office 365 アカウントで電子メールを送受信し、予定表と連絡先を管理できます。電子メールの送受信や取得、予定表のイベントの作成や削除、連絡先の作成、更新、取得や削除など、さまざまなアクションを実行できます。

**基本的なアクション**

- 新しい電子メール (トリガー)
- メールの送信
- メールへの返信
- 送信イベント
- 連絡先の追加


## ロジック アプリの作成##
ロジック アプリで Office 365 コネクタを使用するには、コネクタの API アプリをあらかじめ作成しておきます。または、その場でロジック アプリを使用してアプリを作成できます。ここでは、電子メールを受信したときに起動する簡単なロジック アプリを作成します (受信先は、sales@contoso.com などの営業用の問い合わせ電子メール ID とします)。また、このロジック アプリでは、イベントの作成、差出人の詳細を含めた連絡先の追加、個人用アカウントへの電子メールの送信を行い、最後に、受信を確認したことを知らせる返信を送信します。

1.	Azure ポータルにログインし、[新規作成]、[Web とモバイル]、[ロジック アプリ] の順にクリックします。

	![][1]

2.	[ロジック アプリの作成] ページで、名前、App Service プラン、場所などの必要な詳細を入力します。

	![][2]

3.	[トリガーとアクション] をクリックすると、ロジック アプリ エディターの画面が表示されます。

	![][3]

4.	ギャラリーで [新規作成] を展開し、使用可能なすべての API アプリを表示します。ギャラリーから [Office 365] を選択すると、"Office 365 トリガー" がフローに追加されます。
	![][4]

5.	これにより、新しい "Office 365 コネクタ" API アプリが、ロジック アプリと同じリソース グループに作成されます。API アプリが作成されるまで、約 30 秒かかります。

6.	アプリが作成されたら、[承認] をクリックし、Office 365 のログイン資格情報を入力します。

	![][5]

7.	Office 365 のログイン ページにリダイレクトされ、Office 365 アカウントの資格情報で認証することができます。

	![][6]

	![][7]

8.	承認が完了すると、Office 365 トリガーが表示されます。

	![][8]

9.	"新しい電子メール" トリガーを選択すると、入力パラメーターが表示されます。


10.	トリガーの頻度を [ 'Minutes'] に変更して、[✓] をクリックします。 

	![][9]

11. Office 365 の  'New Email' トリガーが構成され、出力パラメーターも表示されているのが確認できます。
	
	![][10]

12.	ギャラリーの [最近使用した項目] セクションから [Office 365 コネクタ] を選択すると、新しい "Office 365" アクションが追加されます。

13.	アクションのリストから [送信イベント] を選択すると、"送信イベント" アクションの入力パラメーターが表示されます。

	![][11]

14.	イベントの詳細を指定して、[✓] をクリックします。

	![][12]

15.	ギャラリーの [最近使用した項目] セクションから [Office 365 コネクタ] を選択すると、新しい "Office 365" アクションが追加されます。

16.	アクションのリストから [連絡先の追加] を選択すると、"連絡先の追加" アクションの入力パラメーターが表示されます。

	![][13]

17.	 'Email Address' フィールドの隣にある [+] をクリックし、トリガーから  'From' 出力フィールドの値を選択します。

	![][14]

18. [✓] をクリックすると、アクションの構成が完了します。

	![][15]

19.	ギャラリーの [最近使用した項目] セクションから [Office 365 コネクタ] を選択すると、新しい "Office 365" アクションが追加されます。


20.	アクションのリストから [電子メールの送信] を選択すると、"電子メールの送信" アクションの入力パラメーターが表示されます。

	![][19]

21.	電子メールの送信に必要な詳細を入力します。以下のような文字列を入力すると、メッセージを作成できます。 'Send Email' アクションが構成されたら、[✓] をクリックします。

		Body - @concat('You got a new sales enquiry from',triggers().output.body.From)

	![][20]
22.	ギャラリーの [最近使用した項目] セクションから [Office 365 コネクタ] を選択すると、新しい "Office 365" アクションが追加されます。


23.	アクションのリストから [返信] を選択すると、"返信" アクションの入力パラメーターが表示されます。

	![][16]

24.	 'From' フィールドの隣にある [+] をクリックし、トリガーの出力メッセージ ID から値を選択して、[✓] をクリックします。

	![][17]

25. ロジック アプリ エディターの画面で [OK] をクリックし、[ 'Create'] をクリックします。作成が完了するまで約 30 秒かかります。

26. トリガーを構成したアカウントに電子メールを送信すると、個人用のメール アカウントに電子メール、Office のメール アカウントに予定表のイベントおよび連絡先が表示されます。また、営業の問い合わせに対してすぐに回答することを知らせる返信も受け取ります。

<!--Image references-->
[1]: ./media/app-service-logic-connector-office365/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-office365/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-office365/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-office365/4_Select_Office365_Gallery.png
[5]: ./media/app-service-logic-connector-office365/5_Office365_Authorize.png
[6]: ./media/app-service-logic-connector-office365/6_Office365_Login.png
[7]: ./media/app-service-logic-connector-office365/7_Office365_User_Consent.png
[8]: ./media/app-service-logic-connector-office365/8_Office365_Trigger.png
[9]: ./media/app-service-logic-connector-office365/9_Office365_Trigger_Settings.png
[10]: ./media/app-service-logic-connector-office365/10_Office365_Trigger_Configured.png
[11]: ./media/app-service-logic-connector-office365/11_Office365_Actions_List.png
[12]: ./media/app-service-logic-connector-office365/12_Office365_Create_Event_Inputs.png
[13]: ./media/app-service-logic-connector-office365/13_Office365_Add_Contact_Inputs.png
[14]: ./media/app-service-logic-connector-office365/14_Office365_Add_Contact_Email_FromTrigger.png
[15]: ./media/app-service-logic-connector-office365/15_Office365_Add_Contacts_Configured.png
[16]: ./media/app-service-logic-connector-office365/16_Office365_Reply_To_Inputs.png
[17]: ./media/app-service-logic-connector-office365/17_Office365_Reply_To_MessageId.png
[18]: ./media/app-service-logic-connector-office365/18_Office365_Reply_To_Configured.png
[19]: ./media/app-service-logic-connector-office365/19_Office365_Send_Inputs.png
[20]: ./media/app-service-logic-connector-office365/20_Office365_Send_Configured.png


<!--HONumber=49-->