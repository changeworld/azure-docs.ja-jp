<properties urlDisplayName="Website with WebMatrix" pageTitle="WebMatrix を使用した .NET Web サイト - Azure チュートリアル" metaKeywords="WebMatrix Azure, WebMatrix Azure, Azure Web サイト WebMatrix, Azure Web サイト WebMatrix, Web Matrix Azure, WebMatrix Azure" description="Learn how to develop and deploy an Azure website with WebMatrix." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Develop and deploy a website with Microsoft WebMatrix" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/27/2014" ms.author="tomfitz" />





#Microsoft WebMatrix を使用して Web サイトを開発してデプロイする
このガイドでは、Microsoft WebMatrix を使用して Web サイトを作成し、それを Azure にデプロイする方法について説明します。WebMatrix のサイト テンプレートにあるサンプル アプリケーションを使用します。

学習内容:

* WebMatrix 内から Azure にサインインする方法
* WebMatrix による組み込みテンプレートを使用してサイトを作成する方法 
* カスタマイズした Web サイトを直接 WebMatrix から Azure にデプロイする方法


[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Azure へのサインイン

1. WebMatrix を起動します。
2. 初めて WebMatrix 3 を使用する場合は、Azure へのサインインを求めるメッセージが表示されます。そうでない場合は、**[サインイン]** ボタンをクリックし、**[アカウントの追加]** を選択します。Microsoft アカウントを使用して**サインイン**することを選択します。

	![Add Account][addaccount]

3. Azure アカウントにサインアップしている場合は、Microsoft アカウントを使用してログインできます。

	![Sign into Azure][signin]	


## Azure の組み込みテンプレートを使用したサイトの作成

1. スタート画面で **[新規作成]** ボタンをクリックし、**[テンプレート ギャラリー]** を選択して、テンプレート ギャラリーから新しいサイトを作成します。

	![New site from Template Gallery][sitefromtemplate]

2. テンプレート ギャラリーに、ローカルにまたは Azure で実行できる使用可能なテンプレートの一覧が表示されます。テンプレートの一覧から **[Bakery]** を選択し、**[サイト名]** に「**bakerysample**」と入力して、**[次へ]** をクリックします。

	![Create Site from Template][sitefromtemplatedetails]

3. Azure にサインインしている場合は、ここでローカル サイト用の Azure Web サイトを作成することができます。一意の名前を選択し、サイトの作成先のデータ センターを選択します。 

	![Create site on Azure][sitefromtemplateazure]

	WebMatrix による Web サイトの構築が終了すると、WebMatrix IDE が表示されます。

	![WebMatrix IDE][howtowebmatrixide] 

## 電子メールをセットアップします。

パン屋のサンプルには、注文された品目が記載された電子メール メッセージを送信するシミュレートされた注文フォームが含まれています。Azure で SendGrid 電子メール サービスを使用して、サイトからの電子メールを送信できます。

1. チュートリアル「[SendGrid を使用した Azure での電子メールの送信方法][sendgridexample]」の手順に従い、SendGrid アカウントを設定し、接続情報を取得します。チュートリアル全体を実行する必要はありません。接続情報を取得するところまでで実行します。

2. プロジェクトに SendGrid NuGet パッケージを追加します。まず、[NuGet] ボタンをクリックします。

    ![Add SendGrid][addsendgrid]

    SendGrid を検索し、それをインストールします。

    ![Install SendGrid][installsendgrid]

    パッケージのインストールが完了すると、SendGrid アセンブリが Bin に追加されたことがわかります。

    ![SendGrid added][binsendgrid]

3. ファイル名をダブルクリックして、*Order.cshtml* ページを開きます。

	![][modify2]

4. ファイルの先頭に、次のコードを追加します。

        @using SendGrid;
        @using System.Net.Mail;

4. //SMTP Configuration for Hotmail というコメントを検索し、Web メールを使用するためのコードをすべて削除するか、コメント アウトします。

        /*
        //SMTP Configuration for Hotmail
        WebMail.SmtpServer = "smtp.live.com";
        WebMail.SmtpPort = 25;
        WebMail.EnableSsl = true;

        //Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

        if (WebMail.UserName.IsEmpty() || WebMail.Password.IsEmpty() || WebMail.From.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        } 
        else {
            try {
                WebMail.Send(to: customerEmail, subject: "Fourth Coffee - New Order", body: body);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }*/


5. Web メールではなく SendGrid を使用して電子メールを送信するためのコードを追加します。前の手順で削除したコードの代わりに次のコードを追加します。

		 if (email.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        }
        else {
            // Create the email object first, then add the properties.
            SendGridMessage myMessage = new SendGridMessage();
            myMessage.AddTo(email);
            myMessage.From = new MailAddress("FourthCoffee@example.com", "Fourth Coffee");
            myMessage.Subject = "Fourth Coffee - New Order";
            myMessage.Text = body;

            // Create credentials, specifying your user name and password.
            var credentials = new NetworkCredential("[your user name", "[your password]");

            // Create an Web transport for sending email.
            var transportWeb = new Web(credentials);

            // Send the email.
            try {
                transportWeb.Deliver(myMessage);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }


6. WebMatrix リボンの **[実行]** をクリックして、サイトをテストします。

	![][modify4]

7. 商品の 1 つで **[Order Now]** をクリックして、注文を自分に送信します。

8. 注文の確認メールを受け取ったことを確認します。メールを正しく送信できない場合は、ASP.NET Web ページ (Razor) のトラブルシューティング ガイドのページにある[メール送信関連の問題の項目][sendmailissues]を参照してください。
 

## カスタマイズした Web サイトを WebMatrix から Azure にデプロイする

1. WebMatrix で、**[ホーム]** リボンの **[発行]** をクリックします。Web サイトの **[発行のプレビュー]** ダイアログ ボックスが表示されます。

	![WebMatrix Publish Preview][howtopublishpreview]

2. bakery.sdf の横にあるチェック ボックスをオンにして、**[続行]** をクリックします。発行が完了すると、更新された Azure の Web サイトの URL が WebMatrix IDE の下部に表示されます。  

	![Publishing Complete][publishcomplete]

3. リンクをクリックして、ブラウザーで Web サイトを開きます。

	![Bakery Sample Site][bakerysample]

	Web サイトの URL は Azure ポータルでも確認できます。**[Web サイト]** をクリックすると、ユーザーのサブスクリプションに関連付けられた Web サイトがすべて表示されます。各 Web サイトの URL は Web サイトのページの [URL] 列に表示されます。

## Web サイトを修正して Azure Web サイトに再発行する

WebMatrix を使用してサイトを修正し、それを Azure Web サイトに再発行することができます。以下の手順では、注文がギフトであることを示すチェック ボックスを追加します。

1. *Order.cshtml* ページを開きます。

2. shipping クラスのフォーム定義を見つけます。&lt;li&gt; ブロックの直後に次のコードを挿入します。
		
		<li class="gift">
		    <div class="fieldcontainer" data-role="fieldcontain">
		        <label for="isGift">This is a gift</label>           
		        <div>@Html.CheckBox("isGift")</div>
		    </div>
		</li>

	![][modify5]

3. ファイルから "var shipping = Request["orderShipping"];" 行を見つけて、その直後に次のコード行を挿入します。

		var gift = Request["isGift"];

4. 発送先住所が空でないことを検証するコード ブロックの直後に、次のコードを挿入します。

		if(gift != null) {
			body += "This is a gift." + "<br/>";
		}

	*order.cshtml* ファイルは次の図のようになります。

	![][modify6]

5. ファイルを保存し、ローカルでサイトを実行して、テスト注文を自分に送信します。新しいチェック ボックスのテストを忘れないでください。

	![][modify7]

6. **[ホーム]** リボンの **[発行]** をクリックしてサイトを再発行します。

7. **[発行のプレビュー]** ダイアログ ボックスで、Order.cshtml のチェック ボックスがオンになっていることを確認して、[続行] をクリックします。

8. リンクをクリックしてブラウザーで Web サイトを開き、Azure Web サイトの更新内容をテストします。

# 次のステップ

これで WebMatrix から Web サイトを作成して Azure にデプロイする方法はわかりました。WebMatrix の詳細については、次のリソースを参照してください。

* [Azure 用 WebMatrix の概要](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [WebMatrix の Web サイト](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[howtowebmatrixide]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide2.png
[howtopublishpreview]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png
[addsendgrid]: ./media/web-sites-dotnet-using-webmatrix/addsendgridpackage.png
[installsendgrid]: ./media/web-sites-dotnet-using-webmatrix/installsendgrid.png
[binsendgrid]: ./media/web-sites-dotnet-using-webmatrix/sendgridbin.png

[publishcomplete]: ./media/web-sites-dotnet-using-webmatrix/howtopublished2.png
[bakerysample]: ./media/web-sites-dotnet-using-webmatrix/howtobakerysamplesite.png
[addaccount]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template.png
[sitefromtemplatedetails]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-details.png
[sitefromtemplateazure]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-azure.png

[modify1]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-1.png
[modify2]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-2.png
[modify3]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-3.png
[modify4]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-4.png
[modify5]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-5.png
[modify6]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-6.png
[modify7]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-7.png



[sendmailissues]: http://go.microsoft.com/fwlink/?LinkId=253001#email
[sendgridexample]: http://azure.microsoft.com/ja-jp/documentation/articles/sendgrid-dotnet-how-to-send-email/
