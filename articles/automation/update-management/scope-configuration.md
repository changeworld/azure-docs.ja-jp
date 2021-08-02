---
title: Azure Automation Update Management のデプロイのスコープを制限する
description: この記事では、スコープ構成を使用して Update Management のデプロイのスコープを制限する方法について説明します。
services: automation
ms.date: 06/03/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 230be9fcdb30e51802a2e288cde86fd8047f70f0
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854956"
---
# <a name="limit-update-management-deployment-scope"></a>Update Management のデプロイのスコープを制限する

この記事では、[Update Management](overview.md) 機能を使用して、お使いのマシンに更新プログラムと修正プログラムをデプロイする際のスコープ構成の操作方法について説明します。 詳細については、「[Azure Monitor での監視ソリューションのターゲット設定 (プレビュー)](../../azure-monitor/insights/solution-targeting.md)」を参照してください。

## <a name="about-scope-configurations"></a>スコープ構成について

スコープ構成は、Update Management のスコープを特定のコンピューターに限定するために使用される、1 つ以上の保存された検索条件 (クエリ) のグループです。 スコープ構成は、有効にするコンピューターを対象として設定するために、Log Analytics ワークスペース内で使用されます。 Update Management から更新プログラムを受信するコンピューターを追加すると、ワークスペース内の保存した検索条件にも、そのコンピューターが追加されます。 

既定では、Update Management でマシンを有効にした方法に応じて、Update Management によって **Updates__MicrosoftDefaultComputerGroup** という名前のコンピューター グループが作成されます。

* Automation アカウントから、 **[+ Azure VM の追加]** を選択した。
* Automation アカウントから、 **[マシンの管理]** を選択し、 **[使用可能なすべてのマシンで有効にする]** オプションを選択するか、 **[選択したマシンで有効にする]** を選択した。

上記のいずれかの方法が選択されている場合は、このコンピューター グループが **MicrosoftDefaultScopeConfig-Updates** スコープ構成に追加されます。 また、管理のニーズに合わせて 1 つ以上のカスタム コンピューター グループをこのスコープに追加し、特定のコンピューターで Update Management による管理を有効にする方法を制御することもできます。

1 つ以上のマシンを **Updates__MicrosoftDefaultComputerGroup** から削除して Update Management による管理を停止するには、「[Update Management から VM を削除する](remove-vms.md)」を参照してください。

## <a name="set-the-scope-limit"></a>スコープの制限を設定する

Update Management デプロイのスコープを制限するには、次のようにします。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. Azure portal で、 **[Log Analytics ワークスペース]** に移動します。 一覧からお使いのワークスペースを選択します。

3. Log Analytics ワークスペースで、左側のメニューから **[スコープ構成 (プレビュー)]** を選択します。

4. **MicrosoftDefaultScopeConfig-Updates** スコープ構成の右側にある省略記号を選択し、 **[編集]** を選択します。

5. 編集ペインで **[コンピューター グループの選択]** を展開します。 **[コンピューター グループ]** ペインに、スコープ構成に追加された、保存済み検索条件が表示されます。 Update Management によって使用される保存した検索条件は次のとおりです。

    |名前     |カテゴリ  |エイリアス  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | 更新プログラム        | Updates__MicrosoftDefaultComputerGroup         |

6. カスタム グループを追加した場合は、一覧に表示されます。 選択を解除するには、項目の左側にあるチェック ボックスをオフにします。 スコープにカスタム グループを追加するには、それを選択し、変更が完了したら、 **[選択]** をクリックします。

7. **[スコープ構成の編集]** ページで、 **[OK]** をクリックして変更を保存します。

## <a name="next-steps"></a>次のステップ

[Azure Monitor ログに対してクエリを実行](query-logs.md)すると、更新プログラムの評価、展開、およびその他の関連する管理タスクを分析することができます。 これには、作業を開始するために役立つ事前定義されたクエリが含まれています。
