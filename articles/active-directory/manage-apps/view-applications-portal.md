---
title: 'クイックスタート: エンタープライズ アプリケーションを表示する'
description: Azure Active Directory テナントを使用するために登録されているエンタープライズ アプリケーションを表示します。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 09/07/2021
ms.author: davidmu
ms.reviewer: arvinh
ms.openlocfilehash: 4da70b29fb7d5a8920db05e0d7c28f5c29a101fd
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553137"
---
# <a name="quickstart-view-enterprise-applications"></a>クイックスタート: エンタープライズ アプリケーションを表示する

このクイックスタートでは、Azure Active Directory 管理センターを使用して、Azure Active Directory (Azure AD) テナントで既に構成されているエンタープライズ アプリケーションを検索して表示する方法について説明します。

このクイックスタートの手順をテストするには、非運用環境を使用することをお勧めします。

## <a name="prerequisites"></a>前提条件

Azure AD テナントに登録されているアプリケーションを表示するには、次のものが必要です。

- Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。
- [エンタープライズ アプリケーションを追加するクイックスタート](add-application-portal.md)の手順を完了していること。

## <a name="view-a-list-of-applications"></a>アプリケーションの一覧を表示する

テナントに登録されているエンタープライズ アプリケーションを表示するには、次のようにします。

1. [Azure Active Directory 管理センター](https://aad.portal.azure.com)に移動し、前提条件の一覧で示されているロールのいずれかを使用してサインインします。
1. 左側のメニューで、 **[エンタープライズ アプリケーション]** を選択します。 **[すべてのアプリケーション]** ペインが開き、Azure AD テナントのアプリケーションの一覧が表示されます。

    :::image type="content" source="media/view-applications-portal/view-enterprise-applications.png" alt-text="Azure AD テナント内の登録済みアプリケーションを表示します。":::

1. 他のアプリケーションを表示するには、一覧の一番下にある **[さらに読み込む]** を選択します。 テナント内に多数のアプリケーションがある場合は、一覧をスクロールするよりも特定のアプリケーションを検索する方が簡単な場合があります。

## <a name="search-for-an-application"></a>アプリケーションを検索する

特定のアプリケーションを検索するには:

1. **[アプリケーションの種類]** メニューの **[すべてのアプリケーション]** を選択し、 **[適用]** を選択します。
1. 検索するアプリケーションの名前を入力します。 アプリケーションが Azure AD テナントに追加されている場合は、それが検索結果に表示されます。 たとえば、前のクイックスタートで使用されている **Azure AD SAML Toolkit 1** のアプリケーションを検索できます。 
1. アプリケーション名の最初の数文字を入力してください。

## <a name="select-viewing-options"></a>表示オプションを選択する

探しているものに応じてオプションを選択します。

1. **[アプリケーションの種類]** 、 **[アプリケーションの状態]** 、 **[アプリケーションの可視性]** によってアプリケーションを表示できます。
1. **[アプリケーションの種類]** で、次のいずれかのオプションを選択します。
    - **[エンタープライズ アプリケーション]** には、Microsoft 以外のアプリケーションが表示されます。
    - **[Microsoft アプリケーション]** には、Microsoft アプリケーションが表示されます。
    - **[すべてのアプリケーション]** には、Microsoft 以外のアプリケーションと Microsoft アプリケーションの両方が表示されます。
1. **[アプリケーションの状態]** で **[任意]** 、 **[無効]** 、または **[有効]** を選択します。 **[任意]** オプションでは、無効なアプリケーションと有効なアプリケーションの両方が含まれます。
1. **[アプリケーションの可視性]** で、 **[任意]** または **[非表示]** を選択します。 **[非表示]** オプションは、テナントに存在するにもかかわらずユーザーには表示されないアプリケーションを示します。
1. 必要なオプションを選択したら、 **[適用]** を選択します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クイックスタート全体で使用された **Azure AD SAML Toolkit 1** という名前のテスト アプリケーションを作成した場合は、今すぐ削除してテナントをクリーンアップすることができます。 詳細については、[アプリケーションの削除](delete-application-portal.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

エンタープライズ アプリケーションの削除方法について学習します。
> [!div class="nextstepaction"]
> [アプリケーションの削除](add-application-portal.md)
