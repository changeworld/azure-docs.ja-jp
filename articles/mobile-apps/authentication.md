---
title: Visual Studio App Center と Azure サービスを使用してモバイル アプリに認証を追加する
description: ユーザー認証を設定したり、モバイル アプリケーションがソーシャル アカウント、Azure Active Directory、およびカスタム認証を使用して認証を受けられるようにしたりするのに役立つ Visual Studio App Center などのサービスについて説明します。
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 820005bca008ea3bdb59659c815da3ec36beb0d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241047"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>ご利用のモバイル アプリに認証を追加し、ユーザー ID を管理する

ご利用のアプリケーション全体でのユーザーとその行動を把握することにより、開発者はユーザーに合わせたエクスペリエンスを作成してユーザーの関心を高めることができます。 組織内のユーザー向けにコラボレーション アプリケーションを構築しているアプリケーション開発者であっても、次のソーシャル ネットワーク プラットフォームを作成している場合であっても、ユーザーを認証し、ユーザー ID を管理する方法が必要になります。 ID 管理サービスは、モバイル バックエンド サービスの最も重要な機能の 1 つです。

次のサービスを使用することで、ご利用のモバイル アプリ内でユーザー認証を有効にすることができます。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Auth](/appcenter/auth/) はクラウドベースの ID 管理サービスです。開発者はこれを使用してユーザーを認証し、ユーザー ID を管理することができます。 App Center Auth は、Visual Studio App Center のその他の部分とも統合されます。 開発者は、ユーザー ID を使用して、他のサービスの[ユーザー データを表示したり](/appcenter/data/index)、個々のデバイスではなく[ユーザーにプッシュ通知を送信したり](/appcenter/push/push-to-user#setting-user-identity)することができます。 

**主な機能**
- Azure Active Directory B2C (Azure AD B2C) を利用。 
    - エンタープライズ グレード。
    - 高可用性。
    - セキュリティで保護されたグローバルサービス。
- ご自分が使用している ID を持ち込み、Auth0 や Firebase などのその他の一般的な ID およびアクセス管理プロバイダーを使用するオプション。
- Azure Active Directory のサポート。
    - 既存の Azure AD テナントに接続します。 
    - 会社のドメインに対する認証を有効にします。
    - 機密データへのアクセスを管理します。
- シンプルなユーザー エクスペリエンスと Microsoft 認証ライブラリを Visual Studio App Center SDK. でラップすることによる魔法のような SDK エクスペリエンス。
- iOS、Android、Xamarin、React Native のプラットフォームのサポート。

**参照**
- [Visual Studio App Center にサインアップする](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [App Center Auth を使ってみる](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) は企業-消費者間 (B2C) の ID 管理サービスです。開発者はこれを使用して、自分の顧客を認証することができます。 このホワイトラベル サービスを使用することで、開発者はユーザーが自分の Web、デスクトップ、モバイル、またはシングルページ アプリケーションと安全に作用する方法をカスタマイズしたり制御したりできます。 Azure AD B2C を使用して、ユーザーは、サインアップ、サインイン、パスワードのリセット、およびプロファイルの編集を行うことができます。 Azure AD B2C では、OpenID Connect プロトコルと OAuth 2.0 プロトコルの一形式が実装されます。 

**主な機能**
- 顧客の好みの ID プロバイダーを使用して顧客を安全に認証します。
- 顧客の ID とアクセスの管理。
- Facebook、GitHub、Google、LinkedIn、Twitter、WeChat、Weibo などのソーシャル メディアのサインインがサポートされています。
- OpenID Connect、SAML などの業界標準のプロトコルを使用してユーザー アカウントにアクセスできるため、多様なプラットフォーム上で ID の管理が可能になります。
- ブランド化された登録とサインインのエクスペリエンスを提供します。
- CRM データベース、マーケティング分析ツール、アカウント検証システムと容易に統合することが可能です。
- 顧客のサインイン、ユーザー設定、および変換データをキャプチャします。

**参照**
- [Azure Portal](https://portal.azure.com/)
- [Azure AD B2C ドキュメント](/azure/active-directory-b2c/)
- [クイック スタート](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [サンプル](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) は Microsoft が提供するクラウドベースの ID およびアクセス管理サービスであり、従業員による以下のリソースへのサインインとアクセスを支援します。
- Microsoft Office 365、Azure portal、およびその他何千という サービスとしてのソフトウェア (SaaS) アプリケーションなどの外部リソース。
- 企業ネットワークとイントラネット上のアプリや、自分の組織で開発したクラウド アプリなどの内部リソース。

**主な機能**
- ユーザーが必要とするアプリケーションにユーザーを接続することによるシームレスかつ安全性の高いアクセス。
- 包括的な ID 保護と、ユーザー、位置情報、デバイス、データ、アプリ コンテキストに基づく ID およびアクセスの強化されたセキュリティ。
- 何千もの事前統合されたアプリ。Office 365、Salesforce.com、Box などの、商用アプリケーションとカスタム アプリケーションの両方があります。
- 大規模なアクセス管理機能。

**参照**
- [Azure Portal](https://portal.azure.com/)
- [Azure AD とは](/azure/active-directory/fundamentals/active-directory-whatis)
- [Azure Active Directory の概要](/azure/active-directory/fundamentals/active-directory-whatis)
- [クイック スタート](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)