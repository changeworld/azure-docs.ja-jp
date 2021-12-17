---
title: 'クイックスタート: エンタープライズ アプリケーションを追加する'
description: Azure Active Directory でエンタープライズ アプリケーションを追加します。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergleenl
ms.openlocfilehash: f3d9cd36b821b1dca5311c266b84e259eab005c0
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546958"
---
# <a name="quickstart-add-an-enterprise-application"></a>クイックスタート: エンタープライズ アプリケーションを追加する

このクイックスタートでは、Azure Active Directory 管理センターを使用し、Azure Active Directory (Azure AD) テナントにエンタープライズ アプリケーションを追加します。 Azure AD にはあらかじめ統合された何千ものエンタープライズ アプリケーションが含まれるギャラリーがあります。 組織で使用しているアプリケーションの多くは、おそらく既にギャラリーにあります。 このクイックスタートでは、例として **Azure AD SAML Toolkit** というアプリケーションを使用しますが、この概念は [ギャラリーのエンタープライズ アプリケーション](../saas-apps/tutorial-list.md)のほとんどに適用されます。

このクイックスタートの手順をテストするには、非運用環境を使用することをお勧めします。

## <a name="prerequisites"></a>前提条件

Azure AD テナントにエンタープライズ アプリケーションを追加するには、次が必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。

## <a name="add-an-enterprise-application"></a>エンタープライズ アプリケーションの追加

テナントにエンタープライズ アプリケーションを追加するには、次が必要です。

1. [Azure Active Directory 管理センター](https://aad.portal.azure.com)に移動し、前提条件の一覧で示されているロールのいずれかを使用してサインインします。
1. 左側のメニューで、 **[エンタープライズ アプリケーション]** を選択します。 **[すべてのアプリケーション]** ペインが開き、Azure AD テナントのアプリケーションの一覧が表示されます。
1. **[エンタープライズ アプリケーション]** ペインで、 **[新しいアプリケーション]** を選択します。
1. **[Azure AD ギャラリーの参照]** ペインが開き、クラウド プラットフォーム、オンプレミス アプリケーション、および注目のアプリケーションのタイルが表示されます。 **[注目のアプリケーション]** セクションに一覧表示されているアプリケーションには、フェデレーション シングル サインオン (SSO) とプロビジョニングをサポートしているかどうかを示すアイコンがあります。 アプリケーションを検索して選択します。 このクイックスタートでは、**Azure AD SAML Toolkit** が使用されています。

    :::image type="content" source="media/add-application-portal/browse-gallery.png" alt-text="追加するアプリケーションのエンタープライズ アプリケーション ギャラリーを参照します。":::

1. アプリケーションのインスタンスを識別するために使用する名前を入力します。 たとえば、「 `Azure AD SAML Toolkit 1` 」のように入力します。
1. **［作成］** を選択します

OpenID Connect ベースの SSO を使用するアプリケーションのインストールを選択した場合、 **[作成]** ボタンを表示するのではなく、アカウントが既にあるかどうかに応じて、アプリケーションのサインインまたはサインアップ ページにリダイレクトするボタンが表示されます。 詳細については、[OpenID Connect ベースのシングル サインオン アプリケーションの追加](add-application-portal-setup-oidc-sso.md)に関する記事を参照してください。 サインインすると、アプリケーションがテナントに追加されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のクイックスタートを行う予定の場合は、作成したエンタープライズ アプリケーションをそのままにします。 そうでない場合は、削除してテナントをクリーンアップしてもかまいません。 詳細については、[アプリケーションの削除](delete-application-portal.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

ユーザー アカウントを作成し、追加したエンタープライズ アプリケーションに割り当てる方法について説明します。
> [!div class="nextstepaction"]
> [ユーザー アカウントを作成して割り当てる](add-application-portal-assign-users.md)
