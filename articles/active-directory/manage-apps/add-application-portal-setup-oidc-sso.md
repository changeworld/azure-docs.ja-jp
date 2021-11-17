---
title: OpenID Connect ベースのシングル サインオン アプリケーションを追加する
description: Azure Active Directory で OpenID Connect ベースのシングル サインオン アプリケーションを追加する方法について説明します。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: 02bbc4d6b0cc6f346111a8cd0145ef099dd33feb
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552016"
---
# <a name="add-an-openid-connect-based-single-sign-on-application"></a>OpenID Connect ベースのシングル サインオン アプリケーションを追加する

Azure Active Directory (Azure AD) テナントに対する [OpenID Connect (OIDC)](../develop/active-directory-v2-protocols.md) ベースのシングル サインオン (SSO) をサポートするアプリケーションを追加します。

このページの手順をテストするには、非運用環境を使用することをお勧めします。

## <a name="prerequisites"></a>前提条件

OIDC ベースの SSO を構成するには、次が必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。

## <a name="add-the-application"></a>アプリケーションを追加する

SSO 向けの OIDC 標準を使用するエンタープライズ アプリケーションを追加するときに、セットアップ ボタンを選択します。 このボタンを選択すると、アプリケーションのサインアップ プロセスが完了します。

OIDC ベースの SSO をアプリケーション用に構成するには、次のようにします。

1. [Azure Active Directory 管理センター](https://aad.portal.azure.com)に移動し、前提条件の一覧で示されているロールのいずれかを使用してサインインします。
1. 左側のメニューで、 **[エンタープライズ アプリケーション]** を選択します。 **[すべてのアプリケーション]** ペインが開き、Azure AD テナントのアプリケーションの一覧が表示されます。 
1. **[エンタープライズ アプリケーション]** ペインで、 **[新しいアプリケーション]** を選択します。
1. **[Azure AD ギャラリーの参照]** ペインが開き、クラウド プラットフォーム、オンプレミス アプリケーション、および注目のアプリケーションのタイルが表示されます。 **[注目のアプリケーション]** セクションに一覧表示されているアプリケーションには、フェデレーション SSO とプロビジョニングをサポートしているかどうかを示すアイコンがあります。 アプリケーションを検索して選択します。 この例では、**SmartSheet** が使用されています。
1. **[サインアップ]** を選択します。 Azure Active Directory から、ユーザー アカウントの資格情報でサインインします。 アプリケーションのサブスクリプションを既に持っている場合は、ユーザーの詳細およびテナント情報が検証されます。 アプリケーションがユーザーを確認できない場合は、アプリケーション サービスへのサインアップにリダイレクトされます。

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/oidc-sso-configuration.png" alt-text="アプリケーションの同意画面を完了します。":::

1. **[組織の代理として同意する]** を選択して、 **[同意する]** を選択します。 アプリケーションがテナントに追加され、アプリケーションのホーム ページが表示されます。 ユーザーおよび管理者の同意の詳細については、「[ユーザーおよび管理者の同意について](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)」を参照してください。

## <a name="next-steps"></a>次のステップ

シングル サインオン デプロイの計画について詳しく学習します。
> [!div class="nextstepaction"]
> [シングル サインオンのデプロイの計画](plan-sso-deployment.md)
