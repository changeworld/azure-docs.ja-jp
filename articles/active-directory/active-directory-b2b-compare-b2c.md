---

title: "Azure Active Directory での B2B コラボレーションと B2C の比較 | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションと Azure AD B2C の違いは何でしょうか。"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/08/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 72e7e57185d882a5f3243e2705aed54ee8347798
ms.openlocfilehash: 5d157a936317bbf66cfc3634d4d08ea3483d137b


---

# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Azure Active Directory での B2B コラボレーションと B2C の比較

Azure Active Directory (Azure AD) B2B コラボレーションと Azure AD B2C は、両方とも Azure AD で外部ユーザーと共同で作業できます。 しかし、どのような違いがあるのでしょうか。


B2B コラボレーション機能 |     Azure AD B2C スタンドアロン製品
-------- | --------
対象: 企業データ、リソース、およびアプリケーションへのアクセス権限を、選択した任意の ID を使用して他の組織のユーザーに提供する必要がある組織。 | 対象: (従業員や外部共同作業者以外の) 個人、一般人、法人や企業の顧客を対象とするモバイル アプリと Web アプリに、選択した任意の ID を使用して接続するお客様。
サポートされている ID: 会社または学校のアカウントを持つ従業員、会社または学校のアカウントを持つパートナー、または任意の E メール アドレス。 近々直接フェデレーションをサポートします。  | サポートされている ID: ローカル アプリケーションのアカウント (任意の Eメール アドレスまたはユーザー名) を持つコンシューマー ユーザーまたは直接フェデレーションを使用するサポート対象の任意のソーシャル ID。
パートナー ユーザーが存在するディレクトリ: 外部組織からのパートナー ユーザーは従業員と同じディレクトリで管理されますが、特別な注釈が付与されます。 これらの外部ユーザーは従業員と同じように管理したり、同じグループに追加したりなどできます。  | お客様のユーザー エンティティが存在するディレクトリ: アプリケーション ディレクトリに存在します。 組織の従業員やパートナーのディレクトリ (存在する場合) とは別に管理されます。
Azure AD に接続されているすべてのアプリ (オンプレミスのアプリを含む) に対するシングル サインオン (SSO) および Office 365 やその他のマイクロソフトおよびマイクロソフト以外の SaaS アプリ (Salesforce、Box、Workday など) への SSO がサポートされます。  |  Azure AD B2C テナント内のお客様所有のアプリへの SSO をサポートします。 Office 365 やその他のマイクロソフトおよびマイクロソフト以外の SaaS アプリへの SSO はサポートされていません。
パートナーのライフサイクル: ホスト/招待元の組織によって管理されます。  | お客様のライフサイクル: セルフサービスまたはアプリケーションによって管理されます。
セキュリティ ポリシーとコンプライアンス: ホスト/招待元の組織によって管理されます。  | セキュリティ ポリシーとコンプライアンス: アプリケーションによって管理されます。
ブランド化: ホスト/招待元の組織のブランドが使用されます。  |    ブランド化: アプリケーションによって管理されます。 一般的には製品によりブランド化される傾向があり、組織は目立たなくなります。
詳細情報: [ブログの投稿](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/)、[ドキュメント](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | 詳細情報: [製品ページ](https://azure.microsoft.com/en-us/services/active-directory-b2c/)、[ドキュメント](https://docs.microsoft.com/en-us/azure/active-directory-b2c/)


### <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B コラボレーション ユーザーのプロパティ](active-directory-b2b-user-properties.md)
* [B2B コラボレーション ユーザーのロールへの追加](active-directory-b2b-add-guest-to-role.md)
* [B2B コラボレーションの招待を委任する](active-directory-b2b-delegate-invitations.md)
* [動的グループと B2B コラボレーション](active-directory-b2b-dynamic-groups.md)
* [B2B コラボレーション用に SaaS アプリを構成する](active-directory-b2b-configure-saas-apps.md)
* [B2B コラボレーション ユーザーのトークン](active-directory-b2b-user-token.md)
* [B2B コラボレーション ユーザーの要求マッピング](active-directory-b2b-claims-mapping.md)
* [Office 365 の外部共有](active-directory-b2b-o365-external-user.md)
* [B2B コラボレーションの現在の制限](active-directory-b2b-current-limitations.md)
* [B2B コラボレーションのサポートの利用](active-directory-b2b-support.md)



<!--HONumber=Feb17_HO2-->


