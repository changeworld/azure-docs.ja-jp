---
title: Azure AD アプリのリンクされたサインオン - Microsoft ID プラットフォーム
description: Microsoft ID プラットフォーム (Azure AD) で Azure AD エンタープライズ アプリケーションに、リンクされたシングル サインオン (SSO) を構成する
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfe4aeb17f482cc9d4126efc6d65d3f7d173536b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063545"
---
# <a name="configure-linked-sign-on"></a>リンクされたサインオンの構成

ギャラリーまたはギャラリー以外の Web アプリケーションを追加するときに使用できるシングル サインオン オプションの 1 つが、[リンクされたサインオン](what-is-single-sign-on.md)です。 組織の Azure AD アクセス パネルまたは Office 365 ポータルにアプリケーションへのリンクを追加するには、このオプションを選択します。 この方法を使用して、認証用に Azure AD の代わりに現在 Active Directory フェデレーション サービス (AD FS) (または他のフェデレーション サービス) を使用しているカスタム Web アプリケーションへのリンクを追加することができます。 または、ユーザーのアクセス パネルに表示するだけの特定の SharePoint ページまたは他の Web ページにディープ リンクを追加することもできます。

## <a name="before-you-begin"></a>開始する前に

アプリケーションが Azure AD テナントに追加されていない場合は、[ギャラリー アプリの追加](add-gallery-app.md)に関するページまたは[ギャラリー以外のアプリの追加](add-non-gallery-app.md)に関するページを参照してください。

### <a name="open-the-app-and-select-linked-sign-on"></a>アプリを開き、リンクされたサインオンを選択する

1. クラウド アプリケーション管理者か Azure AD テナントのアプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。

1. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に移動します。 ご自分の Azure AD テナント内にあるアプリケーションのランダム サンプルが表示されます。 

1. **[アプリケーションの種類]** メニューの **[すべてのアプリケーション]** を選択した後、 **[適用]** を選択します。

1. 検索ボックスにアプリケーションの名前を入力し、結果からアプリケーションを選択します。

1. **[管理]** セクションの **[シングル サインオン]** を選択します。 

1. **[リンク済み]** を選択します。

1. リンク先のアプリケーションの URL を入力します。 URL を入力し、 **[保存]** を選択します。 
 
1. ユーザーとグループをアプリケーションに割り当てることができます。これにより、アプリケーションが [Office 365 アプリ ランチャー](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/)またはユーザーの [Azure AD アクセス パネル](end-user-experiences.md)に表示されるようになります。

1. **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [アプリケーションにユーザーまたはグループを割り当てる](methods-for-assigning-users-and-groups.md)
- [自動ユーザー アカウント プロビジョニングを構成する](../app-provisioning/configure-automatic-user-provisioning-portal.md)
