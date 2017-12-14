# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Windows デスクトップ アプリから Microsoft Graph API を呼び出す

このガイドでは、ネイティブの Windows デスクトップ .NET (XAML) アプリケーションがアクセス トークンを取得し、Microsoft Graph API またはアクセス トークンを必要とする他の API を、Azure Active Directory v2 エンドポイントから呼び出す方法について説明します。

このガイドの最後では、個人アカウント (outlook.com、live.com などを含む) だけでなく、Azure Active Directory のあるすべての会社や組織の職場/学校アカウントを使用して、保護された API をアプリケーションで呼び出せるようになります。  

> このガイドでは、Visual Studio 2015 Update 3 または Visual Studio 2017 が必要です。  お持ちでない場合は、 [Visual Studio 2017 を無料でダウンロードできます。](https://www.visualstudio.com/downloads/)

### <a name="how-this-guide-works"></a>このガイドの利用法

![このガイドの利用法](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

このガイドで作成したサンプル アプリケーションにより、Windows デスクトップ アプリケーションで、Microsoft Graph API または Azure Active Directory v2 エンドポイントからトークンを受け取る Web API でクエリを実行できるようになります。 このシナリオでは、トークンは認証ヘッダーを介して HTTP 要求に追加されます。 トークンの取得と更新は、Microsoft Authentication Library (MSAL) で処理されます。


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>保護された Web API にアクセスするためのトークン取得処理

ユーザーが認証されると、サンプル アプリケーションは、Microsoft Graph API または Microsoft Azure Active Directory v2 によって保護されている Web API でのクエリ実行で使用できるトークンを受け取ります。

Microsoft Graph などの API では、特定のリソースにアクセスできるアクセス トークンが必要です。たとえば、ユーザーのプロファイルの読み取り、ユーザーの予定表へのアクセス、電子メールの送信などです。 アプリケーションでは、MSAL を使用してアクセス トークンを要求し、API スコープを指定することによってこれらのリソースにアクセスできます。 このアクセス トークンは、保護されたリソースに対するすべての呼び出しで HTTP 認証ヘッダーに追加されます。 

MSAL がアクセス トークンのキャッシュと更新を管理します。そのため、アプリケーションが管理する必要はありません。


### <a name="nuget-packages"></a>NuGet パッケージ

このガイドでは、次の NuGet パッケージを使用します。

|ライブラリ|Description|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|

