<properties
   pageTitle="Azure Active Directory 認証ライブラリ | Microsoft Azure"
   description="Azure AD 認証ライブラリ (ADAL) を使用すると、クライアント アプリケーション開発者は、クラウドまたはオンプレミスの Active Directory (AD) に対して簡単にユーザーを認証し、API 呼び出しを保護するためのアクセス トークンを取得できます。"
   services="active-directory"
   documentationCenter=""
   authors="msmbaldwin"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin" />

# Azure Active Directory 認証ライブラリ

Azure AD 認証ライブラリ (ADAL) を使用すると、クライアント アプリケーション開発者は、クラウドまたはオンプレミスの Active Directory (AD) に対して簡単にユーザーを認証し、API 呼び出しを保護するためのアクセス トークンを取得できます。ADAL には、非同期のサポート、アクセス トークンと更新トークンを格納する構成可能なトークン キャッシュ、アクセス トークンの有効期限が切れたときに更新トークンを使用できる場合のトークンの自動更新など、開発者向けに認証を容易にする多くの機能が用意されています。複雑な部分のほとんどが ADAL によって処理されるため、開発者はアプリケーションでビジネス ロジックに集中し、セキュリティの専門家でなくても簡単にリソースを保護できます。

ADAL は、さまざまなプラットフォームで使用できます。

