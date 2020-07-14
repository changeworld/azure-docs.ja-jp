---
title: クイック スタート:Azure Active Directory (Azure AD) テナントからアプリケーションを削除する
description: このクイックスタートでは、Azure portal を使用して、Azure Active Directory (Azure AD) テナントからアプリケーションを削除します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02d12538fce5ccc3905ea1170fc1a8324309004f
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956000"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>クイック スタート:Azure Active Directory (Azure AD) テナントからアプリケーションを削除する

このクイックスタートでは、Azure portal を使用して、Azure AD テナントに追加されたアプリケーションを削除します。

## <a name="prerequisites"></a>前提条件

Azure AD テナントからアプリケーションを削除するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。
- (省略可能: [アプリの表示](view-applications-portal.md)の完了)。
- (省略可能: [アプリの追加](add-application-portal.md)の完了)。
- (省略可能: [アプリの構成](add-application-portal-configure.md)の完了)。
- (省略可能: [シングル サインオンの設定](add-application-portal-setup-sso.md)の完了)。

>[!IMPORTANT]
>このクイックスタートの手順をテストする場合は、非運用環境を使用することをお勧めします。

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Azure AD テナントからアプリケーションを削除する

Azure AD テナントからアプリケーションを削除するには:

1. Azure AD ポータルで、 **[エンタープライズ アプリケーション]** を選んでから、削除するアプリケーションを見つけて選択します。 この場合、前のクイックスタートで追加した GitHub_test アプリケーションを削除します。
2. ナビゲーションの [管理] セクションで、 **[プロパティ]** を選択します。
3. [削除] を選択してから [はい] を選び、Azure AD テナントからアプリを削除することを確定します。


## <a name="next-steps"></a>次のステップ

- [アプリケーション管理のベスト プラクティス](application-management-fundamentals.md)
- [アプリケーション管理の一般的なシナリオ](common-scenarios.md)
- [アプリケーション管理の可視性と制御](cloud-app-security.md)