<properties urlDisplayName="Twilio Phone Call" pageTitle="Twilio から通話する方法 (.NET) - Azure" metaKeywords="Azure .NET Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, ASP.NET twilio Azure" description="Azure で Twilio API サービスを使用して通話や SMS メッセージの送信を行う方法について学習します。コード サンプルは .NET で記述されています。" metaCanonical="" services="" documentationCenter=".NET" title="Azure の Web ロールで Twilio を使用して通話する方法" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; larryf" />

# Azure の Web ロールで Twilio を使用して通話する方法

このガイドでは、Azure でホストされる Web ページから Twilio を使用して通話する方法を説明します。次のスクリーンショットに示すように、作成されたアプリケーションは通話に関する値の入力をユーザーに求めます。

![Twilio および ASP.NET を使用した Azure 通話フォーム][Twilio および ASP.NET を使用した Azure 通話フォーム]

## 目次

-   [前提条件][前提条件]
-   [方法: 通話するために Web フォームを作成する][方法: 通話するために Web フォームを作成する]
-   [方法: 通話するためにコードを作成する][方法: 通話するためにコードを作成する]
-   [次のステップ][次のステップ]
-   [関連項目][関連項目]

## <a name="twilio-prereqs"></a>前提条件

このトピックでコードを使用するためには次の操作を行う必要があります。

1.  Twilio アカウントと認証トークンを取得します。Twilio を利用するには、[][]<https://www.twilio.com/try-twilio></a> でサインアップします。[][1]<http://www.twilio.com/pricing></a> で価格を検討することができます。Twilio から提供される API の詳細については、[][2]<http://www.twilio.com/voice/api></a> を参照してください。
2.  使用する電話番号を Twilio で確認します。電話番号を確認する方法については、[][3]<https://www.twilio.com/user/account/phone-numbers/verified>\#</a> を参照してください。既存の番号を使用する代わりに、Twilio 電話番号を購入することができます。
    この例では、Twilio サンドボックス電話番号を使用して、有効な電話番号にメッセージを送信します。サンドボックス電話番号を使用するだけで、有効な電話番号に送信できます。
3.  Twilio .NET ライブラリを Web ロールに追加します。このトピックの「Twilio ライブラリを Web ロール プロジェクトに追加するには」のセクションを参照してください。

Azure での基本的な Web ロールの作成について詳しく理解している必要があります。

## <a name="howtocreateform"></a>方法: 通話用の Web フォームの作成

### <span id="use_nuget"></span></a>Twilio ライブラリを Web ロール プロジェクトに追加するには

1.  Visual Studio でソリューションを開きます。
2.  **[参照]** を右クリックします。
3.  **[NuGet パッケージの管理]** をクリックします。
4.  **[オンライン]** をクリックします。
5.  [オンライン検索] ボックスに、「*twilio*」と入力します。
6.  Twilio パッケージで **[インストール]** をクリックします。

次のコードは、通話するためのユーザー データを取得する Web フォームの作成方法を示しています。この例では、**TwilioCloud** という名前の ASP.NET Web ロールが作成されます。

    <%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
        AutoEventWireup="true" CodeBehind="Default.aspx.cs"
        Inherits="WebRole1._Default" %>

    <asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
    </asp:Content>
    <asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
        <div>
            <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
            </asp:BulletedList>
        </div>
        <div>
            <p>Fill in all fields and click <b>Make this call</b>.</p>
            <div>
                To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
                Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
                <asp:Button ID="callpage" runat="server" Text="Make this call"
                    onclick="callpage_Click" />
            </div>
        </div>
    </asp:Content>

## <span id="howtocreatecode"></span></a>方法: 通話用のコードの作成

