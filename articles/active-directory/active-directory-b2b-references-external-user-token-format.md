<properties
   pageTitle="Azure Active Directory B2B コラボレーション プレビューの外部ユーザー トークンの形式 | Microsoft Azure"
   description="Azure Active Directory B2B は、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで、会社間のリレーションシップをサポートします"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# Azure AD B2B コラボレーション プレビュー: 外部ユーザー トークンの形式

標準の Azure AD トークンの要求については、azure.microsoft.com の「[サポートされているトークンと要求の種類](active-directory-token-and-claims.md)」に記載されています。

B2B コラボレーションの認証された外部ユーザーの要求は、以下がそれぞれ異なります。<br/>
- **OID:** リソース テナントのオブジェクト ID<br/>
- **TID:** リソース テナントのテナント ID<br/>
- **発行者**: これはリソース テナントです<br/>
- **IDP**: これはユーザーのホームのテナントです<br/>
- **AltSecId**: これは代替のセキュリティ ID (ユーザーに対して非透過的です)<br/>

## 関連記事
Azure AD B2B コラボレーションに関する他の記事を参照してください。

- [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
- [動作のしくみ](active-directory-b2b-how-it-works.md)
- [詳細なチュートリアル](active-directory-b2b-detailed-walkthrough.md)
- [CSV ファイル形式リファレンス](active-directory-b2b-references-csv-file-format.md)
- [外部ユーザー オブジェクト属性の変更](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [現在のプレビューの制限事項](active-directory-b2b-current-preview-limitations.md)
- [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0511_2016-->