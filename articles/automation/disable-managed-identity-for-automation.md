---
title: Azure Automation アカウントのマネージド ID を無効にする (プレビュー)
description: この記事では、Azure Automation アカウントのマネージド ID を無効にして削除する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: e17e1afda50d9a0263067a77bf26435f53b4f237
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519274"
---
# <a name="disable-your-azure-automation-account-managed-identity-preview"></a>Azure Automation アカウントのマネージド ID を無効にする (プレビュー)

Azure Automation でシステム割り当て ID を無効にする方法は 2 つあります。 このタスクは、Azure portal から、または Azure Resource Manager (ARM) テンプレートを使用して実行できます。

## <a name="disable-managed-identity-in-the-azure-portal"></a>Azure portal でマネージド ID を無効にする

マネージド ID が最初に設定された方法に関係なく、Azure portal からマネージド ID を無効にすることができます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Automation アカウントに移動し、 **[アカウント設定]** で **[ID]** を選択します。

1. **[システム割り当て済み]** オプションを **[オフ]** に設定し、 **[保存]** を押します。 確認を求めるメッセージが表示されたら、 **[はい]** を押します。

マネージド ID が削除され、ターゲット リソースにアクセスできなくなりました。

## <a name="disable-using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して無効にする

Azure Resource Manager テンプレートを使用して Automation アカウントのマネージド ID を作成した場合は、そのテンプレートを再利用し、設定を変更することで、マネージド ID を無効にすることができます。 次の例に示すように、ID オブジェクトの子プロパティの種類を **None** に設定し、テンプレートを再実行します。

```json
"identity": { 
   "type": "None" 
} 
```

この方法を使用してシステム割り当て ID を削除すると、Azure AD からも削除されます。 また、システム割り当て ID は、それらが割り当てられているアプリ リソースが削除されると、Azure AD から自動的に削除されます。

## <a name="next-steps"></a>次のステップ

- Azure Automation でのマネージド ID の有効化の詳細については、[Automation でのマネージド ID の有効化と使用 (プレビュー)](enable-managed-identity-for-automation.md)に関する記事を参照してください。

- Automation アカウントのセキュリティの概要については、[Automation アカウントの認証の概要](automation-security-overview.md)に関する記事を参照してください。
