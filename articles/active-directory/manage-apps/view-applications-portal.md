---
title: クイック スタート:Azure Active Directory (Azure AD) テナントを使用して ID 管理を行っているアプリケーションの一覧を表示する
description: このクイックスタートでは、Azure portal を使用して、Azure Active Directory (Azure AD) テナントを ID 管理に使用するように登録されているアプリケーションの一覧を表示します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 249cdbef6214eb8c66da841d9eae817bcf794996
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86055394"
---
# <a name="quickstart-view-the-list-of-applications-that-are-using-your-azure-active-directory-azure-ad-tenant-for-identity-management"></a>クイック スタート:Azure Active Directory (Azure AD) テナントを使用して ID 管理を行っているアプリケーションの一覧を表示する

Azure AD を、ご自分の組織で使用するアプリケーションの Identity and Access Management (IAM) システムとして使用開始する方法を説明します。 このクイックスタートでは、Azure AD テナントを ID プロバイダーとして使用するように既に設定されているアプリケーション (アプリとも呼ばれます) を表示します。

## <a name="prerequisites"></a>前提条件

Azure AD テナントに登録されているアプリケーションを表示するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

>[!IMPORTANT]
>このクイックスタートの手順をテストする場合は、非運用環境を使用することをお勧めします。

## <a name="find-the-list-of-applications-in-your-tenant"></a>テナント内のアプリケーションの一覧を検索する

Azure AD テナントに登録されたアプリケーションは、Azure portal の **[エンタープライズ アプリ]** セクションで確認できます。

テナントに登録されているアプリケーションを表示するには、次のようにします。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。
3. **[Azure Active Directory]** ウィンドウで、 **[エンタープライズ アプリケーション]** を選択します。
4. **[アプリケーションの種類]** ドロップダウン メニューの **[すべてのアプリケーション]** を選択し、 **[適用]** を選択します。 テナント アプリケーションのランダム サンプルが表示されます。
5. 他のアプリケーションを表示するには、一覧の一番下にある **[さらに読み込む]** を選択します。 テナント内に多数のアプリケーションがある場合は、一覧をスクロールするよりも特定のアプリケーションを検索する方が簡単な場合があります。 特定のアプリケーションの検索については、このクイックスタートで後ほど説明します。

## <a name="select-viewing-options"></a>表示オプションを選択する

探しているものに応じてオプションを選択します。

1. **[アプリケーションの種類]** 、 **[アプリケーションの状態]** 、 **[アプリケーションの可視性]** によってアプリケーションを表示できます。
2. **[アプリケーションの種類]** で、次のいずれかのオプションを選択します。
    - **[エンタープライズ アプリケーション]** には、Microsoft 以外のアプリケーションが表示されます。
    - **[Microsoft アプリケーション]** には、Microsoft アプリケーションが表示されます。
    - **[すべてのアプリケーション]** には、Microsoft 以外のアプリケーションと Microsoft アプリケーションの両方が表示されます。
3. **[アプリケーションの状態]** で **[任意]** 、 **[無効]** 、または **[有効]** を選択します。 **[任意]** オプションでは、無効なアプリケーションと有効なアプリケーションの両方が含まれます。
4. **[アプリケーションの可視性]** で、 **[任意]** または **[非表示]** を選択します。 **[非表示]** オプションは、テナントに存在するにもかかわらずユーザーには表示されないアプリケーションを示します。
5. 必要なオプションを選択したら、 **[適用]** を選択します。

## <a name="search-for-an-application"></a>アプリケーションを検索する

特定のアプリケーションを検索するには:

1. **[アプリケーションの種類]** メニューの **[すべてのアプリケーション]** を選択し、 **[適用]** を選択します。
2. 検索するアプリケーションの名前を入力します。 アプリケーションが Azure AD テナントに追加されている場合は、それが検索結果に表示されます。 この例は、GitHub がテナント アプリケーションに追加されていないことを示しています。
    ![テナントにアプリが追加されていないことを表示する例](media/view-applications-portal/search-for-tenant-application.png)
3. アプリケーション名の最初の数文字を入力してください。 この例は、**Sales** で始まるすべてのアプリケーションを示しています。
    ![Sales で始まるすべてのアプリを表示する例](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure AD テナント内でアプリケーションを表示する方法について学習しました。 アプリケーションの種類、状態、可視性によって、アプリケーションの一覧をフィルター処理する方法を学習しました。 また、特定のアプリケーションを検索する方法についても学習しました。

- [アプリを追加する](add-application-portal.md)
- [アプリを構成する](add-application-portal-configure.md)
- [シングル サインオンを設定する](add-application-portal-setup-sso.md)
- [アプリを削除する](delete-application-portal.md)
