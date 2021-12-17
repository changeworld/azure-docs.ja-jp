---
title: アプリケーションに対してテナント全体の管理者の同意を付与する
titleSuffix: Azure AD
description: エンド ユーザーがアプリケーションにサインインするときに同意を求めるメッセージが表示されないように、テナント全体の同意をアプリケーションに付与する方法について説明します。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/23/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8845643a7ef3816cb4bb3b2e25d511467d2bfcbb
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546597"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>アプリケーションに対してテナント全体の管理者の同意を付与する

  この記事では、Azure Active Directory (Azure AD) で、テナント全体の管理者の同意をアプリケーションに付与する方法について説明します。

テナント全体の管理者の同意をアプリケーションに付与すると、すべてのユーザーがアプリにサインインできるようになります。 アプリケーションにサインインできるユーザーを制限するには、アプリがユーザー割り当てを要求するように構成し、アプリケーションにユーザーまたはグループを割り当てます。 詳細については、[ユーザーとグループの割り当て方法](./assign-user-or-group-access-portal.md)に関するページを参照してください。

アプリへのテナント全体の管理者の同意によって、アプリおよびアプリの発行者に、組織のデータへのアクセスが付与されます。 同意を付与する前に、アプリケーションで要求されているアクセス許可をよく確認してください。 アプリケーションに同意する行為の詳細については、「[Azure Active Directory 同意フレームワーク](../develop/consent-framework.md)」を参照してください。

テナント全体に管理者の同意を与えると、以前にテナント全体に与えられていたアクセス許可が取り消されます。 以前にユーザーが自分で与えたアクセス許可は影響を受けません。

## <a name="prerequisites"></a>前提条件

テナント全体の管理者の同意を付与するには、組織を代表して同意する権限を持つユーザーとしてサインインする必要があります。

テナント全体の管理者の同意を許可するには、次が必要です。

- 次のいずれかのロールを持つ Azure アカウント: グローバル管理者、特権ロール管理者、クラウド アプリケーション管理者、またはアプリケーション管理者。 [アプリケーションにアクセス許可を付与するアクセス許可](../roles/custom-consent-permissions.md)を含む[カスタム ディレクトリ ロール](../roles/custom-create.md)が割り当てられているユーザーも、テナント全体の同意を付与することが許可されます。

## <a name="grant-tenant-wide-admin-consent-in-enterprise-apps"></a>Enterprise アプリでテナント全体の管理者の同意を付与する

テナントでアプリケーションが既にプロビジョニングされている場合、"*エンタープライズ アプリケーション*" を通じて、テナント全体の管理者の同意を付与できます。 たとえば、少なくとも 1 人のユーザーが既にアプリケーションに同意している場合は、アプリがテナントでプロビジョニングされている可能性があります。 詳細については、「[アプリケーションを Azure AD に追加する方法と理由](../develop/active-directory-how-applications-are-added.md)」を参照してください。

**[エンタープライズ アプリケーション]** に一覧表示されているアプリにテナント全体の管理者の同意を付与するには:

1. 前提条件セクションに記載されているいずれかのロールを使用して [Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]** を選択し、 **[エンタープライズ アプリケーション]** を選択します。
1. テナント全体の管理者の同意を付与するアプリケーションを選択し、 **[アクセス許可]** を選択します。
   :::image type="content" source="media/grant-tenant-wide-admin-consent/grant-tenant-wide-admin-consent.png" alt-text="テナント全体の管理者の同意を付与する方法を示すスクリーンショット。":::

1. アプリケーションに必要なアクセス許可を慎重に確認します。 アプリケーションで必要なアクセス許可に同意する場合は、 **[管理者の同意の付与]** を選択します。

## <a name="grant-admin-consent-in-app-registrations"></a>アプリの登録で管理者の同意を付与する

組織で開発したアプリケーション、または Azure AD テナントに直接登録されているアプリケーションの場合は、Azure portal の **[アプリの登録]** から、テナント全体の管理者の同意を付与することもできます。

**[アプリの登録]** からテナント全体の管理者の同意を付与するには:

1. 前提条件セクションに記載されているいずれかのロールを使用して [Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]** を選択し、 **[アプリの登録]** を選択します。
1. テナント全体の管理者の同意を付与するアプリケーションを選択します。
1. **[API のアクセス許可]** を選択します。
1. アプリケーションに必要なアクセス許可を慎重に確認します。 同意する場合は、 **[管理者の同意の付与]** を選択します。

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>テナント全体の管理者の同意を付与するための URL を作成する

上記のいずれかの方法を使用してテナント全体の管理者の同意を付与すると、Azure portal でウィンドウが開き、テナント全体の管理者の同意を求めるメッセージが表示されます。 アプリケーションのクライアント ID (アプリケーション ID とも呼ばれます) がわかっている場合は、同じ URL を作成して、テナント全体の管理者の同意を付与することができます。

テナント全体の管理者の同意の URL は、次のような形式です。

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

各値の説明:

- `{client-id}` は、アプリケーションのクライアント ID (アプリ ID とも呼ばれます) です。
- `{tenant-id}` は、組織のテナント ID または検証済みのドメイン名です。

この場合も、同意を付与する前に、アプリケーションで要求されているアクセス許可をよく確認してください。

## <a name="next-steps"></a>次のステップ

[Azure Active Directory でエンド ユーザーがアプリケーションに同意する方法を構成する](configure-user-consent.md)

[管理者の同意ワークフローの構成](configure-admin-consent-workflow.md)
