
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>iOS アプリケーションから Microsoft Graph API を呼び出す

このガイドでは、ネイティブ iOS アプリケーション (Swift) で Microsoft Azure Active Directory (Azure AD) v2.0 エンドポイントからのアクセス トークンを必要とする API を呼び出す方法を説明します。 アクセス トークンを取得し、Microsoft Graph API や他の API の呼び出しでそれを使う方法について説明します。

このガイドの演習を完了すると、Azure AD を使用する会社や組織の保護された API をアプリケーションで呼び出すことができるようになります。 アプリケーションで outlook.com や live.com などの個人アカウントおよび職場または学校アカウントを使って、保護された API を呼び出すことができます。

## <a name="prerequisites"></a>前提条件
- このガイドで作成するサンプルには、XCode バージョン 8.x が必要です。 XCode は、[iTunes の Web サイト](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode ダウンロード URL")からダウンロードできます。
- パッケージの管理には、[Carthage](https://github.com/Carthage/Carthage) 依存関係マネージャーが必要です。

## <a name="how-this-guide-works"></a>このガイドの利用法

![このガイドの利用法](media/active-directory-develop-guidedsetup-ios-introduction/iosintro.png)

このガイドのサンプル アプリケーションを参考にすると、iOS アプリケーションで、Microsoft Graph API または Azure AD v2.0 エンドポイントからトークンを受け取る Web API に対してクエリを実行できるようになります。 このシナリオでは、トークンは **Authorization** ヘッダーを使って HTTP 要求に追加されます。 トークンの取得と更新は、Microsoft Authentication Library (MSAL) で処理されます。


### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>保護された Web API にアクセスするためのトークンの取得を処理する

ユーザー認証の後、サンプル アプリケーションはトークンを受け取ります。 トークンは、Microsoft Graph API や、Azure AD v2.0 エンドポイントによって保護された Web API に対して、クエリを実行するために使われます。

Microsoft Graph などの API では、特定のリソースへのアクセスを許可するためにアクセス トークンが必要になります。 トークンは、ユーザーのプロファイルの読み取り、ユーザーの予定表へのアクセス、メールの送信などに必要です。 アプリケーションでは、MSAL を使って API スコープを指定することで、アクセス トークンを要求できます。 アクセス トークンは、保護されたリソースに対するすべての呼び出しで、HTTP の **Authorization** ヘッダーに追加されます。

MSAL がアクセス トークンのキャッシュと更新を管理するため、アプリケーションでは何もする必要がありません。


## <a name="libraries"></a>ライブラリ

このガイドでは、次のライブラリを使用します。

|ライブラリ|Description|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Microsoft Authentication Library の iOS 用プレビュー|

