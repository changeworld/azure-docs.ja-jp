<properties linkid="dev-net-how-to-access-control" urlDisplayName="アクセス制御" pageTitle="アクセス制御の使用方法 (.NET) - Azure の機能ガイド" metaKeywords="Azure アクセス制御サービス認証 C#" description="このガイドでは、ユーザーが Web アプリケーションにアクセスしようとするときに、それらのユーザーを認証するために Azure アプリケーションでアクセス制御サービス (ACS) を使用する方法について説明します。" metaCanonical="" services="active-directory" documentationCenter=".NET" title="Azure Active Directory のアクセス制御で Web ユーザーを認証する方法" authors=""  solutions="" writer="juneb" manager="" editor="" />



# Azure Active Directory のアクセス制御で Web ユーザーを認証する方法


このガイドでは、Microsoft、Google、Yahoo、Facebook などの ID プロバイダーのユーザーが Web アプリケーションにアクセスしようとするときに、それらのユーザーを認証するために Azure Active Directory の Access Control (Access Control サービス (ACS) とも呼ばれます) を使用する方法について説明します。

<h2><span class="short-header">目次</span>目次</h2>

-   [ACS とは][]
-   [概念][]
-   [前提条件][]
-   [Access Control 名前空間の作成][]
-   [ASP.NET MVC アプリケーションを作成する][]
-   [Web アプリケーションを ACS と統合する][]
-   [ACS との統合をテストする][]
-   [ACS により送信されたクレームを参照する][vcsb]
-   [ACS 管理ポータルでアプリケーションを参照する][vpp]
-   [ID プロバイダーを追加する][]
-   [参照トピック][]

<h2><span class="short-header">ACS とは</span>ACS とは</h2>

ほとんどの開発者は ID の専門家ではなく、アプリケーションやサービスの認証と承認のメカニズムの開発に時間をかけたくはありません。ACS は、Web アプリケーションとサービスにアクセスするユーザーを認証するための簡単な方法を提供する Azure サービスです。これを使えば、複雑な認証ロジックをコードに追加する必要はありません。

ACS では、次の機能を使用できます。

-   Windows Identity Foundation (WIF) との統合。
-   Microsoft アカウント (旧 Windows Live ID)、Google、Yahoo、Facebook などの一般的な Web ID プロバイダーのサポート。
-   Active Directory フェデレーション サービス (AD FS) 2.0 のサポート。
-   -   ACS 設定へのプログラムによるアクセスを提供する Open Data Protocol 
    (OData) ベースの管理サービス。
-   -   ACS 設定への管理アクセスが可能な管理ポータル。

ACS の詳細については、[アクセス制御サービス 2.0 に関するページ][]を参照してください。

<h2><span class="short-header">概念</span>概念</h2>

ACS はクレームベース ID のプリンシパルに基づいており、これは内部またはクラウドで実行されているアプリケーションの認証メカニズムを作成するための一貫した方法になっています。クレームベース ID は、アプリケーションとサービスが組織内、他の組織内、およびインターネット上のユーザーに関する必要な ID 情報を取得するための一般的な方法を提供します。

このガイドのタスクを完了するには、このガイドで使用される次の用語と概念を理解している必要があります。


**クライアント**: Web アプリケーションにアクセスしようとするブラウザー。

**証明書利用者 (RP) アプリケーション**: 使用している Web アプリケーション。RP アプリケーションは、外部の機関に認証を委託する Web サイトまたは Web サービスです。ID の分野では、"RP がその機関を信頼する" という表現を使います。このガイドでは、アプリケーションが ACS を信頼するように構成する方法について説明します。

**トークン**: ユーザーは RP アプリケーションが信頼している機関によって発行された有効なトークンを提示することによって、RP アプリケーションにアクセスできます。クライアントの認証時に発行されたセキュリティ データのコレクションです。これには、クレームのセット、つまり、ユーザーの名前や年齢、ユーザー ロールの ID など、認証されたユーザーの属性が含まれます。トークンはデジタル署名されるため、発行者を特定でき、その内容は変更できません。

**ID プロバイダー (IP)**: ユーザー ID を認証し、セキュリティ トークンを発行する機関。Microsoft アカウント (Windows Live ID)、Facebook、Google、Twitter、Active Directory などです。ACS が IP を信頼するように構成されている場合、ACS はその IP によって発行されたトークンを受け入れて検証します。ACS は同時に複数の IP を信頼できるので、アプリケーションが ACS を信頼すると、ACS が信頼しているいずれかの IP によって認証するオプションをアプリケーションでユーザーに提示できます。

