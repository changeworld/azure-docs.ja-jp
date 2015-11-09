<properties
   pageTitle="Azure Active Directory B2B コラボレーション プレビューの外部ユーザー トークンの形式 | Microsoft Azure"
   description="Azure Active Directory B2B は、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで、会社間の関係をサポートします。"
   services="active-directory"
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
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Azure Active Directory (Azure AD) B2B コラボレーション プレビューの外部ユーザー トークンの形式
標準の Azure AD トークンのクレームについては、azure.microsoft.com の「[サポートされているトークンとクレームの種類](active-directory-token-and-claims.md)」に記載されています。

B2B コラボレーションの認証された外部ユーザーのクレームは、以下がそれぞれ異なります。<br/> - **OID:** リソース テナントのオブジェクト ID<br/> - **TID**: リソース テナントのテナント ID<br/> - **発行者**: リソース テナント<br/> - **IDP**: ユーザーのホーム テナント<br/> - **AltSecId**: 代替のセキュリティ ID (ユーザーに対して非透過的)<br/>

## 関連記事:
Azure B2B コラボレーションに関する他の記事を参照してください。

- [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
- [動作のしくみ](active-directory-b2b-how-it-works.md)
- [詳細なチュートリアル](active-directory-b2b-detailed-walkthrough.md)
- [CSV ファイル形式リファレンス](active-directory-b2b-references-csv-file-format.md)
- [外部ユーザー オブジェクト属性の変更](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [プレビューの現在の制限事項](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=Nov15_HO1-->