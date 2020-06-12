---
title: Azure Automation Update Management のデプロイのスコープを制限する
description: この記事では、スコープ構成を使用して Update Management のデプロイのスコープを制限する方法について説明します。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 72065b388f348da1d268f875a10d5b13d2f8cf3b
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117400"
---
# <a name="limit-update-management-deployment-scope"></a>Update Management のデプロイのスコープを制限する

この記事では、[Update Management](automation-update-management.md) 機能を使用して VM に更新プログラムとパッチをデプロイする際のスコープ構成の操作方法について説明します。 詳細については、「[Azure Monitor での監視ソリューションのターゲット設定 (プレビュー)](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)」を参照してください。 

## <a name="about-scope-configurations"></a>スコープ構成について

スコープ構成は、Update Management のスコープを特定のコンピューターに限定するために使用される、1 つ以上の保存された検索条件 (クエリ) のグループです。 スコープ構成は、有効にするコンピューターを対象として設定するために、Log Analytics ワークスペース内で使用されます。 Update Management から更新プログラムを受信するコンピューターを追加すると、ワークスペース内の保存した検索条件にも、そのコンピューターが追加されます。

## <a name="set-the-scope-limit"></a>スコープの制限を設定する

Update Management デプロイのスコープを制限するには、次のようにします。

1. お使いの Automation アカウントで、 **[関連リソース]** の下にある **[リンクされたワークスペース]** を選択します。

2. **[ワークスペースに移動]** をクリックします。

3. **[Workspace Data Sources]\(ワークスペース データ ソース\)** の下の **[Scope Configurations (Preview)]\(スコープ構成 (プレビュー)\)** を選択します。

4. `MicrosoftDefaultScopeConfig-Updates` スコープ構成の右側にある省略記号を選択し、 **[編集]** をクリックします。 

5. 編集ペインで **[コンピューター グループの選択]** を展開します。 [コンピューター グループ] ペインに、スコープ構成を作成するために使用された、保存された検索条件が表示されます。 Update Management によって使用される保存した検索条件は次のとおりです。

    |名前     |カテゴリ  |エイリアス  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | 更新プログラム        | Updates__MicrosoftDefaultComputerGroup         |

6. 保存した検索条件を選択して、グループの設定に使用されるクエリを表示および編集します。 次の図は、クエリとその結果を示しています。

    ![保存した検索条件](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>次のステップ

* この機能を使った作業については、「[Azure VM の更新プログラムとパッチの管理](automation-tutorial-update-management.md)」を参照してください。
* この機能に関するエラーのトラブルシューティングについては、「[Update Management ソリューションに関する問題のトラブルシューティング](troubleshoot/update-management.md)」を参照してください。
* Windows Update エージェントに関するエラーのトラブルシューティングについては、「[Windows Update エージェントの問題をトラブルシューティングする](troubleshoot/update-agent-issues.md)」を参照してください。
* Linux Update エージェントに関するエラーのトラブルシューティングについては、「[Linux Update エージェントに関する問題のトラブルシューティング](troubleshoot/update-agent-issues-linux.md)」を参照してください。