---
title: クイックスタート - ポータルを使用して Azure Automation アカウントを作成する
description: このクイックスタートでは、ポータルを使用して Azure Automation アカウントの作成を開始する方法を説明します
services: automation
ms.date: 10/26/2021
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 7497362ccd71e6efabc873015796855bd989d33c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470685"
---
# <a name="quickstart-create-an-automation-account-using-the-azure-portal"></a>クイックスタート: Azure portal を使用して Automation アカウントを作成する

Azure [Automation アカウント](../automation-security-overview.md)は、さまざまなリソースにアクセスできるブラウザーベースのユーザー インターフェイスである Azure portal を使用して作成できます。 1 つの Automation アカウントで、特定のテナントのすべてのリージョンおよびサブスクリプションにわたってリソースを管理できます。 このクイックスタートでは、Automation アカウントを作成する手順について説明します。

## <a name="prerequisites"></a>前提条件

アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-automation-account"></a>Automation アカウントを作成する

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 上部のメニューで、 **[+ リソースの作成]** を選択します。

1. **[カテゴリ]** で、 **[IT & Management Tools]\(IT & 管理ツール\)** 、 **[Automation]** の順に選択します。

   :::image type="content" source="./media/create-account-portal/automation-account-portal.png" alt-text="ポータルでの Automation アカウントの検索。":::

新しい Automation アカウントのオプションは、 **[Automation アカウントを作成する]** ページのタブにまとめられています。 次のセクションでは、各タブとそのオプションについて説明します。

### <a name="basics"></a>基本

**[基本]** タブでは、Automation アカウントに関する必須の情報を入力します。 **[基本]** タブの設定が済んだら、他のタブのオプションを設定して新しい Automation アカウントをさらにカスタマイズするか、 **[確認および作成]** を選択して既定のオプションをそのまま使用し、アカウントの検証と作成を続けることができます。

> [!NOTE]
> 既定では、Automation アカウントのシステム割り当てマネージド ID が有効になっています。

次の表では **[基本]** タブのフィールドについて説明します。

| **フィールド** | **必須**<br> **or**<br> **省略可能** |**説明** |
|---|---|---|
|サブスクリプション|必須 |ドロップダウン リストから、アカウントの Azure サブスクリプションを選択します。|
|Resource group|必須 |ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** を選択します。|
|Automation アカウント名|必須 |場所とリソース グループに対して一意である名前を入力します。 削除された Automation アカウントの名前はすぐには使用できない場合があります。 一度ユーザー インターフェイスで入力されたアカウント名を変更することはできません。 |
|リージョン|必須 |ドロップダウン リストから、アカウントのリージョンを選択します。 Automation アカウントをデプロイできる場所の最新の一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all)」を参照してください。|

次の図は、新しい Automation アカウントの標準構成を示しています。

:::image type="content" source="./media/create-account-portal/create-account-basics.png" alt-text="[基本] タブで Automation アカウントを作成するために必要なフィールド":::

### <a name="advanced"></a>上級

**[詳細設定]** タブで、新しい Automation アカウントのマネージド ID オプションを構成できます。 Automation アカウントを作成した後で、ユーザー割り当てマネージド ID オプションを構成することもできます。

ユーザー割り当てマネージド ID を作成する方法については、「[ユーザー割り当てマネージド ID を作成する](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)」を参照してください。

次の表では、 **[詳細設定]** タブのフィールドを説明します。

| **フィールド** | **必須**<br> **or**<br> **省略可能** |**説明** |
|---|---|---|
|システム割り当て |オプション |Automation アカウントのライフサイクルに関連付けられている Azure Active Directory ID。 |
|ユーザー割り当て |オプション |使用するリソースとは別に管理されるスタンドアロンの Azure リソースとして表されるマネージド ID。|

マネージド ID を後で有効にすることもでき、それがなくても Automation アカウントは作成されます。 アカウントの作成後にマネージド ID を有効にするには、「[マネージド ID の有効化](enable-managed-identity.md)」を参照してください。 両方のオプションを選択する場合、ユーザー割り当て ID については **[ユーザー割り当て ID の追加]** オプションを選択 します。 **[ユーザー割り当て済みマネージド ID の選択]** ページで、サブスクリプションを選択し、そのサブスクリプションで作成された 1 つ以上のユーザー割り当て ID を追加して、Automation アカウントに割り当てます。

次の図は、新しい Automation アカウントの標準構成を示しています。

:::image type="content" source="./media/create-account-portal/create-account-advanced.png" alt-text="[詳細設定] タブで Automation アカウントを作成するために必要なフィールド":::

### <a name="tags-tab"></a>[タグ] タブ

**[タグ]** タブでは、Azure リソースを整理するための Resource Manager タグを指定できます。 詳細については、「[論理的な組織化のためにリソース、リソース グループ、サブスクリプションにタグを付ける](../../azure-resource-manager/management/tag-resources.md)」を参照してください。

### <a name="review--create-tab"></a>[確認と作成] タブ

**[確認および作成]** タブに移動すると、選択した Automation アカウントの設定の検証が Azure によって実行されます。 検証に成功した場合は、Automation アカウントの作成に進むことができます。

検証が失敗した場合は、変更する必要がある設定がポータルに示されます。

新しい Automation アカウントを確認します。

:::image type="content" source="./media/create-account-portal/automation-account-overview.png" alt-text="Automation アカウントの概要ページ":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Automation アカウントを引き続き使用しない場合は、 **[概要]** ページで **[削除]** を選択し、確認のメッセージが表示されたら **[はい]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Automation アカウントを作成しました。 Automation アカウントでマネージド ID を使用するには、次のクイックスタートに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル - マネージド ID を使用して Automation PowerShell Runbook を作成する](../learn/powershell-runbook-managed-identity.md)