# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Windows デスクトップ アプリから Microsoft Graph API を呼び出す

このガイドでは、ネイティブの Windows デスクトップ .NET (XAML) アプリケーションでアクセス トークンを取得し、Microsoft Graph API またはアクセス トークンを必要とする他の API を、Azure Active Directory v2 エンドポイントから呼び出す方法について説明します。

このガイドを完了すると、アプリケーションで個人アカウント (outlook.com、live.com など) を使用する保護された API を呼び出すことができるようになります。 このアプリケーションでは、Azure Active Directory を使用する会社または組織の職場および学校のアカウントも使用します。  

> [!NOTE] 
> このガイドでは、Visual Studio 2015 Update 3 または Visual Studio 2017 が必要です。  どちらのバージョンもお持ちでない場合は、 [Visual Studio 2017 を無料でダウンロードできます](https://www.visualstudio.com/downloads/)。

## <a name="how-this-guide-works"></a>このガイドの利用法

![このガイドの利用法](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

このガイドで作成するサンプル アプリケーションにより、Windows デスクトップ アプリケーションで、Microsoft Graph API または Azure Active Directory v2 エンドポイントからトークンを受け取る Web API に対してクエリを実行できるようになります。 このシナリオでは、Authorization ヘッダーを使用して HTTP 要求にトークンを追加します。 トークンの取得と更新は、Microsoft Authentication Library (MSAL) で処理されます。

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>保護された Web API にアクセスするためのトークン取得処理

ユーザーが認証されると、サンプル アプリケーションは、Microsoft Graph API または Azure Active Directory v2 で保護された Web API でのクエリ実行に使用できるトークンを受け取ります。

Microsoft Graph などの API では、特定のリソースへのアクセスを許可するためにトークンが必要になります。 たとえば、トークンは、ユーザーのプロファイルの読み取り、ユーザーの予定表へのアクセス、メールの送信などに必要です。 アプリケーションでは、MSAL を使用してアクセス トークンを要求し、API スコープを指定することによってこれらのリソースにアクセスできます。 このアクセス トークンは、保護されたリソースに対するすべての呼び出しで HTTP 認証ヘッダーに追加されます。 

MSAL がアクセス トークンのキャッシュと更新を管理するため、アプリケーションでは何もする必要がありません。

## <a name="nuget-packages"></a>NuGet パッケージ

このガイドでは、次の NuGet パッケージを使用します。

|ライブラリ|[説明]|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|

