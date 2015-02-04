<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Integrating Multi-Tenant Cloud Applications with Azure Active Directory" authors="terrylan" solutions="" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="terrylan" />

# マルチテナント クラウド アプリケーションと Azure Active Directory の統合

## <a name="introduction"></a> はじめに

Azure の Active Directory (Azure AD) は、クラウド アプリケーションに ID 管理機能およびアクセス制御機能を提供する最新の REST ベースのサービスです。Azure AD は Azure、Microsoft Office 365、Dynamics CRM Online、Windows Intune だけでなく、クラウド サービスとも簡単に統合されます。既存の内部設置型 Active Directory の展開でも Azure AD を最大限に活用できます。詳細については、windowsazure.com の [ID に関するページ][ID に関するページ]を参照してください。

このチュートリアルは、マルチテナント アプリケーションを Azure AD と統合したいと考えている .NET 開発者を対象としています。学習内容:

-   Azure AD を使用して顧客によるアプリケーションへのサインアップを有効にする
-   Azure AD を使用してシングル サインオン (SSO) を有効にする
-   Azure AD Graph API を使用して顧客のディレクトリ データを照会する

このチュートリアルで使用するサンプル アプリケーションは[こちらからダウンロード][こちらからダウンロード]できます。サンプルは変更せずに実行できますが、https を使用するには [Visual Studio でのポート割り当て][Visual Studio でのポート割り当て]の変更が必要になる場合があります。このリンクの先の手順に従いますが、ApplicationHost.config ファイルの bindings セクションでバインド プロトコルを "https" に設定してください。以降に示している手順のすべてのコードはサンプルからの抜粋です。

> [WACOM.NOTE]
> マルチテナント ディレクトリ アプリケーションのサンプルは例示のみを目的として提供されています。このサンプルを (そのヘルパー ライブラリ クラスも含め) 運用環境には使用しないでください。

### 前提条件

開発者には次の前提条件がこのチュートリアルで必要になります。

-   [Visual Studio 2012][Visual Studio 2012]
-   [WCF Data Services for OData][WCF Data Services for OData]

### 目次

-   [はじめに][はじめに]
-   [パート 1: Azure AD へのアクセス用のクライアント ID を取得する][パート 1: Azure AD へのアクセス用のクライアント ID を取得する]
-   [パート 2: Azure AD を使用して顧客によるサインアップを有効にする][パート 2: Azure AD を使用して顧客によるサインアップを有効にする]
-   [パート 3: シングル サインオンを有効にする][パート 3: シングル サインオンを有効にする]
-   [パート 4: Azure AD Graph にアクセスする][パート 4: Azure AD Graph にアクセスする]
-   [パート 5: アプリケーションを発行する][パート 5: アプリケーションを発行する]
-   [まとめ][まとめ]

## <a name="getclientid"></a>パート 1: Azure AD へのアクセス用のクライアント ID を取得する
このセクションでは、Microsoft 販売者ダッシュボード アカウントを作成した後にクライアント ID とクライアント シークレットを取得する方法について説明します。クライアント ID はアプリケーションの一意の識別子です。クライアント シークレットはその対応するキーであり、クライアント ID を使用して要求を行うときに必要になります。いずれもアプリケーションを Azure AD と統合するために必要です。
### 手順 1. Microsoft 販売者ダッシュボードでアカウントを作成する
Azure AD と統合するアプリケーションを開発して発行するには、[Microsoft 販売者ダッシュボード][Microsoft 販売者ダッシュボード] アカウントにサインアップする必要があります。その後、会社または個人として[アカウントのプロファイルを作成する][アカウントのプロファイルを作成する]ように求められます。このプロファイルは、Azure Marketplace などのマーケットプレースにアプリケーションを発行するために使用され、クライアント ID とクライアント シークレットを生成するために必要になります。

