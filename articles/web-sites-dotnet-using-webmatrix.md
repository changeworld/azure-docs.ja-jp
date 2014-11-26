<properties linkid="develop-dotnet-website-with-webmatrix" urlDisplayName="Website with WebMatrix" pageTitle=".NET website with WebMatrix - Azure tutorials" metaKeywords="WebMatrix Azure, WebMatrix Azure, Azure web site WebMatrix, Azure website WebMatrix, Web Matrix Azure, WebMatrix Azure" description="Learn how to develop and deploy an Azure website with WebMatrix." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Develop and deploy a website with Microsoft WebMatrix" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Microsoft WebMatrix を使用して Web サイトを開発してデプロイする

このガイドでは、Microsoft WebMatrix を使用して Web サイトを作成し、それを Azure にデプロイする方法について説明します。WebMatrix のサイト テンプレートにあるサンプル アプリケーションを使用します。

学習内容:

-   WebMatrix 内から Azure にサインインする方法
-   WebMatrix による組み込みテンプレートを使用してサイトを作成する方法
-   カスタマイズした Web サイトを直接 WebMatrix から Azure にデプロイする方法

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Azure へのサインイン

1.  WebMatrix を起動します。
2.  初めて WebMatrix 3 を使用する場合は、Azure へのサインインを求めるメッセージが表示されます。そうでない場合は、**[サインイン]** ボタンをクリックし、**[アカウントの追加]** を選択します。Microsoft アカウントを使用して**サインイン**することを選択します。

    ![アカウントの追加][アカウントの追加]

3.  Azure アカウントにサインアップしている場合は、Microsoft アカウントを使用してログインできます。

    ![Azure へのサインイン][Azure へのサインイン]

## Azure の組み込みテンプレートを使用したサイトの作成

1.  スタート画面で **[新規作成]** ボタンをクリックし、**[テンプレート ギャラリー]** を選択して、テンプレート ギャラリーから新しいサイトを作成します。

    ![テンプレート ギャラリーからの新しいサイト][テンプレート ギャラリーからの新しいサイト]

2.  テンプレート ギャラリーに、ローカルにまたは Azure で実行できる使用可能なテンプレートの一覧が表示されます。テンプレートの一覧から **[Bakery]** を選択し、**[サイト名]** に「**bakerysample**」と入力して、**[次へ]** をクリックします。

    ![テンプレートからサイトを作成][テンプレートからサイトを作成]

3.  Azure にサインインしている場合は、ここでローカル サイト用の Azure Websites を作成することができます。一意の名前を選択し、サイトの作成先のデータ センターを選択します。

    ![Azure でのサイトの作成][Azure でのサイトの作成]

    WebMatrix による Web サイトの構築が終了すると、WebMatrix IDE が表示されます。

    ![WebMatrix IDE][WebMatrix IDE]

## Web サイトをテストする

パン屋のサンプルには、シミュレーションとして、指定した Windows Live Hotmail アカウントに注文品目を記載したメールを送信する注文フォームが用意されています。

1.  WebMatrix の左側にあるナビゲーション ウィンドウで、**[bakerysample]** フォルダーを展開します。

    ![][0]

2.  ファイル名をダブルクリックして、*Order.cshtml* ページを開きます。

    ![][1]

3.  「//SMTP Configuration for Hotmail」というコメントを探します。

    ![][2]

4.  自分のメール プロバイダーに合わせて、次の行の値を変更します。

        WebMail.SmtpServer = "smtp.live.com";
        WebMail.SmtpPort  = 25;
        WebMail.EnableSsl = true; 

        //Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

    WebMail.SmtpServer の値を、メール送信に通常使用するメール サーバーの名前に変更します。続いてユーザー名とパスワードの値を指定します。From プロパティは自分のメール アドレスに設定します。

5.  WebMatrix リボンの **[実行]** をクリックして、サイトをテストします。

    ![][3]

6.  商品の 1 つで **[Order Now]** をクリックして、注文を自分に送信します。

