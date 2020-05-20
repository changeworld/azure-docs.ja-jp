---
title: アプリケーションに対してテナント全体の管理者の同意を付与する - Azure AD
description: エンド ユーザーがアプリケーションにサインインするときに同意を求めるメッセージが表示されないように、テナント全体の同意をアプリケーションに付与する方法について説明します。
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: c515fef4997720435c64bd5f3ae7b18f8921fc5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75475273"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>アプリケーションに対してテナント全体の管理者の同意を付与する

アプリケーションに対してテナント全体の管理者の同意を付与することで、ユーザー エクスペリエンスを簡略化する方法について説明します。 この記事では、さまざまな方法でこれを実現します。 この方法は、Azure Active Directory (Azure AD) テナントのすべてのエンド ユーザーに適用されます。

アプリケーションに同意する行為の詳細については、「[Azure Active Directory 同意フレームワーク](../develop/consent-framework.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

テナント全体の管理者の同意を付与するには、[グローバル管理者](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)、[アプリケーション管理者](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)、または[クラウド アプリケーション管理者](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)としてサインインする必要があります。

> [!IMPORTANT]
> アプリケーションにテナント全体の管理者の同意が付与されていると、ユーザーの割り当てが必要であると構成されていない限り、すべてのユーザーがアプリにサインインできるようになります。 アプリケーションにサインインできるユーザーを制限するには、ユーザーの割り当てを必要にした後、アプリケーションにユーザーまたはグループを割り当てます。 詳細については、[ユーザーとグループの割り当て方法](methods-for-assigning-users-and-groups.md)に関するページを参照してください。

> [!WARNING]
> テナント全体の管理者の同意をアプリケーションに付与すると、アプリおよびアプリの発行者に、組織のデータへのアクセスが許可されます。 同意を付与する前に、アプリケーションで要求されているアクセス許可をよく確認してください。

## <a name="grant-admin-consent-from-the-azure-portal"></a>Azure portal から管理者の同意を付与する

### <a name="grant-admin-consent-in-enterprise-apps"></a>エンタープライズ アプリで管理者の同意を付与する

テナントでアプリケーションが既にプロビジョニングされている場合、"*エンタープライズ アプリケーション*" を通じて、テナント全体の管理者の同意を付与できます。 たとえば、少なくとも 1 人のユーザーが既にアプリケーションに同意している場合は、アプリがテナントでプロビジョニングされている可能性があります。 詳細については、「[アプリケーションを Azure AD に追加する方法と理由](../develop/active-directory-how-applications-are-added.md)」を参照してください。

**[エンタープライズ アプリケーション]** に一覧表示されているアプリにテナント全体の管理者の同意を付与するには:

1. [グローバル管理者](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)、[アプリケーション管理者](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)、または[クラウド アプリケーション管理者](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)として、[Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]** を選択し、 **[エンタープライズ アプリケーション]** を選択します。
3. テナント全体の管理者の同意を付与するアプリケーションを選択します。
4. **[アクセス許可]** を選択し、 **[管理者の同意の付与]** をクリックします。
5. アプリケーションに必要なアクセス許可を慎重に確認します。
6. アプリケーションで必要なアクセス許可に同意する場合は、同意を付与します。 そうでない場合は、 **[キャンセル]** をクリックするか、ウィンドウを閉じます。

### <a name="grant-admin-consent-in-app-registrations"></a>アプリの登録で管理者の同意を付与する

組織で開発したアプリケーション、または Azure AD テナントに直接登録されているアプリケーションの場合は、Azure portal の **[アプリの登録]** から、テナント全体の管理者の同意を付与することもできます。

**[アプリの登録]** からテナント全体の管理者の同意を付与するには:

1. [グローバル管理者](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)、[アプリケーション管理者](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)、または[クラウド アプリケーション管理者](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)として、[Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]** を選択し、 **[アプリの登録]** を選択します。
3. テナント全体の管理者の同意を付与するアプリケーションを選択します。
4. **[API のアクセス許可]** を選択し、 **[管理者の同意を与える]** をクリックします。
5. アプリケーションに必要なアクセス許可を慎重に確認します。
6. アプリケーションで必要なアクセス許可に同意する場合は、同意を付与します。 そうでない場合は、 **[キャンセル]** をクリックするか、ウィンドウを閉じます。

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>テナント全体の管理者の同意を付与するための URL を作成する

上記のいずれかの方法を使用してテナント全体の管理者の同意を付与すると、Azure portal でウィンドウが開き、テナント全体の管理者の同意を求めるメッセージが表示されます。 アプリケーションのクライアント ID (アプリケーション ID とも呼ばれます) がわかっている場合は、同じ URL を作成して、テナント全体の管理者の同意を付与することができます。

テナント全体の管理者の同意の URL は、次のような形式です。

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

各値の説明:

* `{client-id}` は、アプリケーションのクライアント ID (アプリ ID とも呼ばれます) です。
* `{tenant-id}` は、組織のテナント ID または検証済みのドメイン名です。

この場合も、同意を付与する前に、アプリケーションで要求されているアクセス許可をよく確認してください。

## <a name="next-steps"></a>次のステップ

[エンド ユーザーがアプリケーションに同意する方法の構成](configure-user-consent.md)

[管理者の同意ワークフローの構成](configure-admin-consent-workflow.md)

[Microsoft ID プラットフォームでのアクセス許可と同意](../develop/active-directory-v2-scopes.md)

[StackOverflow での Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
