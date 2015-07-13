<properties
	pageTitle="Azure AD の動作"
	description="Azure AD では、クラウド内に自分の ID ランドス ケープが作成されます。これは、内部設置型の ID システムに接続するか、または単独で使用することができます。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="curtand"/>



# Azure Active Directory の機能


###このトピックに関するその他の記事
[Azure AD とは](active-directory-whatis.md)<br> [どのよう動作しますか。](active-directory-works.md)<br> [開始](active-directory-get-started.md)<br> [次のステップ](active-directory-next-steps.md)<br> [詳細](active-directory-learn-map.md)


Azure AD では、クラウド内に自分の ID ランドス ケープが作成されます。これは、内部設置型の ID システムに接続するか、または単独で使用することができます。

Azure AD のアカウントは、クラウドでの運転免許書と見なすことができます。これは、オンラインでサービスにアクセスするための一意の ID です。この例で、Azure AD は、運転免許証に対してクラウド内のユーザー専用の登録機関のような役割を果たします。これは、クラウド内のあらゆる場所で ID を使用可能にし、内部設置型のリソースにアクセスするユーザーのモビリティを向上させます。

> [AZURE.NOTE]Azure Active Directory を使用するには、Azure のアカウントが必要です。アカウントを持っていない場合は、[無料の Azure アカウントにサインアップ](http://azure.microsoft.com/pricing/free-trial/)できます。

## Azure AD では、Office 365、Microsoft Intune、および他の Azure サービスがどのようにサポートされていますか。
Azure ポータル、Office 365 管理センター、Microsoft Intune アカウント ポータル、および Azure AD PowerShell モジュールのコマンドレットはすべて、ディレクトリに関連付けられている Azure AD の単一の共有インスタンスに対して読み書きを実行します。ポータル (またはコマンドレット) は、ディレクトリ情報を取り込み、または変更するフロント エンド インターフェイスとして機能します。[その他のサービスのサポートの詳細情報](active-directory-administer.md#what-is-an-azure-ad-tenant)

## Azure AD の、サードパーティ製アプリケーションのサポート
Azure AD は、すぐにコーディングを開始するためのさまざまなプラットフォーム向けのオープン ソース ライブラリとともに、サービスとしての ID を提供することにより、開発者にとっての認証を簡略化します。[Azure AD の認証シナリオの詳細情報](active-directory-authentication-scenarios.md)。


## Azure AD の内部設置型ディレクトリ拡張方法
Azure AD では、最も広く使用されている認証および承認のプロトコルが複数サポートされています。[ Azure Active Directory の認証プロトコルの詳細情報](active-directory-authentication-scenarios.md)。

## ID 管理のための Azure
Azure AD を管理する方法、 ディレクトリの取得方法、 ディレクトリの削除方法、 ディレクトリ データの管理方法など、 Azure AD ディレクトリの管理の詳細情報をご覧いただけます。[Azure AD の管理方法の詳細情報](active-directory-administer.md)

## その他のリソース

* [Azure への組織としてのサインアップ](sign-up-organization.md)
* [Azure ID](fundamentals-identity.md)
 

<!---HONumber=62-->