次のコードはユーザーがフォームへの入力を完了すると呼び出され、このコードによって通話メッセージが作成され、通話が生成されます。この例では、コードは、フォームのボタンの onclick イベント ハンドラーで実行されます (次のコードは、**accountSID** および **authToken** に割り当てられたプレースホルダー値の代わりに Twilio アカウントと認証トークンを使用します)。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;
    using Twilio;

    namespace WebRole1
    {
        public partial class _Default : System.Web.UI.Page
        {
            protected void Page_Load(object sender, EventArgs e)
            {

            }

            protected void callpage_Click(object sender, EventArgs e)
            {
                // Call porcessing happens here.

                // Use your account SID and authentication token instead of
                // the placeholders shown here.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Instantiate an instance of the Twilio client.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Retrieve the account, used later to retrieve the
                Twilio.Account account = client.GetAccount();
                string APIversuion = client.ApiVersion;
                string TwilioBaseURL = client.BaseUrl;

                this.varDisplay.Items.Clear();
                if (this.toNumber.Text == "" || this.message.Text == "")
                {
                    this.varDisplay.Items.Add(
                            "You must enter a phone number and a message.");
                }
                else
                {
                    // Retrieve the values entered by the user.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Create a URL using the Twilio message and the user-entered
                    // text. You must replace spaces in the user's text with '%20'
                    // to make the text suitable for a URL.
                    String Url = "http://twimlets.com/message?Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Diplay the enpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Tilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values into a hash map.
                    // This sample uses the sandbox number provided by Twilio
                    // to make the call.
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Place the call.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

通話が行われ、Twilio エンドポイント、API バージョン、および通話の状態が表示されます。次のスクリーンショットは、実行サンプルでの出力を示しています。

![Twilio および ASP.NET を使用した Azure 通話応答][Twilio および ASP.NET を使用した Azure 通話応答]

TwiML の詳細については、[][4]<http://www.twilio.com/docs/api/twiml></a> を参照してください。\<Say\> およびその他の Twilio 動詞の詳細については、[][5]<http://www.twilio.com/docs/api/twiml/say></a> を参照してください。

## <span id="nextsteps"></span></a>次のステップ

Azure の ASP.NET Web ロールで Twilio を使用した基本機能を示すために、このコードが用意されました。運用環境で Azure に展開する前に、エラー処理やその他の機能をさらに追加することができます。次に例を示します。

-   Web フォームを使用する代わりに、Azure BLOB ストレージまたは Azure SQL データベース インスタンスを使用して、電話番号と通話テキストを保存できます。Azure での BLOB の使用に関する詳細については、「[.NET での Azure BLOB ストレージ サービスの使用方法][.NET での Azure BLOB ストレージ サービスの使用方法]」を参照してください。SQL データベースの使用の詳細については、「[.NET アプリケーションでの Azure SQL データベースの使用方法][.NET アプリケーションでの Azure SQL データベースの使用方法]」を参照してください。
-   RoleEnvironment.getConfigurationSettings を使用すると、Twilio アカウント ID と認証トークンは、その値をフォーム内にハードコーディングするのではなく、デプロイの構成設定から取得できます。RoleEnvironment クラスの詳細については、「[Microsoft.WindowsAzure.ServiceRuntime 名前空間][Microsoft.WindowsAzure.ServiceRuntime 名前空間]」を参照してください。
-   また、Twilio に関するセキュリティ ガイドライン ([][6]<https://www.twilio.com/docs/security></a>) も参照してください。
-   Twilio の詳細については、[][7]<https://www.twilio.com/docs></a> を参照してください。

## <a name="seealso"></a>関連項目

-   [Azure から音声および SMS 機能に Twilio を使用する方法][Azure から音声および SMS 機能に Twilio を使用する方法]

  [Twilio および ASP.NET を使用した Azure 通話フォーム]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
  [前提条件]: #twilio-prereqs
  [方法: 通話するために Web フォームを作成する]: #howtocreateform
  [方法: 通話するためにコードを作成する]: #howtocreatecode
  [次のステップ]: #nextsteps
  [関連項目]: #seealso
  []: http://www.twilio.com/try-twilio
  [1]: http://www.twilio.com/pricing
  [2]: http://www.twilio.com/voice/api
  [3]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Twilio および ASP.NET を使用した Azure 通話応答]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png
  [4]: http://www.twilio.com/docs/api/twiml
  [5]: http://www.twilio.com/docs/api/twiml/say
  [.NET での Azure BLOB ストレージ サービスの使用方法]: https://www.windowsazure.com/ja-jp/develop/net/how-to-guides/blob-storage/
  [.NET アプリケーションでの Azure SQL データベースの使用方法]: https://www.windowsazure.com/ja-jp/develop/net/how-to-guides/sql-database/
  [Microsoft.WindowsAzure.ServiceRuntime 名前空間]: http://msdn.microsoft.com/ja-jp/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
  [6]: http://www.twilio.com/docs/security
  [7]: http://www.twilio.com/docs
  [Azure から音声および SMS 機能に Twilio を使用する方法]: ../twilio-dotnet-how-to-use-for-voice-sms/
