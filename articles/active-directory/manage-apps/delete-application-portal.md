---
title: 'クイックスタート: エンタープライズ アプリケーションを削除する'
description: Azure Active Directory でエンタープライズ アプリケーションを削除します。
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
ms.openlocfilehash: c2ee107815186ac7877056ac8cec4954d30de747
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547528"
---
# <a name="quickstart-delete-an-enterprise-application"></a>クイックスタート: エンタープライズ アプリケーションを削除する

このクイックスタートでは、Azure Active Directory 管理センターを使用し、Azure Active Directory (Azure AD) テナントに追加されたアプリケーションを削除します。

このクイックスタートの手順をテストするには、非運用環境を使用することをお勧めします。

## <a name="prerequisites"></a>前提条件

エンタープライズ アプリケーションを削除するには、次のものが必要になります。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。
- [エンタープライズ アプリケーションを追加するクイックスタート](add-application-portal.md)の手順を完了していること。

## <a name="delete-an-enterprise-application"></a>エンタープライズ アプリケーションの削除

エンタープライズ アプリケーションを削除するには:

1. [Azure Active Directory 管理センター](https://aad.portal.azure.com)に移動し、前提条件の一覧で示されているロールのいずれかを使用してサインインします。
1. 左側のメニューで、 **[エンタープライズ アプリケーション]** を選択します。 **[すべてのアプリケーション]** ペインが開き、Azure AD テナントのアプリケーションの一覧が表示されます。 削除するアプリケーションを検索して選択します。 たとえば、**Azure AD SAML Toolkit 1** などです。
1. 左側のメニューの **[管理]** セクションで **[プロパティ]** を選択します。
1. **[プロパティ]** ペインの上部で、 **[削除]** を選択してから **[はい]** を選択し、Azure AD テナントからアプリケーションを削除することを確定します。

    :::image type="content" source="media/delete-application-portal/delete-application.png" alt-text="エンタープライズ アプリケーションを削除します。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタート シリーズを完了したら、アプリケーションを削除してテスト テナントをクリーンアップすることを検討してください。 アプリケーションの削除については、このクイックスタートで説明しました。

## <a name="next-steps"></a>次のステップ

シングル サインオン デプロイの計画について詳しく学習します。
> [!div class="nextstepaction"]
> [シングル サインオンのデプロイの計画](plan-sso-deployment.md)
