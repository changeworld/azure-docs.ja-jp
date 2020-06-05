---
title: Azure Automation の Update Management のスコープ構成を操作する
description: この記事では、Update Management を使用する際のスコープ構成を操作する方法について説明します。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 23ec49f2d68cf376ef0beb118d8bf69ada7bc0de
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832029"
---
# <a name="work-with-scope-configurations-for-update-management"></a>Update Management のスコープ構成を操作する

この記事では、VM に対して [Update Management](automation-update-management.md) 機能を使用する際のスコープ構成を操作する方法について説明します。 

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>スコープ構成の確認

Update Management は、Log Analytics ワークスペース内のスコープ構成を使用して、その機能を有効にする対象のコンピューターを決定します。 スコープ構成は、機能のスコープを特定のコンピューターに限定するために使用される、1 つ以上の保存された検索条件のグループです。 スコープ構成にアクセスするには:

1. Automation アカウントで、 **[関連リソース]** の **[ワークスペース]** を選択します。 

2. **[ワークスペースのデータ ソース]** でワークスペースを選択し、 **[スコープ構成]** を選択します。

3. 選択したワークスペースの Update Management 機能がまだ有効になっていない場合、`MicrosoftDefaultScopeConfig-Updates` スコープ構成が作成されます。 

4. 選択したワークスペースで、その機能が既に有効になっている場合、機能の再デプロイは行われず、スコープ構成の追加も行われません。 

5. いずれかのスコープ構成の省略記号を選択し、 **[編集]** をクリックします。 

6. 編集ペインで **[コンピューター グループの選択]** を選択します。 [コンピューター グループ] ペインに、スコープ構成を作成するために使用された、保存された検索条件が表示されます。

## <a name="view-a-saved-search"></a>保存した検索条件を表示する

Update Management にコンピューターを追加すると、ワークスペース内の保存した検索条件にも、そのコンピューターが追加されます。 保存した検索条件は、対象のコンピューターを含んだクエリです。

1. Log Analytics ワークスペースに移動し、 **[全般]** の **[保存された検索条件]** を選択します。 Update Management によって使用される保存した検索条件は次のとおりです。

|名前     |カテゴリ  |エイリアス  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     | 更新プログラム        | Updates__MicrosoftDefaultComputerGroup         |

2. 保存した検索条件を選択して、グループの設定に使用されるクエリを表示します。 次の図は、クエリとその結果を示しています。

    ![保存した検索条件](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>次のステップ

* この機能を使った作業については、「[Azure VM の更新プログラムとパッチの管理](automation-tutorial-update-management.md)」を参照してください。
* この機能に関するエラーのトラブルシューティングについては、「[Update Management ソリューションに関する問題のトラブルシューティング](troubleshoot/update-management.md)」を参照してください。
* Windows Update エージェントに関するエラーのトラブルシューティングについては、「[Windows Update エージェントの問題をトラブルシューティングする](troubleshoot/update-agent-issues.md)」を参照してください。
* Linux Update エージェントに関するエラーのトラブルシューティングについては、「[Linux Update エージェントに関する問題のトラブルシューティング](troubleshoot/update-agent-issues-linux.md)」を参照してください。