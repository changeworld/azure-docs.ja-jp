---
title: 最近削除されたユーザーを復元するか完全に削除する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory を使用して、復元可能なユーザーを表示する方法、削除されたユーザーを復元する方法、およびユーザーを完全に削除する方法。
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: 066c9189e0c3cea409a99217e0ab4b46002df1f9
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556783"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Azure Active Directory を使用して最近削除されたユーザーを復元または削除する
ユーザーを削除した後、アカウントは 30 日間、中断状態のままになります。 その 30 日の期間中は、ユーザー アカウントをそのすべてのプロパティと共に復元することができます。 30 日の期間が経過すると、ユーザーは自動的に完全に削除されます。

Azure portal 内で Azure Active Directory (Azure AD) を使用して、復元可能なユーザーを表示したり、削除されたユーザーを復元したり、ユーザーを完全に削除したりすることができます。

>[!Important]
>管理者も Microsoft カスタマー サポートも、完全に削除されたユーザーを復元することはできません。

## <a name="required-permissions"></a>必要なアクセス許可
ユーザーの復元または完全な削除を行うには、次のいずれかのロールが必要です。

- 会社の管理者

- パートナー レベル 1 のサポート

- パートナー レベル 2 のサポート

- ユーザー アカウント管理者

## <a name="view-your-restorable-users"></a>復元可能なユーザーを表示する
削除されてからまだ 30 日が経過していないユーザーはすべて表示することができます。 これらのユーザーは復元することができます。

### <a name="to-view-your-restorable-users"></a>復元可能なユーザーを表示するには
1. ディレクトリの全体管理者アカウントを使用して、[Azure portal](https://portal.azure.com/) にサインインします。

2. **[Azure Active Directory]**、**[ユーザー]**、**[削除済みのユーザー]** の順に選択します。

    復元可能なユーザーの一覧を確認します。

    ![まだ復元可能なユーザーが表示されている [ユーザー - 削除済みのユーザー] ページ](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>最近削除されたユーザーを復元する
ユーザーのアカウントが中断されている間は、関連するディレクトリ情報がすべて保持されます。 ユーザーを復元すると、このディレクトリ情報も復元されます。

### <a name="to-restore-a-user"></a>ユーザーを復元するには
1. **[ユーザー - 削除済みユーザー]** ページ上で、利用可能なユーザーを検索し、その中のいずれかを選択します。 たとえば、_Mary Parker_ を選択します。

2. **[ユーザーの復元]** を選びます。

    ![[ユーザー - 削除済みユーザー] ページ上で強調表示されている [ユーザーの復元] オプション](media/active-directory-users-restore/users-deleted-users-restore-user.png)

>[!NOTE]
>これまでは、オンプレミスの同期範囲からユーザーが削除され、クラウドでも削除された場合、アカウントの DirSyncEnabled 状態が誤って "False" に設定されていました。 その後、そのユーザーが Azure AD のごみ箱から手動で復元された場合は、"Cloud-Only" という正しくない状態のアカウントとして表示されていました。 現在はこれは修正され、ユーザーが同期範囲から削除され、論理的に削除された後、Azure AD のごみ箱から手動で復元された場合でも、DirSyncEnabled 状態は、常に "True" で維持されるようになりました。

## <a name="permanently-delete-a-user"></a>ユーザーの完全な削除
30 日経過してから自動削除されるのを待つことなく、ご利用のディレクトリからユーザーを完全に削除することができます。 完全に削除されたユーザーは、担当の管理者も、別の管理者も、Microsoft カスタマー サポートも復元することができません。

>[!Note]
>誤ってユーザーを完全に削除してしまった場合は、新しいユーザーを作成して、以前の情報をすべて手動で入力する必要があります。 新しいユーザーの作成の詳細については、[ユーザーの追加または削除](add-users-azure-active-directory.md)に関するページを参照してください。

### <a name="to-permanently-delete-a-user"></a>ユーザーを完全に削除するには

1. **[ユーザー - 削除済みユーザー]** ページ上で、利用可能なユーザーを検索し、その中のいずれかを選択します。 たとえば、_Rae Huff_ を選択します。

2. **[完全に削除]** を選びます。

    ![[ユーザー - 削除済みユーザー] ページ上で強調表示されている [ユーザーの復元] オプション](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>次の手順
ユーザーを復元または削除したら、次の基本的なプロセスを実行することができます。

- [ユーザーを追加または削除する](add-users-azure-active-directory.md)

- [ユーザーにロールを割り当てる](active-directory-users-assign-role-azure-portal.md)

- [プロファイル情報を追加または変更する](active-directory-users-profile-azure-portal.md)

- [別のディレクトリからのゲスト ユーザーの追加](../b2b/what-is-b2b.md) 

他の使用可能なユーザー管理タスクの詳細については、「[Azure Active Directory のユーザー管理のドキュメント](../users-groups-roles/index.yml)」をご覧ください。
