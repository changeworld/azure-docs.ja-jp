<properties
   pageTitle="マルチテナント アプリケーションの ID 管理 | Microsoft Azure"
   description="マルチテナント アプリケーションでの認証、承認、および ID 管理のベスト プラクティス。"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Microsoft Azure のマルチテナント アプリケーションの ID 管理

このシリーズでは、認証と ID の管理に Azure AD を使用する場合の、マルチテナント方式のベスト プラクティスについて説明します。

マルチテナント アプリケーションを構築する場合、最初の課題の 1 つは、すべてのユーザーがテナントに属しているようになるために、ユーザー ID を管理することです。次に例を示します。

- ユーザーは、その組織の資格情報でサインインします。
- ユーザーは、その組織のデータへのアクセス権を持つ必要がありますが、他のテナントに属しているデータは必要ありません。
- 組織は、アプリケーションにサインアップし、そのメンバーにアプリケーション ロールを割り当てることができます。

Azure Active Directory (Azure AD) には、これらのシナリオのすべてをサポートするいくつかの優れた機能があります。

この一連の記事を付属するために、マルチテナント アプリケーションの完全な [エンド ツー エンド実装][tailspin]も作成しました。記事には、アプリケーションの作成プロセスで学習した内容が反映されています。アプリケーションを開始するには、「[Running the Surveys application](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md)」 (Surveys アプリケーションの実行) を参照してください。


## 目次

- [はじめに](guidance-multitenant-identity-intro.md)
- [Tailspin Surveys アプリケーションについて](guidance-multitenant-identity-tailspin.md)
- [Azure AD での認証](guidance-multitenant-identity-authenticate.md)
- [クレーム ベースの ID の操作](guidance-multitenant-identity-claims.md)
- [サインアップおよびテナントのオンボーディング](guidance-multitenant-identity-signup.md)
- [アプリケーション ロールの定義と管理](guidance-multitenant-identity-app-roles.md)
- [承認](guidance-multitenant-identity-authorize.md)
- [バックエンド Web API のセキュリティ保護](guidance-multitenant-identity-web-api.md)
- [OAuth2 アクセス トークンのキャッシュ](guidance-multitenant-identity-token-cache.md)
- [顧客の AD FS とのフェデレーション](guidance-multitenant-identity-adfs.md)
- [クライアント アサーションを使用した、Azure AD からのアクセス トークンの取得](guidance-multitenant-identity-client-assertion.md)
- [Key Vault を使用した、アプリケーション シークレットの保護](guidance-multitenant-identity-keyvault.md)

[tailspin]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->