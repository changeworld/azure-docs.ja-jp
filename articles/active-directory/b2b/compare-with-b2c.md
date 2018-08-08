---
title: Azure Active Directory での B2B コラボレーションと B2C の比較 | Microsoft Docs
description: Azure Active Directory B2B コラボレーションと Azure AD B2C の違いは何でしょうか。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 03/15/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: b59dba541394c105370cfd3af0768a8477ddb4e6
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39347839"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Azure Active Directory での B2B コラボレーションと B2C の比較

Azure Active Directory (Azure AD) B2B コラボレーションと Azure AD B2C は、両方とも Azure AD で外部ユーザーと共同で作業できます。 しかし、どのような違いがあるのでしょうか。


B2B コラボレーション機能 |     Azure AD B2C スタンドアロン製品
-------- | --------
対象: ID プロバイダーに関係なく、取引先組織のユーザーを認証できるようにする必要のある組織。 | 対象: 個人、法人や企業の顧客を問わず、モバイル アプリや Web アプリの顧客を Azure ID に招待するお客様。
サポートされている ID: 会社または学校のアカウントを持つ従業員、会社または学校のアカウントを持つパートナー、または任意の E メール アドレス。 近々直接フェデレーションをサポートします。  | サポートされている ID: ローカル アプリケーションのアカウント (任意の Eメール アドレスまたはユーザー名) を持つコンシューマー ユーザーまたは直接フェデレーションを使用するサポート対象の任意のソーシャル ID。
パートナー ユーザーが存在するディレクトリ: 外部組織からのパートナー ユーザーは従業員と同じディレクトリで管理されますが、特別な注釈が付与されます。 これらの外部ユーザーは従業員と同じように管理したり、同じグループに追加したりなどできます。  | お客様のユーザー エンティティが存在するディレクトリ: アプリケーション ディレクトリに存在します。 組織の従業員やパートナーのディレクトリ (存在する場合) とは別に管理されます。
あらゆる Azure AD 接続アプリへのシングル サインオン (SSO) がサポートされています。 たとえば、Office 365 またはオンプレミスのアプリケーションや、Salesforce、Workday などの SaaS アプリへのアクセスを提供できます。  |  Azure AD B2C テナント内のお客様所有のアプリへの SSO をサポートします。 Office 365 やその他のマイクロソフトおよびマイクロソフト以外の SaaS アプリへの SSO はサポートされていません。
パートナーのライフサイクル: ホスト/招待元の組織によって管理されます。  | お客様のライフサイクル: セルフサービスまたはアプリケーションによって管理されます。
セキュリティ ポリシーとコンプライアンス: ホスト/招待元の組織によって管理されます (たとえば、[条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)を使って)。  | セキュリティ ポリシーとコンプライアンス: アプリケーションによって管理されます。
ブランド化: ホスト/招待元の組織のブランドが使用されます。  |    ブランド化: アプリケーションによって管理されます。 一般的には製品によりブランド化される傾向があり、組織は目立たなくなります。
詳細情報: [ブログの投稿](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/)、[ドキュメント](what-is-b2b.md)  | 詳細情報: [製品ページ](https://azure.microsoft.com/services/active-directory-b2c/)、[ドキュメント](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>次の手順

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)
- [B2B コラボレーション ユーザーのプロパティ](user-properties.md)

