---
title: Azure Automation の Change Tracking とインベントリのデプロイのスコープを制限する
description: この記事では、スコープ構成を使用して、Change Tracking とインベントリのデプロイのスコープを制限する方法について説明します。
services: automation
ms.subservice: change-inventory-management
ms.date: 05/27/2021
ms.topic: conceptual
ms.openlocfilehash: 6812a0b0688efdb75d847a36d661ba87017a8b9d
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110653197"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Change Tracking とインベントリのデプロイのスコープを制限する

この記事では、[Change Tracking とインベントリ](overview.md)機能を使用して VM に変更をデプロイする際のスコープ構成の操作方法について説明します。 詳細については、「[Azure Monitor での監視ソリューションのターゲット設定 (プレビュー)](../../azure-monitor/insights/solution-targeting.md)」を参照してください。

## <a name="about-scope-configurations"></a>スコープ構成について

スコープ構成は、Change Tracking とインベントリのスコープを特定のコンピューターに限定するために使用される、1 つ以上の保存された検索条件 (クエリ) のグループです。 スコープ構成は、有効にするコンピューターを対象として設定するために、Log Analytics ワークスペース内で使用されます。 この機能から変更にコンピューターを追加すると、ワークスペース内の保存した検索条件にも、そのコンピューターが追加されます。

既定では、変更履歴とインベントリにより、マシンをどのように有効にしたかに応じて、**ChangeTracking__MicrosoftDefaultComputerGroup** という名前のコンピューター グループが作成されます。

* Automation アカウントから、 **[+ Azure VM の追加]** を選択しました。
* Automation アカウントから **[マシンの管理]** を選択し、 **[使用可能なすべてのマシンで有効にする]** オプションを選択するか、 **[選択したマシンで有効にする]** を選択しました。

上記のいずれかの方法が選択されている場合は、このコンピューター グループが **MicrosoftDefaultScopeConfig-ChangeTracking** スコープ構成に追加されます。 また、管理のニーズに合わせて 1 つ以上のカスタム コンピューター グループをこのスコープに追加し、特定のコンピューターが 変更履歴とインベントリ で管理を有効にする方法を制御することもできます。

**ChangeTracking__MicrosoftDefaultComputerGroup** から 1 つ以上のマシンを削除して変更履歴とインベントリ を使用して管理を停止するには、[「変更履歴とインベントリ から VM を削除する」](remove-vms-from-change-tracking.md)を参照してください。

## <a name="set-the-scope-limit"></a>スコープの制限を設定する

Change Tracking とインベントリのデプロイのスコープを制限するには、次のようにします。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. Azure portal で、 **[Log Analytics ワークスペース]** に移動します。 一覧からお使いのワークスペースを選択します。

3. Log Analytics ワークスペースで、左側のメニューから **[スコープ構成 (プレビュー)]** を選択します。

4. **MicrosoftDefaultScopeConfig-ChangeTracking** スコープ構成の右側にある省略記号を選択し、 **[編集]** を選択します。

5. 編集ペインで **[コンピューター グループの選択]** を展開します。 **[コンピューター グループ]** ペインに、スコープ構成に追加された、保存済み検索条件が表示されます。 Update Management によって使用される保存した検索条件は次のとおりです。

    |名前     |カテゴリ  |エイリアス  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | ChangeTracking        | ChangeTracking__MicrosoftDefaultComputerGroup         |

6. カスタム グループを追加した場合は、一覧に表示されます。 選択を解除するには、項目の左側にあるチェック ボックスをオフにします。 スコープにカスタム グループを追加するには、それを選択し、変更が完了したら、 **[選択]** をクリック します。

7. **[スコープ構成の編集]** ページで、 **[OK]** をクリックして変更を保存します。

## <a name="next-steps"></a>次のステップ

* 変更履歴とインベントリの操作については、[変更履歴とインベントリの管理](manage-change-tracking.md)に関するページを参照してください。
* この機能に関する一般的な問題のトラブルシューティングについては、「[Change Tracking と Inventory に関する問題のトラブルシューティング](../troubleshoot/change-tracking.md)」を参照してください。
