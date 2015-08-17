<properties
   pageTitle="Azure Active Directory のコード例"
   description="シナリオ別に整理された Azure Active Directory のコード サンプルのインデックスです。"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/30/2015"
   ms.author="mbaldwin"/>

# Azure Active Directory のコード例

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Microsoft Azure Active Directory (Azure AD) を使用すると、Web アプリケーションおよび Web API に認証と承認を追加できます。このセクションでは、その方法を示すコード サンプルへのリンクと、アプリケーションで使用できるコード スニペットを示します。コード サンプルのページには、要件、インストール、設定に関する詳細な Readme トピックが含まれています。また、重要なセクションの理解に役立つように、コードにはコメントが付けられています。

各サンプル タイプの基本的なシナリオを理解するには、「Azure AD の認証シナリオ」を参照してください。

GitHub でのサンプルに関する情報: [Microsoft Azure Active Directory のサンプルおよびドキュメント](https://github.com/AzureADSamples)。

## Web ブラウザー対 Web アプリケーション

次のサンプルでは、ユーザーのブラウザーに指示を出してユーザーを Azure AD にサインインさせるようにする Web アプリケーションの作成方法を示します。



| 言語/プラットフォーム | サンプル | 説明
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-OpenIDConnect-DotNet](http://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | OpenID Connect (ASP.Net OpenID Connect OWIN ミドルウェア) を使用して Azure AD テナントからのユーザーを認証します。
| C#/.NET | [WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet) | OpenID Connect (ASP.Net OpenID Connect OWIN ミドルウェア) を使用して複数の Azure AD テナントからのユーザーを認証するマルチテナント .NET MVC Web アプリケーション。
| C#/.NET | [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | WS-Federation (ASP.Net WS-Federation OWIN ミドルウェア) を使用して Azure AD テナントからのユーザーを認証します。






## シングル ページ アプリケーション (SPA)

このサンプルでは、Azure AD を使用してセキュリティ保護されているシングル ページ アプリケーションの作成方法を示します。

| 言語/プラットフォーム | サンプル | 説明
| ----------------- | ------ | -----------
| JavaScript、C#/.NET | [SinglePageApp-DotNet](https://github.com/AzureADSamples/SinglePageApp-DotNet) | ADAL for JavaScript と Azure AD を使用して、ASP.NET Web API のバック エンドで実装された AngularJS ベースのシングル ページ アプリケーションをセキュリティ保護します。


## ネイティブ アプリケーション対 Web API

次のコード サンプルでは、Azure AD によってセキュリティ保護されている Web API を呼び出すネイティブ クライアント アプリケーションの構築方法を示します。[Azure AD Authentication Library (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) および [Azure AD での OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) が使用されます。

| 言語/プラットフォーム | サンプル | 説明
| ----------------- | ------ | -----------
| JavaScript | [NativeClient-MultiTarget-Cordova](https://github.com/AzureADSamples/NativeClient-MultiTarget-Cordova) | Apache Cordova 用の ADAL プラグインを使用して、認証のために Web API を呼び出し、Azure AD を使用する Apache Cordova アプリを作成します。
| C#/.NET | [NativeClient-DotNet](http://github.com/AzureADSamples/NativeClient-DotNet) | Azure AD を使用してセキュリティ保護されている Web API を呼び出す .NET WPF アプリケーション。
| C#/.NET | [NativeClient-WindowsStore](http://github.com/AzureADSamples/NativeClient-WindowsStore) | Azure AD を使用してセキュリティ保護されている Web API を呼び出す Windows ストア アプリケーション。
| C#/.NET | [NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore) | Azure AD を使用してセキュリティ保護されているマルチテナント Web API を呼び出す Windows ストア アプリケーション。
| C#/.NET | [WebAPI-OnBehalfOf-DotNet](http://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet) | Web API を呼び出すネイティブ クライアント アプリケーション。これは、元のユーザーに代わって動作するトークンを取得し、このトークンを使用して別の Web API を呼び出します。
| C#/.NET | [NativeClient-WindowsPhone8.1](https://github.com/AzureADSamples/NativeClient-WindowsPhone8.1) | Azure AD によってセキュリティ保護されている Web API を呼び出す、Windows Phone 8.1 用の Windows ストア アプリケーション。
| ObjC | [NativeClient-iOS](http://github.com/AzureADSamples/NativeClient-iOS) | 認証で Azure AD を必要とする Web API を呼び出す iOS アプリケーション。
| C#/.NET | [WebAPI-ManuallyValidateJwt-DotNet](https://github.com/AzureADSamples/WebAPI-ManuallyValidateJwt-DotNet) | OWIN ミドルウェアを使用する代わりに、Web API で JWT トークンを処理するロジックを備えるネイティブ クライアント アプリケーション。
| C#/Xamarin | [NativeClient-Xamarin-Android](https://github.com/AzureADSamples/NativeClient-Xamarin-Android) | Android ライブラリ用のネイティブ Azure AD Authentication Library (ADAL) にバインドする Xamarin。
| C#/Xamarin | [NativeClient-Xamarin-iOS](http://github.com/AzureADSamples/NativeClient-Xamarin-iOS) | iOS 用のネイティブ Azure AD Authentication Library (ADAL) にバインドする Xamarin。
| C#/Xamarin | [NativeClient-MultiTarget-DotNet](http://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet) | 5 つのプラットフォームを対象とし、Azure AD によってセキュリティ保護されている Web API を呼び出す Xamarin プロジェクト。
| C#/.NET | [NativeClient-Headless-DotNet](http://github.com/AzureADSamples/NativeClient-Headless-DotNet) | 非対話型の認証を実行し、Azure AD によってセキュリティ保護されている Web API を呼び出すネイティブ アプリケーション。



## Web アプリケーション対 Web API

次のコード サンプルでは、Azure AD によってセキュリティ保護されている Web API を呼び出す Web アプリケーションを、[Azure AD での OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) を使用して構築する方法を示します。

| 言語/プラットフォーム | サンプル | 説明
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-WebAPI-OpenIDConnect-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNet) | サインインしているユーザーのアクセス許可を使用して Web API を呼び出します。
| C#/.NET | [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-AppIdentity-DotNet) | アプリケーションのアクセス許可を使用して Web API を呼び出します。
| C#/.NET | [WebApp-WebAPI-OAuth2-UserIdentity-Dotnet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-UserIdentity-Dotnet) | [Azure AD での OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) を使用して既存の Web アプリケーションに承認を追加することで、その Web アプリケーションが Web API を呼び出せるようにします。
| JavaScript | [WebAPI-Nodejs](http://github.com/AzureADSamples/WebAPI-Nodejs) | API 保護のために Azure AD と統合される REST API サービスを設定します。Web API に Node.js サーバーを含めます。
| C#/.NET | [WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet) | OpenID Connect (ASP.Net OpenID Connect OWIN ミドルウェア) を使用して Azure AD テナントからのユーザーを認証するマルチテナント MVC Web アプリケーション。承認コードを使用して Graph API を呼び出します。

## サーバーまたはデーモン アプリケーション対 Web API

次のコード サンプルでは、Web API からリソースを取得するデーモンまたはサーバー アプリケーションを、[Azure AD Authentication Library (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) および [Azure AD での OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) を使用して構築する方法を示します。

| 言語/プラットフォーム | サンプル | 説明
| ----------------- | ------ | -----------
| C#/.NET | [Daemon-DotNet](http://github.com/AzureADSamples/Daemon-DotNet) | Web API を呼び出すコンソール アプリケーション。クライアント資格情報はパスワードです。
| C#/.NET | [Daemon-CertificateCredential-DotNet](http://github.com/AzureADSamples/Daemon-CertificateCredential-DotNet) | Web API を呼び出すコンソール アプリケーション。クライアント資格情報は証明書です。


## Azure AD Graph API の呼び出し

次のコード サンプルでは、ディレクトリ データの読み書きを行うために Azure AD Graph API を呼び出すアプリケーションの構築方法を示します。

| 言語/プラットフォーム | サンプル | 説明
| ----------------- | ------ | -----------
| Java | [WebApp-GraphAPI-Java](http://github.com/AzureADSamples/WebApp-GraphAPI-Java) | Graph API を使用して Azure AD ディレクトリ データにアクセスする Web アプリケーション。
| PHP | [WebApp-GraphAPI-PHP](http://github.com/AzureADSamples/WebApp-GraphAPI-PHP) | Graph API を使用して Azure AD ディレクトリ データにアクセスする Web アプリケーション。
| C#/.NET | [WebApp-GraphAPI-DotNet](http://github.com/AzureADSamples/WebApp-GraphAPI-DotNet) | Graph API を使用して Azure AD ディレクトリ データにアクセスする Web アプリケーション。
| C#/.NET | [ConsoleApp-GraphAPI-DotNet](https://github.com/AzureADSamples/ConsoleApp-GraphAPI-DotNet) | このコンソール アプリケーションは、Graph API に対する一般的な Read および Write 呼び出しのデモを示し、ユーザー ライセンスの割り当ての実行方法と、ユーザーのサムネイルの写真とリンクの更新方法を示します。
| C#/.NET | [ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/AzureADSamples/ConsoleApp-GraphAPI-DiffQuery-DotNet) | Azure AD テナントのユーザー オブジェクトに定期的に加えられる変更を取得するために Graph API 内の差分クエリを使用するコンソール アプリケーション。
| C#/.NET | [WebApp-GraphAPI-DirectoryExtensions-DotNet](https://github.com/AzureADSamples/WebApp-GraphAPI-DirectoryExtensions-DotNet) | MVC アプリケーションは、Graph API クエリを使用して、簡単な会社組織グラフを生成します。
| PHP | [WebApp-GraphAPI-DirectoryExtensions-PHP](https://github.com/AzureADSamples/WebApp-GraphAPI-DirectoryExtensions-PHP) | 拡張機能の登録および拡張機能属性内の値の読み取り、更新、削除を行うために Graph API アプリケーションを呼び出す PHP アプリケーション。


## 承認

次のコード サンプルでは、承認で Azure AD を使用する方法を示します。

| 言語/プラットフォーム | サンプル | 説明
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet) | Azure AD と統合されるアプリケーション内で Azure Active Directory グループ要求を利用したロール ベースのアクセス制御 (RBAC) を実行します。
| C#/.NET | [WebApp-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) | Azure AD と統合されるアプリケーション内で Azure Active Directory アプリケーション ロールを利用したロール ベースのアクセス制御 (RBAC) を実行します。


## レガシ チュートリアル

次のチュートリアルは、少し古い技術を使用していますが、依然として重要であると考えられます。

| 言語/プラットフォーム | サンプル | 説明
| ----------------- | ------ | -----------
| C#/.NET | [Microsoft Azure AD アプリケーションでのロール ベースおよび ACL ベースの承認](http://go.microsoft.com/fwlink/?LinkId=331694) | Azure AD と統合されているアプリケーションで、ロール ベースの承認 (RBAC) および ACL ベースの承認を実行します。
| C#/.NET | [AAL - Windows ストア アプリ対 REST サービス - 認証](http://go.microsoft.com/fwlink/?LinkId=330605) | Windows ストアのベータ版に対応する [Azure AD Authentication Library (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) (以前の AAL) を使用して、ユーザー認証機能を Windows ストア アプリに追加します。
| C#/.NET | [ADAL - ネイティブ アプリ対 REST サービス - ブラウザー ダイアログを介した AAD での認証](http://go.microsoft.com/fwlink/?LinkId=259814) | [Azure AD Authentication Library (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) を使用して、WPF クライアントにユーザー認証機能を追加します。
| C#/.NET | [ADAL - ネイティブ アプリ対 REST サービス - ブラウザー ダイアログを介した ACS での認証](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) | [Azure AD Authentication Library (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) および [Access Control Service 2.0 (ACS)](http://msdn.microsoft.com/library/azure/hh147631.aspx) を使用して、WPF クライアントにユーザー認証機能を追加します。
| C#/.NET | [ADAL - サーバー対サーバー認証](http://go.microsoft.com/fwlink/?LinkId=259816) | [Azure AD Authentication Library (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) を使用して、サーバー側プロセスから MVC4 Web API REST サービスへのサービス呼び出しのセキュリティを保護します。
| C#/.NET | [Azure AD を使用した Web アプリケーションへのサインオンの追加](https://msdn.microsoft.com/library/azure/dn151790.aspx) | .NET アプリケーションを構成して、Azure AD エンタープライズ ディレクトリに対する Web シングル サインオンを実行します。
| C#/.NET | [Azure AD でのマルチテナント Web アプリケーションの開発](https://msdn.microsoft.com/library/azure/dn151789.aspx) | Azure AD を使用して、複数の組織で機能する 1 つの .NET アプリケーションのシングル サインオンおよびディレクトリ アクセス機能を追加します。
JAVA | [Azure AD Graph API 用の Java サンプル アプリ](http://go.microsoft.com/fwlink/?LinkId=263969) | Graph API を使用して、Azure AD からのディレクトリ データにアクセスします。
PHP | [Azure AD Graph API 用の PHP サンプル アプリ](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) | Graph API を使用して、Azure AD からのディレクトリ データにアクセスします。
| C#/.NET | [Azure AD Graph API 用のサンプル アプリ](http://go.microsoft.com/fwlink/?LinkID=262648) | Graph API を使用して、Azure AD からのディレクトリ データにアクセスします。
| C#/.NET | [Azure AD Graph 差分クエリ用のサンプル アプリ](http://go.microsoft.com/fwlink/?LinkId=275398) | Graph API 内の差分クエリを使用して、Azure AD テナントのユーザー オブジェクトに定期的に加えられる変更を取得します。
| C#/.NET | [マルチテナント クラウド アプリケーションを Azure AD に統合するサンプル アプリ](http://go.microsoft.com/fwlink/?LinkId=275397) | マルチ テナント アプリケーションを Azure AD に統合します。
| C#/.NET | [Azure AD を使用した Windows ストア アプリケーションと REST Web サービスのセキュリティ保護 (プレビュー)](https://msdn.microsoft.com/library/azure/dn169448.aspx) | Azure AD と [Azure AD Authentication Library (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) を使用する単純な Web API リソースと Windows ストア クライアント アプリケーションを作成します。
| C#/.NET| [Graph API を使用した Azure AD のクエリ](https://msdn.microsoft.com/library/azure/dn151791.aspx) | Azure AD Graph API を使用して Azure AD テナント ディレクトリのデータにアクセスするように Microsoft .NET アプリケーションを構成します。

## 関連項目

##### その他のリソース


[Azure AD Graph API Helper Library](http://code.msdn.microsoft.com/Windows-Azure-AD-Graph-API-a8c72e18)

[Developing Modern Applications using OAuth and Active Directory Federation Services](http://msdn.microsoft.com/library/dn633593.aspx)

<!---HONumber=August15_HO6-->