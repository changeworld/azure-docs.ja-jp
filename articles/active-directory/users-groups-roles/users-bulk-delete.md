---
title: Azure Active Directory ポータルでユーザーを一括削除する (プレビュー) | Microsoft Docs
description: Azure Active Directory の Azure 管理センターでユーザーを一括削除する
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb01b46d61b6ba99c3ec9c537dccc350074f5e05
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146424"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Azure Active Directory でユーザーを一括削除する (プレビュー)

Azure Active Directory (Azure AD) ポータルでは、コンマ区切り値 (CSV) ファイルを使用してユーザーを一括削除することで、グループの多数のメンバーを削除できます。

## <a name="to-bulk-delete-users"></a>ユーザーを一括削除するには

1. 組織のユーザー管理者アカウントで、ご自身の Azure AD 組織にサインインします。
1. Azure AD で、 **[ユーザー]**  >  **[一括削除]** の順に選択します。
1. **[ユーザーの一括削除]** ページで **[ダウンロード]** を選択し、ユーザー プロパティの有効な CSV ファイルを受信し、削除するユーザーを追加します。

   ![CSV ファイルには、削除するユーザーの名前と ID が含まれています。](./media/users-bulk-delete/delete-csv-file.png)

1. CSV ファイルの編集が完了したら、 **[csv ファイルをアップロードします]** の下から検証するファイルを選択します。

   ![削除するユーザーをリストするローカル CSV ファイルを選択する](./media/users-bulk-delete/bulk-delete.png)

1. ファイルの内容が検証されたら、ジョブを送信する前にエラーをすべて修正する必要があります。
1. ファイルの検証に合格したら、 **[送信]** を選択して、ユーザーを削除する Azure Batch ジョブを開始します。 エラーが発生した場合は、[一括操作の結果] ページで結果ファイルをダウンロードして表示できます。 このファイルには、各エラーの理由が含まれています。

## <a name="check-status"></a>状態の確認

**[一括操作の結果 (プレビュー)]** ページでは、保留中のすべての一括要求の状態を確認できます。

   ![[一括操作の結果] ページでアップロードの状態を確認する](./media/users-bulk-delete/bulk-center.png)

次に、削除したユーザーが Azure AD 組織に存在するかどうかを、Azure portal または PowerShell を使用して確認できます。

## <a name="verify-deleted-users-in-the-azure-portal"></a>Azure portal で削除済みのユーザーを確認する

1. 組織のユーザー管理者アカウントで、Azure portal にサインインします。
1. ナビゲーション ペインで、 **[Azure Active Directory]** を選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. **[表示]** で **[すべてのユーザー]** のみを選択し、削除したユーザーがリストされなくなったことを確認します。

### <a name="verify-deleted-users-with-powershell"></a>PowerShell で削除済みユーザーを確認する

次のコマンドを実行します。

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

削除したユーザーがリストされなくなったことを確認します。

## <a name="next-steps"></a>次の手順

- [ユーザーの一括追加](users-bulk-add.md)
- [ユーザーの一覧のダウンロード](users-bulk-download.md)
- [ユーザーの一括復元](users-bulk-restore.md)
