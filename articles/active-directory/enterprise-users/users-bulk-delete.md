---
title: Azure Active Directory ポータルでユーザーを一括削除する | Microsoft Docs
description: Azure Active Directory の Azure 管理センターでユーザーを一括削除する
services: active-directory
author: curtand
ms.author: curtand
manager: KarenH444
ms.date: 07/09/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56822c8a350e1aaaa047b043e614b2eb20123e31
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985430"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Azure Active Directory でユーザーを一括削除する

Azure Active Directory (Azure AD) では、カンマ区切り形式 (CSV) のファイルを使用して、1 グループに属する多人数のメンバーを一括削除できます。

## <a name="csv-template-structure"></a>CSV テンプレートの構造

![CSV ファイルには、削除するユーザーの名前と ID が含まれています。](./media/users-bulk-delete/delete-csv-file.png)

ダウンロードした CSV テンプレート内の行は次のとおりです。

- **バージョン番号**: アップロード CSV の先頭行にバージョン番号を含める必要があります。
- **列見出し**: `User name [userPrincipalName] Required`。 古いバージョンのテンプレートでは異なる場合があります。
- **例の行**: テンプレートには使用できる値の例が含まれています。 `Example: chris@contoso.com` 例の行は削除して、必要な項目に置き換える必要があります。

### <a name="additional-guidance"></a>その他のガイダンス

- テンプレートは、最初の 2 行を削除または修正しなければ処理できません。
- 必須の列が最初に示されています。
- 新しい列をテンプレートに追加しないでください。 列を追加しても無視され、処理されません。
- 最新バージョンの CSV テンプレートをダウンロードしてから、新たな変更を加えます。

## <a name="to-bulk-delete-users"></a>ユーザーを一括削除するには

1. 組織のユーザー管理者アカウントで、[ご自身の Azure AD 組織にサインイン](https://aad.portal.azure.com)します。
1. Azure AD で、 **[Users]\(ユーザー\)**  >  **[Bulk operations]\(一括操作\)**  >  **[Bulk delete]\(一括削除\)** を順にクリックします。
1. **[Bulk delete user]\(ユーザーの一括削除\)** ページで、 **[Download]\(ダウンロード\)** をクリックして、最新バージョンの CSV テンプレートをダウンロードします。
1. この CSV ファイルを開いて、削除するユーザーごとに 1 行を追加します。 唯一の必須値は、**ユーザー プリンシパル名** です。 ファイルを保存します。
1. **[ユーザーの一括削除]** ページの **[CSV ファイルをアップロード]** で、そのファイルを参照します。 ファイルを選択して [送信] をクリックすると、CSV ファイルの検証が開始されます。
1. ファイルの内容が検証されると、"**ファイルが正常にアップロードされました**" と表示されます。 エラーが存在する場合は、ジョブを送信する前にそれらを修正する必要があります。
1. ファイルの検証に合格したら、 **[送信]** を選択して、ユーザーを削除する Azure 一括操作を開始します。
1. 削除操作が完了すると、一括操作が成功したという通知が表示されます。

エラーがある場合は、 **[一括操作の結果]** ページで結果ファイルをダウンロードして表示できます。 このファイルには、各エラーの理由が含まれています。

## <a name="check-status"></a>状態の確認

**[一括操作の結果]** ページでは、保留中のすべての一括要求の状態を確認できます。

   [![[一括操作の結果] ページで削除の状態を確認します。](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

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

## <a name="next-steps"></a>次のステップ

- [ユーザーの一括追加](users-bulk-add.md)
- [ユーザーの一覧のダウンロード](users-bulk-download.md)
- [ユーザーの一括復元](users-bulk-restore.md)