新しいアカウントは [承認の保留] 状態になります。この状態でも、開発を開始することは可能で、クライアント ID とドラフト アプリケーションの内容を作成することができます。ただし、アプリケーションの内容を承認のために送信できるのは、アカウント自体が承認されてからのみです。送信したアプリケーションの内容が Azure Marketplace で顧客に表示されるのは、その内容が承認されてからのみです。
### 手順 2. アプリケーションのクライアント ID を取得する
アプリケーションを Azure AD と統合するには、クライアント ID とクライアント シークレットが必要です。クライアント ID はアプリケーションの一意の識別子であり、主にシングル サインオン時や、Azure AD Graph 呼び出し認証時に、アプリケーションを識別するために使用されます。クライアント ID とクライアント シークレットの取得の詳細については、「[Microsoft 販売者ダッシュボードでのクライアント ID とシークレットの作成][Microsoft 販売者ダッシュボードでのクライアント ID とシークレットの作成]」を参照してください。

> [WACOM.NOTE]
> クライアント ID とクライアント シークレットはこのチュートリアルの後半で必要になるため、必ず記録しておいてください。

クライアント ID とクライアント シークレットを生成するには、Microsoft 販売者ダッシュボードで次のプロパティを入力する必要があります。

**アプリケーション ドメイン**: アプリケーションのホスト名 ("contoso.com" など)。このプロパティにポート番号を含めないでください。開発中、このプロパティは「localhost」に設定する必要があります。

**アプリケーションのリダイレクト URL**: 組織がアプリケーションを承認した場合に、ユーザーのサインイン後に Azure AD が応答を送信する先のリダイレクト URL ("https://contoso.com/" など)。開発中、このプロパティは "https://localhost:&#60;port number&#62;" に設定する必要があります。

### 手順 3. クライアント ID とクライアント シークレットを使用するようにアプリケーションを構成する

このステップには、販売者ダッシュボードでのサインアップ時に生成されたクライアント ID とクライアント シークレットが必要です。クライアント ID は SSO に使用されます。クライアント ID とクライアント シークレットの両方は Azure AD Graph API 呼び出し用のアクセス トークンを取得するために後で使用されます。

サンプル アプリケーションは、Azure AD を使用するために事前に接続され、config からクライアント ID とクライアント シークレットを読み込みます。サンプル アプリケーションの **Web.config** ファイルで、次のように変更します。

1.  **appSettings** ノードでは、"clientId"、"SymmetricKey" の値をクライアント ID、クライアント シークレット、アプリケーション ドメインに置き換えます。

        <appSettings>
            <add key="clientId" value="(Your Client ID value)"/>
            <add key="SymmetricKey" value="(Your Client Secret value)"/>
            <add key="AppHostname" value="(Your App Domain)"/>
        </appSettings>

2.  **system.IdentityModel** の **audienceUris** ノードでは、"spn:" の後ろにクライアント ID を挿入します。

        <system.identityModel>
            <audienceUris>
                <add value="spn:(Your Client ID value)" />
            </audienceUris>

## <a name="enablesignup"></a>パート 2: Azure AD を使用して顧客によるサインアップを有効にする

このセクションでは、Azure AD を使用して顧客によるサインアップを有効にする方法について説明します。顧客が Azure AD と統合されたアプリケーションを使用する前に、テナント管理者はアプリケーションの承認プロセスを実行する必要があります。このプロセスはアプリケーションから Azure AD への同意要求から始まります。Azure AD がその要求への応答を生成し、アプリケーションがその応答を処理する必要があります。次に示している手順では、同意要求の生成と応答の処理の方法について説明します。

このセクションの手順では、サンプル アプリケーションのヘルパー クラスを使用します。これらのヘルパー クラスはサンプルの *Microsoft.IdentityModel.WAAD.Preview* ライブラリに含まれています。それらのクラスにより、アプリケーション コードではなく ID と プロトコルの仕様に注力しやすくなります。

### 手順 1. アプリケーションに対する同意を要求する

次の一連の操作は、アプリケーションに対する同意を要求するプロセスの例です。

1.  顧客がアプリケーションの Web ページで [Azure AD を使用してサインアップ] リンクをクリックします。
2.  要求に付加されたアプリケーションの情報を使用して、Azure AD の承認ページに顧客をリダイレクトします。
3.  顧客はアプリケーションに対する同意を提示または拒否します。
4.  Azure AD によって顧客は指定されたアプリケーションのリダイレクト URL にリダイレクトされます。この URL は、Microsoft 販売者ダッシュボードでクライアント ID とクライアント シークレットを生成したときに指定したものです。リダイレクト要求は、同意が提示された場合のテナントの情報など、同意要求の結果を示しています。