**フェデレーション プロバイダー (FP)**: ID プロバイダー (IP) はユーザーについての直接の情報を持ち、資格情報を使ってユーザーを認証し、ユーザーに関するクレームを発行します。フェデレーション プロバイダー (FP) は、それとは別の種類の機関です。ユーザーを直接認証する代わりに、FP は認証を仲介します。証明書利用者アプリケーションと 1 つ以上の IP の間の仲介役として機能します。ACS はフェデレーション プロバイダー (FP) です。

**ACS 規則エンジン**: クレーム変換規則は、信頼されている IP からのトークンのクレームを変換するため、RP で使用できます。ACS には、RP に指定するクレーム変換規則を適用する規則エンジンが含まれています。

**Access Control 名前空間**: アプリケーション内で ACS リソースのアドレスを指定するための一意のスコープが提供されます。この名前空間には、信頼する IP、サービスを提供する RP アプリケーション、受信トークンに適用する規則などの設定が含まれ、アプリケーションと開発者が ACS とのやり取りに使用するエンドポイントが表示されます。

次の図は、Web アプリケーションで ACS 認証がどのように機能するかを示しています。

![][0]

1.  クライアント (ここではブラウザー) が RP のページを要求します。
2.  要求がまだ認証されていないので、RP は信頼している機関 (つまり
    ACS) にユーザーをリダイレクトします。ACS は、この RP 
    用に指定されている IP の選択肢をユーザーに提示します。ユ
    ーザーが適切な IP を選択します。
3.  クライアントは IP の認証ページに移動し、ユーザーにログオンを求めま
    す。
4.  クライアントが認証されたら (たとえば、ID 資格情報が入力さ
    れたら)、IP はセキュリティ トークンを発行します。
5.  セキュリティ トークンの発行後、IP は、IP が ACS に発行したセキュリティ トークンを送信するようクライアントに指示します。
6.  ACS は IP によって発行されたセキュリティ トークンを検証し、
    このトークンの ID クレームを ACS 規則エンジンに入力して出力 ID 
    クレームを計算し、これらの出力クレームを含む新しいセキュリティ
    トークンを発行します。
7.  ACS は、ACS が RP に発行したセキュリティ トークンを送信するようクライアントに指示します。RP がセキュリティ トークンの署名を検証して、アプリケーション ビジネス ロジックにより使用されるクレームを抽出し、最初に要求されたページを返します。

<h2><span class="short-header">前提条件</span>前提条件</h2>


このガイドのタスクを完了するには、次のものが必要です。

-	Azure サブスクリプション
-	Microsoft Visual Studio 2012
-	Identity and Access Tool for Visual Studio 2012 (ダウンロードするには、[Identity and Access Tool のダウンロード ページ][]を参照)


<h2><span class="short-header">Access Control 名前空間の作成</span>Access Control 名前空間の作成</h2>

Azure で Active Directory のアクセス制御を使用するには、Access Control 名前空間を作成します。名前空間では、アプリケーション内で ACS リ
ソースのアドレスを指定するための一意のスコープが提供されます。

