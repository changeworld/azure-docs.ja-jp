---
title: 'トラブルシューティング: アラート'
titleSuffix: Azure Digital Twins
description: サービス メトリックに基づいてアラートを設定することで、Azure Digital Twins のトラブルシューティングを行う方法について説明します。
author: baanders
ms.author: baanders
ms.date: 10/5/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 653a710274abc4da116b0c3f3e06b93ed3fce92a
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2021
ms.locfileid: "130128999"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>Azure Digital Twins のトラブルシューティング: 警告

この記事では、[Azure portal](https://portal.azure.com) でアラートを設定する方法を説明します。 これらのアラートは、Azure Digital Twins インスタンスのメトリックに基づいて定義した構成可能な条件が満たされた場合に通知され、重要なアクションを実行することができます。

Azure Digital Twins では、リソースの状態に関する情報を提供する、サービス インスタンスの[メトリック](troubleshoot-metrics.md)が収集されます。 これらのメトリックを使用すると、Azure Digital Twins サービスやそれに接続されているリソースの全体的な正常性を評価できます。

**アラート** は、メトリック データで重要な状態が見つかると事前に通知します。 管理者は、その通知を見て、システムのユーザーが問題に気付く前に問題を識別して対処できます。 アラートについて詳しくは、 [「Microsoft Azure のアラートの概要」](../azure-monitor/alerts/alerts-overview.md) をご覧ください。

## <a name="turn-on-alerts"></a>アラートを有効にする

Azure Digital Twins インスタンスに対してアラートを有効にする方法は次のとおりです。

1. [Azure portal](https://portal.azure.com) にサインインし、Azure Digital Twins インスタンスに移動します。 その名前をポータルの検索バーに入力して、検索することができます。 

2. メニューから **[アラート]** を選択した後、 **[+ 新しいアラート ルール]** を選択します。

   :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="Azure Digital Twin インスタンスの [アラート] セクションにある新しいアラート ルールを作成するためのボタンを示す Azure portal のスクリーンショット。" lightbox="media/troubleshoot-alerts/alerts-pre.png":::

3. 続いて表示される *[アラート ルールの作成]* ページで、画面の指示に従って条件、トリガーされるアクション、アラートの詳細を定義できます。     
    * **[スコープ]** の詳細には、インスタンスの詳細が自動的に入力されます。
    * アラートのトリガーと応答をカスタマイズするには、**条件** と **アクション グループ** の詳細を定義します
    * **[アラート ルールの詳細]** セクションで、ルールの名前と説明 (省略可能) を入力します。 
        - アラートが作成されるとすぐにアクティブになるようにするには、_[作成時にアラート ルールを有効にする]_ チェックボックスをオンにします。
        - 条件が満たされなくなったときにアラートが解決されるようにしたい場合は、 _[自動的にアラートを解決する]_ チェックボックスをオンにします。
        - このセクションは、 _[サブスクリプション]_ 、 _[リソース グループ]_ 、 _[重大度]_ レベルの選択にも使用します。

4. _[アラート ルールの作成]_ ボタンを選択して、アラート ルールを作成します。

   :::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="スコープ、条件、アクション グループ、アラート ルールの詳細に関するセクションが表示された [アラート ルールの作成] ページを示す Azure portal のスクリーンショット。" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

これらのフィールドへの入力方法のガイド付きチュートリアルについては、「[Microsoft Azure のアラートの概要](../azure-monitor/alerts/alerts-overview.md)」をご覧ください。 以下に、Azure Digital Twins の手順の例をいくつか示します。

### <a name="select-conditions"></a>条件を選択する

Azure Digital Twins で使用できるアラート シグナルの種類を示す *条件の選択* プロセスからの抜粋を次に示します。 このページでは、シグナルの種類をフィルター処理し、一覧から目的のシグナルを選択できます。

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="最初の [シグナル ロジックの構成] ページを示す Azure portal のスクリーンショット。[シグナルの種類] ボックスとメトリックの一覧が強調表示されています。":::

シグナルを選択すると、アラートのロジックを構成するように求められます。 ディメンションをフィルター処理したり、アラートのしきい値を設定したり、条件をチェックする頻度を設定したりできます。 平均ルーティング エラー率メトリックが 5% を超えた場合のアラートの設定例を次に示します。

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="2 つ目の [シグナル ロジックの構成] ページを示す Azure portal のスクリーンショット。":::

### <a name="verify-success"></a>成功を確認する

アラートを設定すると、インスタンスの *[アラート]* ページに表示されます。
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="[アラート] ページと追加するボタンを示す Azure portal のスクリーンショット。1 つのアラートが構成されています。" lightbox="media/troubleshoot-alerts/alerts-post.png":::

## <a name="next-steps"></a>次のステップ

* Azure Monitor でのアラートについて詳しくは、「[Microsoft Azure のアラートの概要](../azure-monitor/alerts/alerts-overview.md)」を参照してください。
* Azure Digital Twins のメトリックについては、[トラブルシューティング: メトリック](troubleshoot-metrics.md)に関するページを参照してください。
* メトリックの診断ログを有効にする方法については、「[トラブルシューティング: 診断ログ](troubleshoot-diagnostics.md)」を参照してください。