上記の手順 2. のリダイレクト要求を生成するには、Azure AD の承認ページの URL (*<http://activedirectory.windowsazure.com/Consent/AuthorizeApplication.aspx>*) に querystring パラメーターを追加する必要があります。

querystring パラメーターについて次に説明します。

**ApplicationID**: (必須) 販売者ダッシュボードで取得した **ClientID** 値。

**RequestedPermissions**: (省略可能) テナントによってアプリケーションに付与する必要があるアクセス許可。
開発中、これらのアクセス許可は未発行のアプリケーションをテストするために使用されます。発行済みのアプリケーションでは、このパラメーターは無視されます。代わりに、アプリケーションの内容で要求されているアクセス許可が使用されます。アプリケーションの内容の詳細については、パート 5 を参照してください。
このパラメーターには次の 3 つの値を指定できます。

**DirectoryReader**: ユーザー アカウント、グループ、組織の情報などのディレクトリ データを読み取るためのアクセス許可を与えます。SSO を有効にします。

**UserAccountAdministrator**: ユーザー、グループ、組織の情報などのデータを読み書きするためのアクセス許可を与えます。SSO を有効にします。

**None**: シングル サインオンを有効にしますが、ディレクトリ データへのアクセスは無効にします。

パラメーターを指定しないか誤って指定した場合、既定値は "None" になります。

有効な同意要求の URL の例を次に示します。
*<https://activedirectory.windowsazure.com/Consent/AuthorizeApplication.aspx?ApplicationId=33E48BD5-1C3E-4862-BA79-1C0D2B51FB26&RequestedPermissions=DirectoryReader>*

サンプル アプリケーションでは、[Register] リンクには同意要求の URL が含まれています。次のようになっています。

![login][login]

> [WACOM.NOTE]
> 未発行のアプリケーションをテストするときは、顧客と同様の同意プロセスを実行することになります。ただし、未発行のアプリケーションの認証ページと発行済みのアプリケーションの認証ページとでは、表示内容が異なります。発行済みのアプリケーションではアプリケーションの名前、ロゴ、発行者の詳細が表示されますが、未発行のアプリケーションでは表示されません。

### 手順 2. 同意応答を処理する

顧客がアプリケーションに対する同意を提示または拒否した後、Azure AD はアプリケーションのリダイレクト URL に応答を送信します。この応答には次の querystring パラメーターが含まれています。

**TenantId**: アプリケーションを承認した Azure AD テナントの一意の GUID。このパラメーターが指定されるのは、顧客がアプリケーションを承認した場合のみです。

**Consent**: アプリケーションが承認された場合は値が "Granted" に設定され、要求が却下された場合は "Denied" に設定されます。

アプリケーションが承認されたことを表す、同意要求への有効な応答の例を次に示します。
*<https://app.litware.com/redirect.aspx&TenantId=7F3CE253-66DB-4AEF-980A-D8312D76FDC2&Consent=Granted>*

アプリケーションでは、Azure AD の承認ページに送信された要求が応答に関連付けられるように、コンテキストを維持する必要があります (要求が関連付けられていない応答はすべて却下されます)。

<div class="dev-callout"><strong>注</strong><p>同意が提示された後、Azure AD では SSO と Graph のプロビジョニングが完了するまで時間がかかる場合があります。各組織でアプリケーションにサインアップした最初のユーザーには、プロビジョニングが完了するまでサインイン エラーが表示される場合があります。</p></div>

顧客がアプリケーションに対する同意を提示した後、重要なのは、同意応答によって返される TenantId を使用して、新たに作成したテナントをアプリケーションで関連付けて保存することです。サンプル アプリケーションでは、*Microsoft.IdentityModel.WAAD.Preview.Consent* 名前空間にある *HttpModule* が、すべての同意応答によって返される TenantId を customer/TenantId データ ストアに自動的に記録します。次に示しているコードと、customer/TenantId データ ストアへの TenantId の記録は、*TrustedIssuers.Add* メソッドが実行します。

    private void Application_BeginRequest(Object source,
             EventArgs e)
    {
        HttpApplication application = (HttpApplication)source;
        HttpRequest req = application.Context.Request;             

        if((!string.IsNullOrEmpty(req.QueryString["TenantId"]) && (!string.IsNullOrEmpty(req.QueryString["Consent"]))))
        { 
            if(req.QueryString["Consent"].Equals("Granted",StringComparison.InvariantCultureIgnoreCase))
            {
                // For this sample we store the consenting tenants in
                // an XML file. We strongly recommend that you change
                // this to use your DataStore
                TrustedIssuers.Add(req.QueryString["TenantId"]; 
            }
        }            
    }

アプリケーションに対する同意要求/応答のコードをテストする前に、Azure AD ディレクトリ テナントを取得する必要があります。

### 手順 3. アプリケーションのテストに必要な Azure AD テナントを取得する

アプリケーションが Azure AD と統合できたことをテストするには、Azure AD テナントが必要です。別のアプリケーションのテストに使用しているテナントが既にある場合は、そのテナントを再利用できます。アプリケーションを複数のテナントでテストおよび使用できるように、2 つ以上のテナントを取得することをお勧めします。この目的に運用環境のテナントを使用することはお勧めしません。別の [Azure AD テナントを取得][Azure AD テナントを取得]してください。

Azure AD テナントを取得したら、**F5** キーを押してアプリケーションをビルドして実行できます。さらに、新しいテナントを使用してアプリケーションへのサインアップを試すことができます。

<div class="dev-callout"><strong>注</strong><p>顧客が新しい Azure AD テナントにサインアップする場合、そのテナントのプロビジョニングが完了するまで時間がかかることがあります。プロビジョニングが完了するまで、ユーザーには同意ページでエラーが表示される場合があります。</p></div>

## <a name="enablesso"></a>パート 3: シングル サインオンを有効にする

このセクションでは、シングル サインオン (SSO) を有効にする方法について説明します。このプロセスではまず、アプリケーションにユーザーを認証するために、Azure AD へのサインイン要求を生成します。次に、アプリケーションを承認したテナントに顧客が属していることを、サインイン応答で確認します。サインイン要求には、販売者ダッシュボードで生成したクライアント ID と顧客組織のテナント ID が必要です。

サインイン要求はディレクトリ テナントに固有のものであり、TenantID が含まれている必要があります。TenantID は Azure AD ディレクトリ テナントのドメイン名から調べることができます。サインイン時にエンド ユーザーからこのドメイン名を取得するには、一般的に次の 2 つの方法があります。

-   アプリケーションの URL が *<https://contoso.myapp.com>* または *<https://myapp.com/contoso.com>* の場合、*contoso* と *contoso.com* は Azure AD のドメイン名を表し、*myapp.com* はアプリケーションの URL を表します。
-   アプリケーションで、ユーザーに自分の電子メール アドレスまたは Azure AD ドメイン名の入力を求めることができます。このアプローチは、サンプル アプリケーションで使用して、ユーザーに Azure AD ドメイン名の入力を求めます。次のようになっています。

![login][login]

### 手順 1. テナント ID を調べる

顧客が入力した Azure AD ドメイン名を使用して、Azure AD フェデレーション メタデータを解析することで、そのテナント ID を調べることができます。サンプル アプリケーションでは、このプロセスは \* *Microsoft.IdentityModel.WAAD.Preview.TenantUtils.Globals* クラスの *Domain2TenantId* メソッドによって処理されます。

このプロセスを実際に試すには、次に示している手順で contoso.com ドメイン名を使用します。

1.  Azure AD テナントの **FederationMetadata.xml** ファイルを取得します。次に例を示します。
    *<https://accounts.accesscontrol.windows.net/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml>*
2.  **FederationMetadata.xml** ファイルで、**Entity Descriptor** エントリを見つけます。テナント ID は、以下のように **entityID** プロパティの一部として、"<https://sts.windows.net>" の後ろにあります。

         <EntityDescriptor xmlns="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://sts.windows.net/a7456b11-6fe2-4e5b-bc83-67508c201e4b/" ID="_cba45203-f8f4-4fc3-a3bb-0b136a2bafa5"> 

    この場合、TenantID 値は **a7456b11-6fe2-4e5b-bc83-67508c201e4b** です。

3.  アプリケーションの customer/TenantId データ ストアに、ドメインとその対応する TenantID を保存する必要があります。これら 2 つの値を以降のサインイン要求に一緒に使用することで、**FederationMetadata.xml** を毎回取得する必要がなくなります。サンプル アプリケーションでは、この最適化を行っていません。

### 手順 2. サインイン要求を生成する

顧客がサインイン用のボタンをクリックするなどしてアプリケーションにサインインすると、サインイン要求が顧客のテナント ID とアプリケーションのクライアント ID を使用して生成される必要があります。サンプル アプリケーションでは、この要求は *Microsoft.IdentityModel.WAAD.Preview.WebSSO.URLUtils* クラスの *GenerateSignInMessage* メソッドによって生成されます。このメソッドは、アプリケーションを承認した組織を顧客の TenantID が表すことを確認し、サインイン ボタンのリンク先 URL を生成します。次のようになっています。

![login][login]

このボタンをクリックすると、ユーザーのブラウザーで Azure AD のサインイン ページが表示されます。顧客がサインインしたら、Azure AD はサインイン応答をアプリケーションに返します。

### 手順 3. サインイン応答内の受信トークンの発行元を検証する

顧客がアプリケーションにサインインすると、顧客のテナントによってアプリケーションが承認されていることを検証する必要があります。サインイン応答にはトークンが含まれています。トークンにはアプリケーションで検査できるプロパティとクレームが含まれています。

この検証を実行するには、トークン内の Issuer プロパティから TenantID を取得し、その ID が customer/TenantId データ ストアに存在することを確認する必要があります。サンプル アプリケーションでは、この検証を行うために、Windows Identity Foundation の *Saml2SecurityTokenHandler* を継承するカスタム トークン ハンドラー クラスを作成しています。カスタム トークン ハンドラーは、受信セキュリティ トークンを確認し、そのクレームとプロパティをアプリケーションから使用できるようにすることで、TenantID を検証できるようにしています。このクラスのコードの抜粋を次に示します。

サンプル アプリケーションでは、元のコードは *Microsoft.IdentityModel.WAAD.Preview.WebSSO* 名前空間にあります。トークン ハンドラーは、*Microsoft.IdentityModel.WAAD.Preview.WebSSO.TrustedIssuers* クラスの Contains メソッドも使用します。このメソッドは、TenantID が customer/TenantId データ ストアに保存されていることを確認します。

    /// <summary>
    /// Extends the out of the box SAML2 token handler by ensuring
    /// that incoming tokens have been issued by registered tenants 
    /// </summary>
    public class ConfigurationBasedSaml2SecurityTokenHandler : Saml2SecurityTokenHandler
    {
        public override ReadOnlyCollection<System.Security.Claims.ClaimsIdentity> ValidateToken(SecurityToken token)
        {
            ReadOnlyCollection<System.Security.Claims.ClaimsIdentity> aa = base.ValidateToken(token);
            Saml2SecurityToken ss = token as Saml2SecurityToken;
            string tenant = ss.Assertion.Issuer.Value.Split('/')[3];
            if (!TrustedIssuers.Contains(tenant))
            {
                throw new SecurityTokenValidationException(string.Format("The tenant {0} is not registered with the application", tenant));
            }
            return aa;
        }
    }

トークンが検証されたら、ユーザーはアプリケーションにサインインされています。アプリケーションを実行し、以前に作成した同意済みのテナントで Azure AD アカウントを使用してサインインを試します。

## <a name="accessgraph"></a>パート 4: Azure AD Graph にアクセスする

このセクションでは、アクセス トークンを取得し、Azure AD Graph API を呼び出してテナントのディレクトリ データにアクセスする方法について説明します。たとえば、サインイン中に取得されたトークンには、名前や電子メール アドレスなどのユーザー情報が含まれていますが、アプリケーションでは、グループ メンバーシップやユーザーのマネージャーの名前などの情報が必要な場合があります。このような情報は、Graph API を使用してテナントのディレクトリから取得できます。Graph API の詳細については、[こちらのトピック][こちらのトピック]を参照してください。

アプリケーションから Azure AD Graph を呼び出すには、その前にアプリケーションを認証し、アクセス トークンを取得する必要があります。アクセス トークンは、そのクライアント ID とクライアント シークレットを使用してアプリケーションを認証することで取得します。ここでの手順では、次の操作を実行する方法を示します。

1.  生成されたプロキシ クラスを使用して Azure AD Graph を呼び出す
2.  Azure Authentication Library (AAL) を使用してアクセス トークンを取得する
3.  Azure AD Graph を呼び出してテナント ユーザーのリストを取得する

<div class="dev-callout"><strong>注</strong><p>サンプル アプリケーションのヘルパー ライブラリである Microsoft.IdentityModel.WAAD.Preview には、自動生成されたプロキシ クラス (https://graph.windows.net/your-domain-name へのサービス参照を追加することによって作成された GraphService というクラス) が既に含まれています。アプリケーションはこのプロキシ クラスを使用して Azure AD Graph サービスを呼び出します。</p></div>

### 手順 1. プロキシ クラスを使用して Azure AD Graph を呼び出す

この手順では、サンプル アプリケーションを使用して、次の操作を実行する方法を示します。

1.  テナント固有の Azure AD Graph エンドポイントを作成する
2.  エンドポイントを使用し、プロキシをインスタンス化して、Graph を呼び出す
3.  Authorization ヘッダーを要求に追加し、トークンを取得する

サンプル アプリケーションでは、次のコードに示すように、API へのこれらの呼び出しは *Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface* クラスの GraphInterface メソッドによって処理されます。

    public GraphInterface()
    {
        // 1a: When the customer was signed in, we get a security token 
        // that contains a tenant id. Extract that here
        TenantDomainName = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/ws/2012/10/identity/claims/tenantid").Value;

        // 1b: We generate a URL (https://graph.windows.net/<CustomerDomainName>)
        // to access the Azure AD Graph API endpoint for the tenant 
        connectionUri = new Uri(string.Format(@"https://{0}/{1}", TenantUtils.Globals.endpoint, TenantDomainName));

        // 2: create an instance of the AzureAD Service proxy with the connection URL
        dataService = new DirectoryDataService(connectionUri);

        // This flags ignores the resource not found exception
        // If AzureAD Service throws this exception, it returns null
        dataService.IgnoreResourceNotFoundException = true;
        dataService.MergeOption = MergeOption.OverwriteChanges;
        dataService.AddAndUpdateResponsePreference = DataServiceResponsePreference.IncludeContent;

        // 3: This adds the default required headers to each request
        AddHeaders(GetAuthorizationHeader());
    }

### 手順 2. Azure Authentication Library を使用してアクセス トークンを取得する

サンプル アプリケーションでは、Azure Authentication Library (AAL) を使用して、Graph API へのアクセス用のトークンを取得しています。トークンの取得プロセスは、*Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface* クラスの *GetAuthorizationHeader* メソッドによって管理され、次のようになっています。

<div class="dev-callout"><strong>注</strong><p>AAL は NuGet パッケージとして入手可能で、Visual Studio にインストールできます。</p></div>

    /// <summary>
    /// Method to get the Oauth2 Authorization header from ACS
    /// </summary>
    /// <returns>AOauth2 Authorization header</returns>
    private string GetAuthorizationHeader()
    {
        // AAL values
        string fullTenantName = TenantUtils.Globals.StsUrl + TenantDomainName;
        string serviceRealm = string.Format("{0}/{1}@{2}", TenantUtils.Globals.GraphServicePrincipalId, TenantUtils.Globals.GraphServiceHost, TenantDomainName);
        string issuingResource = string.Format("{0}@{1}", Globals.ClientId, TenantDomainName);
        string clientResource = string.Format("{0}/{1}@{2}", Globals.ClientId, Globals.AppHostname, TenantDomainName);

        string authzHeader = null;
        AuthenticationContext _authContext = new AuthenticationContext(fullTenantName);

        try
        {
            ClientCredential credential = new ClientCredential(issuingResource, clientResource, Globals.ServicePrincipalKey);
            AssertionCredential _assertionCredential = _authContext.AcquireToken(serviceRealm, credential);
            authzHeader = _assertionCredential.CreateAuthorizationHeader();
        }
        catch (Exception ex)
        {
            AALException aex = ex as AALException;
            string a = aex.InnerException.Message;
        }

        return authzHeader;
    }

次の情報を使用してアクセス トークンを取得しています。前のコードで示しているようになっています。

1.  アプリケーションの情報 (ClientID、ServicePrincipalKey、AppHostname)
2.  ターゲットの情報 (Graph。前のコードでは ServiceRealm として参照)
3.  前に取得した TenantDomainName

### 手順 3. Azure AD Graph を呼び出してユーザーのリストを取得する

*Microsoft.IdentityModel.WAAD.Preview.Graph.GraphInterface* クラスの次のメソッドは、前に生成された *DataService* プロキシを使用して、テナントのすべてのユーザーのリストを取得します。

    public List<User> GetUsers()
    {
        // Add the page size using top
        var users = dataService.Users.AddQueryOption("$top", 20);

        // Execute the Query
        var userQuery = users.Execute();

        // Get the return users list
        return userQuery.ToList();
    }

このメソッドは、**HomeController.cs** ファイルから呼び出して、Web アプリケーションの [Users] タブでユーザー リストを表示するために使用します。

## <a name="publish"></a>パート 5:アプリケーションの発行

アプリケーションを徹底的にテストしたら、アプリケーションの内容を作成して Azure Marketplace に発行できます。これらの手順は Microsoft 販売者ダッシュボードで実行します。

<div class="dev-callout"><strong>注</strong><p>顧客に発生する課金はすべてアプリケーションで管理する必要があります。Azure Marketplace では、アプリケーションの Web サイトとその関連情報へのリンクのみが提供されます。</p></div>

### 手順 1. アプリケーション マニフェストとアプリケーションの内容を作成する

アプリケーションの内容を作成する前に、アプリケーションの製品版の新しいクライアント ID とクライアント シークレットを生成する必要があります。このチュートリアルのパート 1 では、アプリケーションのテスト版のクライアント ID とクライアント シークレットを生成しました。それらの手順を繰り返し、新しい値でアプリケーションを構成して、運用環境のアプリケーション ドメインとアプリケーション リダイレクト URL を設定します。

次に、アプリケーション マニフェストを作成して、顧客の同意を求めるためにアプリケーションに必要になるアクセス許可をマニフェストで示す必要があります。このマニフェストは、XSD ファイルによって制御される XML 形式で記述されます。マニフェストは、作成するアプリケーションの内容の一部としてアップロードする必要があります。

チュートリアルのパート 1 で説明しているように、次の 3 つ異なるアクセス許可レベルがあります。

**DirectoryReader**: ユーザー アカウント、グループ、組織の情報などのディレクトリ データを読み取るためのアクセス許可を与えます。SSO を有効にします。

**UserAccountAdministrator**: ユーザー、グループ、組織の情報などのデータを読み書きするためのアクセス許可を与えます。SSO を有効にします。

**None**: シングル サインオンを有効にしますが、ディレクトリ データへのアクセスは無効にします。

アプリケーション マニフェストの 2 つの例を次に示します。1 つ目は、SSO 専用のアプリケーションに対するアクセス許可を、2 つ目は、読み取り専用のアプリケーションに対するアクセス許可を示しています。

    <?xml version="1.0" encoding="utf-16"?>
    <AppRequiredPermissions>
      <AppPermissionRequests Policy="AppOnly">
        <AppPermissionRequest Right="None" Scope="http://directory" />
      </AppPermissionRequests>
    </AppRequiredPermissions>


    <?xml version="1.0" encoding="utf-16"?>
    <AppRequiredPermissions>
      <AppPermissionRequests Policy="AppOnly">
        <AppPermissionRequest Right="Directory Reader" Scope="http://directory">
          <Reason culture="ja-jp" value="Needs to read the app"/>
        </AppPermissionRequest>
      </AppPermissionRequests>
    </AppRequiredPermissions>

前に示している例の *Policy* 属性では、要求されているアプリケーション アクセス許可の種類を記述しています。現在、アクセス許可属性としては "AppOnly" のみがサポートされています。このアクセス許可の種類は、そのアプリケーションのみがディレクトリにアクセスすることを示しています。

場合によっては *Reason* 要素を使用して、必要なアクセス許可レベルについて理由を (複数のカルチャで) 指定することもできます。このテキストは同意ページに表示されて、顧客がアプリケーションを承認または却下するときに役立ちます。

新しいクライアント ID とアプリケーション マニフェストを使用し、[Microsoft 販売者ダッシュボードのアプリケーションの追加][Microsoft 販売者ダッシュボードのアプリケーションの追加] での指示に従って、アプリケーションの内容を作成できます。アプリケーションの内容を作成するときは、種類として Azure AD アプリケーションを選択してください。アプリケーションの内容の作成が完了したら、[送信] をクリックしてアプリケーションを Azure Marketplace に発行します。アプリケーションの承認と発行が完了するまで待つ必要があります。

<div class="dev-callout"><strong>注</strong><p>&quot;税金と支払いの情報を追加&quot; するように求められた場合、この手順はスキップできます。Microsoft を通じてではなく顧客に直接アプリケーションを販売するためです。</p></div>

### 手順 2. テストを完了してアプリケーションを発行する

アプリケーションの内容が承認されると、エンド ツー エンドでもう一度アプリケーションをテストする必要があります。たとえば、アプリケーションが運用環境の ClientID とクライアント シークレットで更新されていることを確認してください。最終的にテストのチェック リストを通しで実行して、アプリケーションの内容に含まれる情報が同意ページに表示されることを確認します。

## <a name="summary"></a>まとめ

このチュートリアルでは、マルチテナント アプリケーションを Azure AD と統合する方法について説明しました。このプロセスは次の 3 つの手順で構成されました。

-   Azure AD を使用して顧客によるアプリケーションへのサインアップを有効にする
-   Azure AD を使用してシングル サインオン (SSO) を有効にする
-   Azure AD Graph API を使用して顧客のディレクトリ データを照会する

アプリケーションを Azure AD と統合することで、顧客は保守済みの ID 管理システムを使用して、そのアプリケーションにサインアップしてサインインできるようになります。その結果、アプリケーションで個別の ID 管理タスクを実行する必要がなくなります。この機能により、アプリケーション使用時のよりシームレスなエクスペリエンスを顧客に与えるだけでなく、管理タスクの実行にかかっていた時間を解放することもできます。

  [ID に関するページ]: http://www.windowsazure.com/ja-jp/home/features/identity/
  [こちらからダウンロード]: http://go.microsoft.com/fwlink/?LinkId=271213
  [Visual Studio でのポート割り当て]: http://msdn.microsoft.com/ja-jp/library/ms178109(v=vs.100).aspx
  [Visual Studio 2012]: http://www.microsoft.com/visualstudio/eng/downloads
  [WCF Data Services for OData]: http://www.microsoft.com/download/en/details.aspx?id=29306
  [はじめに]: #introduction
  [パート 1: Azure AD へのアクセス用のクライアント ID を取得する]: #getclientid
  [パート 2: Azure AD を使用して顧客によるサインアップを有効にする]: #enablesignup
  [パート 3: シングル サインオンを有効にする]: #enablesso
  [パート 4: Azure AD Graph にアクセスする]: #accessgraph
  [パート 5: アプリケーションを発行する]: #publish
  [まとめ]: #summary
  [Microsoft 販売者ダッシュボード]: https://sellerdashboard.microsoft.com/
  [アカウントのプロファイルを作成する]: http://msdn.microsoft.com/ja-jp/library/jj552460.aspx
  [Microsoft 販売者ダッシュボードでのクライアント ID とシークレットの作成]: http://msdn.microsoft.com/ja-jp/library/jj552461.aspx
  [login]: ./media/active-directory-dotnet-integrate-multitent-cloud-applications/login.png
  [Azure AD テナントを取得]: http://g.microsoftonline.com/0AX00en/5
  [こちらのトピック]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh974476.aspx
  [Microsoft 販売者ダッシュボードのアプリケーションの追加]: http://msdn.microsoft.com/ja-jp/library/jj552465.aspx
