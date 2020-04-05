---
title: アンマネージド Azure AD の職場または学校アカウントを削除する
description: アンマネージド Azure Active Directory のご自身の職場または学校アカウントを削除する方法。
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 05/20/2019
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c101c0ef7932151e675c5c514ac558e6e0f94b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73815715"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>アンマネージド Azure Active Directory の職場または学校アカウントを削除する

ご自身がアンマネージド Azure Active Directory (Azure AD) 組織のユーザーで、その組織のアプリを利用する必要がなくなった場合、または関連付けを維持する必要がなくなった場合、お使いのアカウントはいつでも削除できます。 アンマネージド ディレクトリには、グローバル管理者がいません。 アンマネージド ディレクトリのユーザーは自分のアカウントをご自身で削除できます。その際、管理者に連絡する必要はありません。

アンマネージド ディレクトリのユーザーは、多くの場合、セルフサービス サインアップ中に作成されます。 たとえば、無料サービスにサインアップした組織のインフォメーション ワーカーが、このユーザーに該当する可能性があります。 セルフサービス サインアップの詳細については、 ("バイラル") サインアップの詳細については、「[Azure Active Directory のセルフサービス サインアップについて](directory-self-service-signup.md)」を参照してください。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>開始する前に

ご自身のアカウントを削除するには、事前に次の項目を確認しておく必要があります。

* ご自身がアンマネージド Azure AD ディレクトリのユーザーであることを確認してください。 マネージド ディレクトリのユーザーの場合、自分のアカウントを削除することはできません。 マネージド ディレクトリのユーザーがアカウントを削除する必要がある場合は、管理者に連絡する必要があります。 ご自身がアンマネージド ディレクトリのユーザーかどうかを確認する方法については、「[アンマネージド テナントからユーザーを削除する](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)」を参照してください。

* 保持する必要があるすべてのデータを保存します。 エクスポート要求を送信する方法については、「[Accessing and exporting system-generated logs for Unmanaged Tenants (アンマネージド テナントのシステム生成ログへのアクセスとエクスポート)](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)」を参照してください。

> [!WARNING]
> 一度アカウントを削除すると、元に戻すことはできません。 ご自身のアカウントを削除すると、すべての個人データが削除され、 ご自身のアカウントやそのアカウントに関連付けられているデータにアクセスできなくなります。

## <a name="close-your-account"></a>アカウントを削除する

職場または学校のアンマネージド アカウントを削除するには、次の手順を実行します。

1. 削除するアカウントを使用してサインインし、[ご自身のアカウントを削除](https://go.microsoft.com/fwlink/?linkid=873123)します。

1. **[データ要求]** で **[アカウントの削除]** を選択します。

    ![データ要求 - アカウントの削除](./media/users-close-account/close-account.png)

1. 確認メッセージを確認して、 **[はい]** を選択します。

    ![データ要求 - 削除の確認](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory のセルフサービス サインアップについて](directory-self-service-signup.md)
- [アンマネージド テナントからユーザーを削除する](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [アンマネージド テナントのシステム生成ログへのアクセスとエクスポート](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
