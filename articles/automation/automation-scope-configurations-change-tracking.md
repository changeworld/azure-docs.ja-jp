---
title: Azure Automation の変更履歴とインベントリのスコープ構成を操作する
description: この記事では、変更履歴とインベントリを使用する際のスコープ構成を操作する方法について説明します。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4fac94cc2f8f378b7e9d8e9485baed6a0ffa838b
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832165"
---
# <a name="work-with-scope-configurations-for-change-tracking-and-inventory"></a>変更履歴とインベントリのスコープ構成を操作する

この記事では、VM に対して [Update Management](automation-update-management.md) 機能を有効にする際のスコープ構成を操作する方法について説明します。 

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>スコープ構成の確認

Update Management は、Log Analytics ワークスペース内のスコープ構成を使用して、Update Management を有効にする対象のコンピューターを決定します。 スコープ構成は、機能のスコープを特定のコンピューターに限定するために使用される、1 つ以上の保存された検索条件のグループです。 スコープ構成にアクセスするには:

1. Automation アカウントで、 **[関連リソース]** の **[ワークスペース]** を選択します。 

2. **[ワークスペースのデータ ソース]** でワークスペースを選択し、 **[スコープ構成]** を選択します。

3. 選択したワークスペースの Update Management 機能がまだ有効になっていない場合、`MicrosoftDefaultScopeConfig-ChangeTracking` スコープ構成が作成されます。 

4. 選択したワークスペースで、その機能が既に有効になっている場合、機能の再デプロイは行われず、スコープ構成の追加も行われません。 

5. いずれかのスコープ構成の省略記号を選択し、 **[編集]** をクリックします。 

6. 編集ペインで **[コンピューター グループの選択]** を選択します。 [コンピューター グループ] ペインに、スコープ構成を作成するために使用された、保存された検索条件が表示されます。

## <a name="view-a-saved-search"></a>保存した検索条件を表示する

変更履歴とインベントリにコンピューターを追加すると、ワークスペース内の保存した検索条件にも、そのコンピューターが追加されます。 保存した検索条件は、対象のコンピューターを含んだクエリです。

1. Log Analytics ワークスペースに移動し、 **[全般]** の **[保存された検索条件]** を選択します。 Update Management によって使用される保存した検索条件は次のとおりです。

    |名前     |カテゴリ  |エイリアス  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

2. 保存した検索条件を選択して、グループの設定に使用されるクエリを表示します。 次の図は、クエリとその結果を示しています。

    ![保存した検索条件](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>次のステップ

* 変更履歴とインベントリの操作については、[変更履歴とインベントリの管理](change-tracking-file-contents.md)に関するページを参照してください。
* この機能に関する一般的な問題のトラブルシューティングについては、「[Change Tracking と Inventory に関する問題のトラブルシューティング](troubleshoot/change-tracking.md)」を参照してください。