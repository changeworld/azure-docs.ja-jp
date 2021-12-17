---
title: クイックスタート - Azure portal を使用して Automation アカウントのマネージド ID を有効にする
description: このクイックスタートでは、Azure portal を使用して Automation アカウントのマネージド ID を有効にする方法を説明します
services: automation
ms.date: 09/07/2021
ms.topic: quickstart
ms.subservice: process-automation
ms.openlocfilehash: 13b314e5f73a0c970e28dfdd87551d713d60175b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462123"
---
# <a name="quickstart-enable-managed-identities-for-your-automation-account-using-the-azure-portal"></a>クイックスタート: Azure portal を使用して Automation アカウントのマネージド ID を有効にする

このクイックスタートでは、Azure Automation アカウントのマネージド ID を有効にする方法について説明します。 マネージド ID と Azure Automation の連携方法の詳細については、[マネージド ID](../automation-security-overview.md#managed-identities) に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- Azure Automation アカウント。 手順については、[Automation アカウントの作成](create-account-portal.md)に関する記事を参照してください。

- ユーザー割り当てマネージド ID。 手順については、「[ユーザー割り当てマネージド ID を作成する](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)」を参照してください。 ユーザー割り当てマネージド ID と、その ID を使用して Runbook によって管理されるターゲット Azure リソースが、同じ Azure サブスクリプションに存在する必要があります。

## <a name="enable-system-assigned-managed-identity"></a>システム割り当てマネージド ID を有効化する

1. [Azure portal](https://portal.azure.com) にサインインし、お使いの Automation アカウントに移動します。

1.  **[アカウント設定]** で、 **[ID (プレビュー)]** を選択します。

   :::image type="content" source="media/enable-managed-identity/managed-identity-portal.png" alt-text="ポータルで [ID] に移動します。":::

1. システム割り当ての **[状態]** オプションを **[オン]** に設定し、 **[保存]** を押します。 確認を求めるメッセージが表示されたら、 **[はい]** を選択します。

   これで、Automation アカウントはシステム割り当て ID を使用できるようになりました。これは Azure Active Directory (Azure AD) に登録され、オブジェクト ID で表されます。

   :::image type="content" source="media/enable-managed-identity/system-assigned-object-id.png" alt-text="マネージド ID であるオブジェクト ID。":::

## <a name="add-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID の追加

このセクションは、最後のセクションが終了した場所から続けられます。

1. **[ユーザー割り当て]** タブを選択し、 **[+ 追加]** または **[ユーザー割り当てマネージド ID の追加]** を選択して **[Add user assigned managed i...]\(ユーザー割り当てマネージド ID の追加\)** ページを開きます。

   :::image type="content" source="media/enable-managed-identity/user-assigned-portal.png" alt-text="ポータルの [ユーザー割り当て] タブ。":::

1. **[サブスクリプション]** ドロップダウン リストから、ユーザー割り当てマネージド ID のサブスクリプションを選択します。

   :::image type="content" source="media/enable-managed-identity/add-user-assigned.png" alt-text="ポータルのユーザー割り当てのものを追加するページ。":::

1. **[ユーザー割り当てマネージド ID]** で、既存のユーザー割り当てマネージド ID を選択し、 **[追加]** を選択します。 **[ユーザー割り当て]** タブに自動的に戻ります。

   :::image type="content" source="media/enable-managed-identity/added-user-identity-portal.png" alt-text="ポータルでユーザー割り当てのものが追加されています。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Automation アカウントにアタッチされているユーザー割り当てマネージド ID が不要になった場合は、次の手順を実行します。

1. **[ユーザー割り当て]** タブで、ユーザー割り当てマネージド ID を選択します。

1. 上部メニューで **[解除]** を選択し、確認を求められたら **[はい]** を選択します。

Automation アカウントで有効になっているシステム割り当てマネージド ID が不要になった場合は、次の手順を実行します。

1. **[システム割り当て]** タブの **[状態]** で、 **[オフ]** を選択します。

1. 上部メニューで **[保存]** を選択し、確認を求められたら **[はい]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Automation アカウントのマネージド ID を有効にしました。 マネージド ID と共に Automation アカウントを使用して Runbook を実行するには、次を参照してください。

> [!div class="nextstepaction"]
> [チュートリアル: マネージド ID を使用して Automation PowerShell Runbook を作成する](../learn/powershell-runbook-managed-identity.md)