7.  注文の確認メールを受け取ったことを確認します。メールを正しく送信できない場合は、ASP.NET Web ページ (Razor) のトラブルシューティング ガイドにある「[Issues with Sending Email (メール送信関連の問題)][Issues with Sending Email (メール送信関連の問題)]」を参照してください。

## カスタマイズした Web サイトを WebMatrix から Azure にデプロイする

1.  WebMatrix で、**[ホーム]** リボンの **[発行]** をクリックします。Web サイトの **[発行のプレビュー]** ダイアログ ボックスが表示されます。

    ![WebMatrix の発行のプレビュー][WebMatrix の発行のプレビュー]

2.  bakery.sdf の横にあるチェック ボックスをオンにして、**[続行]** をクリックします。発行が完了すると、更新された Azure の Web サイトの URL が WebMatrix IDE の下部に表示されます。

    ![発行の完了][発行の完了]

3.  リンクをクリックして、ブラウザーで Web サイトを開きます。

    ![パン屋のサンプル サイト][パン屋のサンプル サイト]

    Web サイトの URL は Azure ポータルでも確認できます。**[Web サイト]** をクリックすると、ユーザーのサブスクリプションに関連付けられた Web サイトがすべて表示されます。各 Web サイトの URL は Web サイトのページの [URL] 列に表示されます。

## Web サイトを修正して Azure Web サイトに再発行する

WebMatrix を使用してサイトを修正し、それを Azure Web サイトに再発行することができます。以下の手順では、注文がギフトであることを示すチェック ボックスを追加します。

1.  *Order.cshtml* のページを開きます。

2.  "shipping" クラスのフォーム定義を見つけます。\<li\> ブロックの直後に次のコードを挿入します。

        <li class="gift">
            <div class="fieldcontainer" data-role="fieldcontain">
                <label for="isGift">This is a gift</label>           
                <div>@Html.CheckBox("isGift")</div>
            </div>
        </li>

    ![][4]

3.  ファイルから「var shipping = Request["orderShipping"];」行を見つけて、その直後に次のコード行を挿入します。

        var gift = Request["isGift"];

4.  発送先住所が空でないことを検証するコード ブロックの直後に、次のコードを挿入します。

        if(gift != null) {
            body += "This is a gift." + "<br/>";
        }

    *order.cshtml* ファイルは次の図のようになります。

    ![][5]

5.  ファイルを保存し、ローカルでサイトを実行して、テスト注文を自分に送信します。新しいチェック ボックスのテストを忘れないでください。

    ![][6]

6.  **[ホーム]** リボンの **[発行]** をクリックしてサイトを再発行します。

7.  **[発行のプレビュー]** ダイアログ ボックスで、Order.cshtml のチェック ボックスがオンになっていることを確認して、[続行] をクリックします。

8.  リンクをクリックしてブラウザーで Web サイトを開き、Azure Web サイトの更新内容をテストします。

# 次のステップ

これで WebMatrix から Web サイトを作成して Azure に展開する方法はわかりました。WebMatrix の詳細については、次のリソースを参照してください。

-   [Azure 用 WebMatrix の概要][Azure 用 WebMatrix の概要]

-   [WebMatrix の Web サイト][WebMatrix の Web サイト]

  [アカウントの追加]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-add-account.png
  [Azure へのサインイン]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-sign-in.png
  [テンプレート ギャラリーからの新しいサイト]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template.png
  [テンプレートからサイトを作成]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-details.png
  [Azure でのサイトの作成]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-azure.png
  [WebMatrix IDE]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide.png
  [0]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-1.png
  [1]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-2.png
  [2]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-3.png
  [3]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-4.png
  [WebMatrix の発行のプレビュー]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png
  [発行の完了]: ./media/web-sites-dotnet-using-webmatrix/howtopublished2.png
  [パン屋のサンプル サイト]: ./media/web-sites-dotnet-using-webmatrix/howtobakerysamplesite.png
  [4]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-5.png
  [5]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-6.png
  [6]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-7.png
  [Azure 用 WebMatrix の概要]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [WebMatrix の Web サイト]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
