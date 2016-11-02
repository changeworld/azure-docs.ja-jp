
<properties
    pageTitle="条件付きアクセス - サポートされるアプリケーション | Microsoft Azure"
    description="条件を利用してアクセスを制御する Azure Active Directory は、ユーザーの認証時、アプリケーションにアクセスを与える前に、選択された特定の条件を確認します。 条件が満たされていれば、ユーザーは承認され、アプリケーションにアクセスできます。"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="09/26/2016"
    ms.author="markvi"/>



# <a name="conditional-access-support-for-applications"></a>アプリケーションの条件付きアクセスのサポート

条件付きアクセス規則は、Azure Active Directory 接続アプリケーション、事前に統合されたフェデレーション SaaS アプリケーション、パスワード シングル サインオンを使用するアプリケーション、基幹業務アプリケーション、Azure AD アプリケーション プロキシなど、さまざまなアプリケーションに対応しています。 条件付きアクセスへの対応状況を詳しく記載したアプリケーション一覧については、「 [条件付きアクセスで有効になっているサービス](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access)」を参照してください。 条件付きアクセスは、先進認証を使用するモバイル アプリケーションおよびデスクトップ アプリケーションで動作します。 このトピックでは、それらのアプリのモバイル版とデスクトップ版に関連したサポート状況について説明しています。

先進認証を使用するアプリケーションでは、Azure AD サインイン ページを表示できます。 これによりユーザーは、アクセスがブロックされたときに、Multi-Factor Authentication に対してインラインでメッセージを表示するか、エンド ユーザーに対してメッセージを表示することができます。 最新の認証は、デバイス ベースの条件付きアクセス ポリシーが評価されるように、Azure AD でデバイスを認証できるためにも必要です。

その他のエントリ ポイントをセキュリティで保護する場合に必要な手順だけでなく、サポートされるアプリケーションを理解しておくことも重要です。

## <a name="applications-using-modern-authentication"></a>先進認証を使用するアプリケーション
次のアプリケーションは、Office 365 やその他のサービス アプリケーションが接続されている Azure AD にアクセスする場合に、条件付きアクセスをサポートします。

| 対象サービス  | プラットフォーム  | アプリケーション                                                  |
|--------------|-----------------|----------------------------------------------------------------|
|Office 365 Exchange Online | Windows 10|メール/カレンダー/People アプリ、Outlook 2016、Outlook 2013 (先進認証が有効)、Skype for Business (先進認証を使用)|
|Office 365 Exchange Online| Windows 7、Windows 8.1、 |Outlook 2016、Outlook 2013 (先進認証が有効)、Skype for Business (先進認証を使用)|
|Office 365 Exchange Online|iOS、Android|  Outlook Mobile アプリ|
|Office 365 Exchange Online|Mac OSX| MFA/場所に対する Outlook 2016 のみ。デバイスのベースのポリシーのサポートは今後に予定。Skype for Business のサポートは今後に予定|
|Office 365 SharePoint Online|Windows 10| Office 2016 アプリ、Office のユニバーサル アプリ、Office 2013 (先進認証が有効)、OneDrive for Business アプリ (NGSC または次世代同期クライアント) のサポートは今後に予定、Office のグループのサポートは今後に予定、SharePoint アプリのサポートは今後に予定|
|Office 365 SharePoint Online|Windows 7、Windows 8.1、|Office 2016 アプリ、Office 2013 (先進認証が有効)、OneDrive for Business アプリ (Groove 同期クライアント)|
|Office 365 SharePoint Online|iOS、Android|  Office モバイル アプリ |
|Office 365 SharePoint Online|Mac OSX| MFA/場所に対する Office 2016 のみ。デバイスのベースのポリシーのサポートは今後に予定|
|Office 365 Yammer|Windows 10、iOS、および Android | Office Yammer アプリ|
|Dynamics CRM|Windows 10、7、8.1、iOS、および Android | Dynamics CRM アプリ|
|PowerBI サービス|Windows 10、7、8.1、iOS、および Android | PowerBI アプリ|
|Azure Remote App サービス|Windows 10、7、8.1、iOS、および Android、Mac OSX |Azure リモート アプリ|
|任意のマイ アプリ アプリ サービス|Android および iOS|任意のマイ アプリ アプリ サービス |


## <a name="applications-that-do-not-use-modern-authentication"></a>先進認証を使用しないアプリケーション

現時点では、先進認証を使用しないアプリケーションは条件付きアクセスが適用されないため、他の方法でアクセスをブロックする必要があります。 これは、以前のアプリ バージョンが従来のプロトコルを使用して構築されている Exchange および SharePoint のアクセスでは主な考慮事項です。

## <a name="office-365-sharepoint-online"></a>Office 365 SharePoint Online

従来のプロトコルは、Set-SPOTenant コマンドレットを使用して、SharePoint で無効にできます。 このコマンドレットは、先進認証以外のプロトコルを使用している Office クライアントが SharePoint Online のリソースにアクセスできないようにします。 

**コマンドの例**:     `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`
 
## <a name="office-365-exchange-online"></a>Office 365 Exchange Online

Exchange には、プロトコルの確認と組織の適切なポリシーの選択という 2 つの主なカテゴリがあります。

1. Exchange ActiveSync。 既定では、 MFA および場所の条件付きアクセス ポリシーは、Exchange ActiveSyncでは適用されません。 Exchange ActiveSync では、Exchange ActiveSync ポリシーを直接構成するか、AD FS の規則を使用して Exchange ActiveSync をブロックすることで、アクセスを保護できます。
2. 従来のプロトコル。 AD FS では、従来のプロトコルをブロックすることができます。 これにより、先進認証が有効でない Office 2013 およびそれ以前の Office クライアントのアクセスがブロックされます。


### <a name="example-ad-fs-rules"></a>AD FS の規則の例
次の規則を使用して、2 つの一般的な構成で、AD FS で従来のプロトコル アクセスをブロックできます。

### <a name="option-1:-allow-exchange-activesync-and-only-allow-legacy-apps-on-the-intranet"></a>オプション 1: Exchange ActiveSync を許可し、イントラネット上のレガシ アプリケーションのみを許可します。

Microsoft Office 365 ID プラットフォームの AD FS 証明書利用者信頼に次の 3 つの規則を適用すると、Exchange ActiveSync トラフィック、ブラウザー トラフィックおよび先進認証トラフィックが許可されます。 レガシ アプリケーションは、エクストラネットからブロックされます。 

規則 1

    `@RuleName = “Allow all intranet traffic”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");`

規則 2

    @RuleName = “Allow EAS”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

規則 3

    @RuleName = “Allow Extranet browser or browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### <a name="option-2:-allow-exchange-activesync-and-block-legacy-apps"></a>オプション 2: Exchange ActiveSync を許可してレガシ アプリケーションをブロックする 
Microsoft Office 365 ID プラットフォームの AD FS 証明書利用者信頼に次の 3 つの規則を適用すると、Exchange ActiveSync トラフィック、ブラウザー トラフィックおよび先進認証トラフィックが許可されます。 レガシ アプリケーションは、どの場所からもブロックされます。 

規則 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] && 
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


規則 2 

    @RuleName = “Allow EAS”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


規則 3 

    @RuleName = “Allow Extranet browser or browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");











<!--HONumber=Oct16_HO2-->