1. [Azure の管理ポータル][] (https://manage.WindowsAzure.com) にログインします。
    
2. **[Active Directory]** をクリックします。

	![][1]

3. 新しい Access Control 名前空間を作成するには、**[新規]** をクリックします。**[アプリケーション サービス]** と **[アクセス制御]** が選択されます。**[簡易作成]** をクリックします。

	![][2]

4. 名前空間の名前を入力します。名前が一意であるかが確認されます。

5. 名前空間が使用されるリージョンを選択します。パフォーマンスを最高にするには、アプリケーションを展開するリージョンと同じにし、**[作成]** をクリックします。

名前空間が作成されて有効化されます。

<h2><span class="short-header">ASP.NET MVC アプリケーションの作成</span>ASP.NET MVC アプリケーションの作成</h2>

この手順では、ASP.NET MVC アプリケーションを作成します。後の手順では、このシンプルな Web フォーム アプリケーションを ACS に統合します。

1.	Visual Studio 2012 または Visual Studio Express for Web 2012 を起動します (それより前のバージョンの Visual Studio は、このチュートリアルでは機能しません)。
1.	**[ファイル]** をクリックし、**[新しいプロジェクト]** をクリックします。
1.	Visual C#/Web テンプレートを選択し、**[ASP.NET MVC 4 Web アプリケーション]** を選択します。

	このガイドでは MVC アプリケーションを使用しますが、このタスクにはどの種類の Web アプリケーションでも使用できます。

	![][3]

1. **[名前]** に、「**MvcACS**」と入力して **[OK]** をクリックします。
1. 次のダイアログで、**[インターネット アプリケーション]** を選択して **[OK]** をクリックします。
1. *Views\Shared\_LoginPartial.cshtml* ファイルを編集し、内容を次のコードに置き換えます。

        @if (Request.IsAuthenticated)
        {
            string name = "Null ID";
            if (!String.IsNullOrEmpty(User.Identity.Name))
            {
                name = User.Identity.Name;
            }    
            <text>
            Hello, @Html.ActionLink(name, "Manage", "Account", routeValues: null, htmlAttributes: new { @class = "username", title = "Manage" })!
                    @using (Html.BeginForm("LogOff", "Account", FormMethod.Post, new { id = "logoutForm" }))
                    {
                        @Html.AntiForgeryToken()
                        <a href="javascript:document.getElementById('logoutForm').submit()">Log off</a>
                    }
            </text>
        }
        else
        {
            <ul>
                <li>@Html.ActionLink("Register", "Register", "Account", routeValues: null, htmlAttributes: new { id = "registerLink" })</li>
                <li>@Html.ActionLink("Log in", "Login", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
            </ul>
        }

現在のところ、ACS は User.Identity.Name を設定しないため、前に示している変更を加える必要があります。

1. F5 キーを押してアプリケーションを実行します。既定の ASP.NET MVC アプリケーションが Web ブラウザーに表示されます。

<h2><span class="short-header">Web アプリケーションを ACS と統合する</span>Web アプリケーションを ACS と統合する</h2>

このタスクでは、ASP.NET Web アプリケーションを ACS と統合します。

1.	ソリューション エクスプローラーで、MvcACS プロジェクトを右クリックし、**[Identity and Access]** をクリックします。

	**[Identity and Access]** オプションがコンテキスト メニューに表示されない場合、Identity and Access Tool をインストールします。詳細については、[Identity and Access Tool のダウンロード ページ]を参照してください。

	![][4]

2.	**[プロバイダー]** タブで、**[Azure のアクセス制御サービスを使用する]** を選択します。

    ![][44]

3.  **[構成]** リンクをクリックします。

    ![][444]

	Visual Studio により、Access Control 名前空間に関する情報が要求されます。前の手順で作成した名前空間名を入力します (上の画像では Test ですが、実際には別の名前空間です)。Azure の管理ポータルに戻り、対称キーを取得します。

	![][17]

4.  Azure の管理ポータルで、Access Control 名前空間をクリックし、**[管理]** をクリックします。

	![][8]

5.	**[管理サービス]** をクリックし、**[管理クライアント]** をクリックします。

	![][18]

6.	**[対称キー]** をクリックし、**[キーの表示]** をクリックして、キーの値をコピーします。次に、**[キャンセル]** をクリックし、変更を加えずに管理クライアントの編集ページを終了します。

	![][19]

7.Visual Studio で、**[名前空間の管理キーを入力]** フィールドにキーを貼り付け、**[管理キーの保存]** をクリックして **[OK]** をクリックします。

	![][20]

	Visual Studio は、名前空間に関する情報を使用して ACS 管理ポータルに接続し、ID プロバイダー、領域、戻り先 URL など、名前空間の設定を取得します。

8.	**[Windows Live ID]** (Microsoft アカウント) を選択して [OK] をクリックします。

	![][5]

<h2><span class="short-header">ACS との統合をテストする</span>ACS との統合をテストする</h2>

このタスクでは、RP アプリケーションと ACS の統合をテストする方法について説明します。

-	Visual Studio で F5 キーを押してアプリケーションを実行します。

アプリケーションが ACS と統合されており、Windows Live ID (Microsoft アカウント) を選択した場合、既定の ASP.NET Web フォーム アプリケーションを開く代わりに、ブラウザーが Microsoft アカウントのサインイン ページにリダイレクトされます。有効なユーザー名とパスワードでサインインすると、MvcACS アプリケーションにリダイレクトされます。

![][6]

ご利用ありがとうございます。ACS と ASP.NET Web アプリケーションを正常に統合できました。ACS が Microsoft アカウントの資格情報を使用してユーザーの認証を処理するようになっています。

<h2><a name="bkmk_viewClaims"></a>ACS により送信されたクレームを参照する</h2>

このセクションでは、ACS によって送信されたクレームが参照されるようにアプリケーションを変更します。Identity and Access Tool により、IP からアプリケーションへのすべてのクレームを通過する規則グループが作成されています。ID プロバイダーが異なると送信されるクレームが異なる点に注意してください。

1. *Controllers\HomeController.cs* ファイルを開きます。**System.Threading** の **using** ステートメントを追加します。

 	using System.Threading;

1. HomeController クラスで、*Claims* メソッドを追加します。

    public ActionResult Claims()
    {
        ViewBag.Message = "Your claims page.";

        ViewBag.ClaimsIdentity = Thread.CurrentPrincipal.Identity;

        return View();
    }

1. * Claims* メソッドを右クリックし、**[ビューの追加]** を選択します。

![][66]

1. **[追加]** をクリックします。

1. *Views\Home\Claims.cshtml* ファイルの内容を次のコードに置き換えます。

        @{
            ViewBag.Title = "Claims";
        }
        <hgroup class="title">
            <h1>@ViewBag.Title.</h1>
            <h2>@ViewBag.Message</h2>
        </hgroup>
        <h3>Values from Identity</h3>
        <table>
            <tr>
                <td>
                    IsAuthenticated: 
                </td>
                <td>
                    @ViewBag.ClaimsIdentity.IsAuthenticated 
                </td>
            </tr>
            <tr>
                <td>
                    Name: 
                </td>        
                <td>
                    @ViewBag.ClaimsIdentity.Name
                </td>        
            </tr>
        </table>
        <h3>Claims from ClaimsIdentity</h3>
        <table>
            <tr>
                <th>
                    Claim Type
                </th>
                <th>
                    Claim Value
                </th>
            </tr>
                @foreach (System.Security.Claims.Claim claim in ViewBag.ClaimsIdentity.Claims ) {
            <tr>
                <td>
                    @claim.Type
                </td>
                <td>
                    @claim.Value
                </td>
            </tr>
        }
        </table>

1. アプリケーションを実行し、*Claims* メソッドに移動します。

![][666]

アプリケーションでクレームの使い方の詳細については、「[Windows Identity Foundation library (Windows Identity Foundation ライブラリ)](http://msdn.microsoft.com/ja-jp/library/hh377151.aspx)」を参照してください。

<h2><a name="bkmk_VP"></a>ACS 管理ポータルでアプリケーションを参照する</h2>

Visual Studio の Identity and Access Tool は、アプリケーションと ACS と自動的に統合します。

[Azure の Access Control を使用する] オプションを選択してアプリケーションを実行すると、Identity and Access Tool によりアプリケーションが証明書利用者として追加されて、選択された ID プロバイダーを使用するように構成され、アプリケーションの既定のクレーム変換規則が生成および選択されます。

これらの構成設定は、ACS 管理ポータルで確認して変更できます。ポータルで変更を確認するには、次のステップを使用します。

1.	[Azure の管理ポータル](http://manage.WindowsAzure.com)にログインします。

2.	**[Active Directory]** をクリックします。

	![][8]

3.	Access Control 名前空間を選択し、**[管理]** をクリックします。この操作により、ACS 管理ポータルが開きます。

	![][9]


4.	**[証明書利用者アプリケーション]** をクリックします。

	新しい MvcACS アプリケーションが、証明書利用者アプリケーションの一覧に表示されます。領域は、自動的にアプリケーション メイン ページに設定されます。

	![][10]


5.	**MvcACS** をクリックします。

	[証明書利用者アプリケーションの編集] ページには、MvcACS Web アプリケーションの構成設定が表示されます。このページの設定を変更して保存すると、変更がすぐにアプリケーションに適用されます。

	![][11]

6.	ID プロバイダーやクレーム変換規則など、MvcACS アプリケーションの他の構成設定が表示されるまで、ページを下にスクロールします。

	![][12]

次のセクションでは、ACS 管理ポータルの機能を使用して Web アプリケーションに変更を加え、どれくらい簡単に変更できるかを示します。

<h2><span class="short-header">ID プロバイダーを追加する</span>ID プロバイダーを追加する</h2>

ACS 管理ポータルを使用して、MvcACS アプリケーションの認証を変更しましょう。この例では、Google を MvcACS の ID プロバイダーとして追加します。

1.	**[ID プロバイダー]** (ナビゲーション メニュー) をクリックし、**[追加]** をクリックします。

	![][13]

2.	**Google** をクリックし、**[次へ]** をクリックします。MvcACS アプリケーションのチェック ボックスが既定でオンになっています。

	![][14]

3. [保存] をクリックします。

	![][15]


これで完了です。Visual Studio に戻ったら、MvcACS アプリケーションのプロジェクトを開いて **[Identity and Access]** をクリックすると、ツールにより Windows Live ID と Google ID プロバイダーの両方が一覧表示されます。

![][16]

また、アプリケーションを実行すると、効果を確認できます。アプリケーションで複数の ID プロバイダーがサポートされる場合、ユーザーのブラウザーにはまず ACS によりホストされているページが表示され、ユーザーは ID プロバイダーの選択を求められます。

![][7]

ユーザーが ID プロバイダーを選択したら、ブラウザーは ID プロバイダーのサインイン ページに移動します。

<h2><span class="short-header">参照トピック</span>参照トピック</h2>

ACS と統合された Web アプリケーションを作成できました。ただし、まだ始まりにすぎません。このシナリオを展開することができます。
 
たとえば、この RP に他の ID プロバイダーを追加したり、Active Directory ドメイン サービスなどのエンタープライズ ディレクトリに登録されたユーザーが Web アプリケーションにログオンできるようにしたりすることができます。

アプリケーション ビジネス ロジックで処理するためにアプリケーションに送信されるクレームを決定する規則を、名前空間に追加することもできます。

さらに ACS の機能を調べたり、他のシナリオを試してみたりする場合は、[アクセス制御サービス 2.0 に関するページ]を参照してください。


  [ACS とは]: #what-is
  [概念]: #concepts
  [前提条件]: #pre
  [ASP.NET MVC アプリケーションを作成する]: #create-web-app
  [Access Control 名前空間の作成]: #create-namespace
  [Web アプリケーションを ACS と統合する]: #Identity-Access
  [ACS との統合をテストする]: #Test-ACS
  [ACS 管理ポータルでアプリケーションを参照する]: acs-portal
  [ID プロバイダーを追加する]: #add-IP
  [参照トピック]: #whats-next
  [vcsb]: #bkmk_viewClaims
  [vpp]: #bkmk_VP

  [アクセス制御サービス 2.0 に関するページ]: http://go.microsoft.com/fwlink/?LinkID=212360
  [Identity and Access Tool のダウンロード ページ]: http://go.microsoft.com/fwlink/?LinkID=245849
  [Azure の管理ポータル]: http://manage.WindowsAzure.com

  [0]: ./media/active-directory-dotnet-how-to-use-access-control/acs-01.png
  [1]: ./media/active-directory-dotnet-how-to-use-access-control/acsCreateNamespace.png
  [2]: ./media/active-directory-dotnet-how-to-use-access-control/acsQuickCreate.png
  [3]: ./media/active-directory-dotnet-how-to-use-access-control/rzMvc.png
  [4]: ./media/active-directory-dotnet-how-to-use-access-control/rzIA.png
[44]: ./media/active-directory-dotnet-how-to-use-access-control/rzPT.png
 [444]: ./media/active-directory-dotnet-how-to-use-access-control/rzC.png
  [5]: ./media/active-directory-dotnet-how-to-use-access-control/acsIdAndAccess1.png
  [6]: ./media/active-directory-dotnet-how-to-use-access-control/acsMSFTAcct.png
  [66]: ./media/active-directory-dotnet-how-to-use-access-control/rzAv.png
  [666]: ./media/active-directory-dotnet-how-to-use-access-control/rzCl.png
  [7]: ./media/active-directory-dotnet-how-to-use-access-control/acsSignIn.png
  [8]: ./media/active-directory-dotnet-how-to-use-access-control/acsClickManage.png
  [9]: ./media/active-directory-dotnet-how-to-use-access-control/acsACSPortal.png
  [10]: ./media/active-directory-dotnet-how-to-use-access-control/acsRPPage.png
  [11]: ./media/active-directory-dotnet-how-to-use-access-control/acsEdit-RP.png
  [12]: ./media/active-directory-dotnet-how-to-use-access-control/acsEdit-RP2.png
  [13]: ./media/active-directory-dotnet-how-to-use-access-control/acsAdd-Idp.png
  [14]: ./media/active-directory-dotnet-how-to-use-access-control/acsAdd-Google.png
  [15]: ./media/active-directory-dotnet-how-to-use-access-control/acsSave-Google.png
  [16]: ./media/active-directory-dotnet-how-to-use-access-control/acsIdAndA-after.png
  [17]: ./media/active-directory-dotnet-how-to-use-access-control/acsConfigAcsNamespace.png
  [18]: ./media/active-directory-dotnet-how-to-use-access-control/acsManagementService.png
  [19]: ./media/active-directory-dotnet-how-to-use-access-control/acsShowKey.png
  [20]: ./media/active-directory-dotnet-how-to-use-access-control/acsConfigAcsNamespace2.png