|プラットフォーム|パッケージ名|クライアント/サーバー|ダウンロード|ソース コード|ドキュメントとサンプル|
|---|---|---|---|---|---|
|.NET クライアント、Windows ストア、Windows Phone (8.1)|Active Directory Authentication Library (ADAL) for .NET|クライアント|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)|[ADAL for .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet)|[ドキュメント](https://msdn.microsoft.com/library/azure/mt417579.aspx)|
|JavaScript|Active Directory Authentication Library (ADAL) for JavaScript|クライアント|[ADAL for JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|[ADAL for JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|サンプル: [SinglePageApp-DotNet (Github)](https://github.com/AzureADSamples/SinglePageApp-DotNet)|
|OS X、iOS|Active Directory Authentication Library (ADAL) for Objective-C|クライアント|[ADAL for Objective-C (CocoaPods)](https://cocoapods.org/?q=adal%20io)|[ADAL for Objective-C (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)|サンプル: [NativeClient-iOS (Github)](https://github.com/AzureADSamples/NativeClient-iOS)|
|Android|Active Directory Authentication Library (ADAL) for Android|クライアント|[ADAL for Android (中央リポジトリ)](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/)|[ADAL for Android (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-android)|サンプル: [NativeClient-Android (Github)](https://github.com/AzureADSamples/NativeClient-Android)|
|Node.js|Active Directory Authentication Library (ADAL) for Node.js|クライアント|[ADAL for Node.js (npm)](https://www.npmjs.com/package/adal-node)|[ADAL for Node.js (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)|サンプル: [WebAPI-Nodejs (Github)](https://github.com/AzureADSamples/WebAPI-Nodejs)|
|Node.js|Node 用 Microsoft Azure Active Directory Passport 認証ミドルウェア|クライアント|[Azure Active Directory Passport for Node.js (npm)](https://www.npmjs.com/package/passport-azure-ad)|[Azure Active Directory for Node.js (Github)](https://github.com/AzureAD/passport-azure-ad)||
|Java|Active Directory Authentication Library (ADAL) for Java|クライアント|[ADAL for Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)|[ADAL for Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)||
|.NET|Identity Protocol Extensions for the Microsoft .NET Framework 4.5|サーバー|[Microsoft.IdentityModel.Protocol.Extensions (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions)|[Azure AD Identity Model Extensions for .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|JSON Web Token Handler For the Microsoft .Net Framework 4.5|サーバー|[System.IdentityModel.Tokens.Jwt (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt)|[Azure AD Identity Model Extensions for .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|アプリケーションで認証に Microsoft のテクノロジを使用できるようにするための OWIN ミドルウェア|サーバー|[Microsoft.Owin.Security.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)||
|.NET|アプリケーションで認証に OpenIDConnect を使用できるようにするための OWIN ミドルウェア|サーバー|[Microsoft.Owin.Security.OpenIdConnect (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|サンプル: [WebApp-OpenIDConnecty-DotNet (Github)](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet)|
|.NET|アプリケーションで認証に WS-Federation を使用できるようにするための OWIN ミドルウェア|サーバー|[Microsoft.Owin.Security.WsFederation (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|サンプル: [WebApp-WSFederation-DotNet (Github)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)|

## シナリオ

ADAL を認証に使用できる 3 つの一般的なシナリオを以下に示します。

### リモート リソースに対してクライアント アプリケーションのユーザーを認証する

このシナリオでは、開発者は Azure AD によって保護されたリモート リソース (Web API など) にアクセスする必要があるクライアント (WPF アプリケーションなど) を使用しています。また、Azure サブスクリプションを持ち、ダウンストリーム Web API を呼び出す方法と、Web API が使用する Azure AD テナントを知っています。そのため、ADAL を使用して、ADAL に認証エクスペリエンスを完全に委任するか、ユーザー資格情報を明示的に処理することで、Azure AD による認証を容易にすることができます。ADAL により、ユーザーを認証し、Azure AD からアクセス トークンと更新トークンを取得し、そのアクセス トークンを使用して Web API への要求を行うことが容易になります。

Azure AD に対する認証を使用してこのシナリオを実証するコード サンプルについては、[ネイティブ クライアント WPF アプリケーションと Web API](https://github.com/azureadsamples/nativeclient-dotnet) に関するページをご覧ください。

### リモート リソースに対してサーバー アプリケーションを認証する

このシナリオでは、開発者は Azure AD によって保護されたリモート リソース (Web API など) にアクセスする必要がある、サーバー上で実行されるアプリケーションを使用しています。また、Azure サブスクリプションを持ち、ダウンストリーム サービスを呼び出す方法と、Web API が使用する Azure AD テナントを知っています。そのため、ADAL を使用して、アプリケーションの資格情報を明示的に処理することで、Azure AD による認証を容易にすることができます。ADAL により、アプリケーションのクライアント資格情報を使用して Azure AD からトークンを取得し、そのトークンを使用して Web API への要求を行うことが容易になります。また、ADAL はアクセス トークンをキャッシュしてその有効期間を管理し、必要に応じて更新します。このシナリオを実証するコード サンプルについては、[コンソール アプリケーションと Web API](https://github.com/AzureADSamples/Daemon-DotNet) に関するページをご覧ください。

### リモート リソースにアクセスするために、ユーザーに代わってサーバー アプリケーションを認証する

このシナリオでは、開発者は Azure AD によって保護されたリモート リソース (Web API など) にアクセスする必要がある、サーバー上で実行されるアプリケーションを使用しています。また、Azure AD でユーザーに代わって要求を行う必要があります。開発者は Azure サブスクリプションを持ち、ダウンストリーム Web API を呼び出す方法と、サービスが使用する Azure AD テナントを知っています。ユーザーが Web アプリケーションに対して認証されると、アプリケーションは Azure AD からユーザーの認証コードを取得できます。その後、Web アプリケーションは、ADAL を使用して、アプリケーションに関連付けられた認証コードとクライアント資格情報を使って、ユーザーの代わりに Azure AD からアクセス トークンと更新トークンを取得できます。アクセス トークンを所有している Web アプリケーションは、トークンの有効期限が切れるまで Web API を呼び出すことができます。トークンの有効期限が切れたら、Web アプリケーションは ADAL を使用して、以前に取得した更新トークンを使って新しいアクセス トークンを取得できます。


## 関連項目

[Azure Active Directory 開発者ガイド](active-directory-developers-guide.md)

[Azure AD の認証シナリオ](active-directory-authentication-scenarios.md)

[Azure Active Directory のコード例](active-directory-code-samples.md)

<!---HONumber=AcomDC_0921_2016-->