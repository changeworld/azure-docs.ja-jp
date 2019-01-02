---
title: Azure Monitor で動的なしきい値を使用したアラートを作成する
description: 機械学習ベースの動的なしきい値を使用したアラートを作成します。
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: Yaniv.Lavi
ms.reviewer: mbullwin
ms.openlocfilehash: b9b56145c13cb83b1686004ce215b1960caced14
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53182997"
---
# <a name="alerts-with-dynamic-thresholds-in-azure-monitor-limited-private-preview"></a>Azure Monitor の動的しきい値を使用したアラート (制限付きプライベート プレビュー)

動的なしきい値を使用したアラートは、Azure Monitor の Azure メトリック アラートの拡張機能で、高度の機械学習 (ML) 機能を活用してメトリックの動作履歴を学習して自動的にベースラインを計算し、それをアラートのしきい値として使用します。

動的なしきい値を使用する利点は次のとおりです。

- モニターは自動的にメトリックのパフォーマンス履歴を学習し、ML アルゴリズムを適用してアラートのしきい値を決定するため、定義済みの固定された境界を設定する必要がありません。
- 季節的な動作を識別し、予想される季節的動作から外れた動作に対しアラートをトリガーできます。 サービスが、週末に定期的にアイドル状態になり、毎週月曜日にピークに達する場合は、動的なしきい値を使用したメトリック アラートはトリガーされません。 現時点では、1 時間ごと、毎日、毎週の周期がサポートされています。
- メトリックのパフォーマンスを継続的に学習し、メトリックの変化に適応します。

この[記事](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#what-resources-can-i-create-near-real-time-metric-alerts-for)に記載されているすべての Azure Monitor ベースメトリック ソースで、動的なしきい値を使用したアラートが利用できます。

## <a name="sign-up-to-access-the-preview"></a>プレビューへのアクセスにサインアップする

この機能を利用するには、[プレビューにサインアップ](https://aka.ms/DynamicThresholdMetricAlerts)してください。 ご意見、ご感想がある場合は、[azurealertsfeedback@microsoft.com](mailto:azurealertsfeedback@microsoft.com) までお寄せください。

## <a name="how-to-configure-alerts-with-dynamic-thresholds"></a>動的なしきい値を使用したアラートの設定方法

動的なしきい値を使用したアラートは、Azure Monitor のアラートから設定できます。

![アラートのプレビュー](media/alerts-dynamic-thresholds/0001.png)

## <a name="creating-an-alert-rule-with-dynamic-thresholds"></a>動的なしきい値を使用したアラート ルールの作成

1. [モニター] の下にある [アラート] ウィンドウから **[新しいアラート ルール]** をクリックして、Azure で新しいアラートを作成します。

   ![新しいアラート ルール](media/alerts-dynamic-thresholds/002.png)

2. [ルールの作成] セクションが表示されます。このセクションは、_[アラートの条件を定義します]_、_[アラートの詳細を定義します]_、_[アクション グループを定義します]_ の 3 つのセクションで構成されています。 まず、_[アラートの条件を定義します]_ セクションでリソースを選択し、**[ターゲットの選択]** リンクを使用して、ターゲットを指定します。 適切なリソースを選択したら、[完了] ボタンをクリックします。

   ![ターゲットを選択する](media/alerts-dynamic-thresholds/0003.png)

3. 次に **[条件の追加]** をクリックして、そのリソースで使用できるシグナル オプションの一覧を表示し、シグナル一覧から適切な **[メトリック]** オプションを選択します。 (たとえば CPU 使用率。)

   ![条件の追加](media/alerts-dynamic-thresholds/004.png)

4. [シグナル ロジックの構成] 画面の [アラート ロジック] セクションには、動的しきい値の種類に条件を変更するオプションがあります。これにより動的しきい値 (赤色の線) とメトリック (青色の線) が自動的に生成されます。

   ![動的](media/alerts-dynamic-thresholds/005.png)

5. グラフに表示されているしきい値は過去 7 日間の履歴データを基に計算され、アラートが作成されると、動的しきい値は、現在利用可能な履歴データを追加要求し、しきい値の精度を向上するために新しいデータを基に学習し続けます。

6. 追加のアラート ロジックの設定:
   - 条件 - 次の 3 つの条件のいずれかを基にアラートをトリガーするよう選択できます。
       - 上限しきい値より大きいか、下限しきい値より小さい (既定値)
       - 上限しきい値より大きい
       - 下限しきい値より小さい
   - 集計時間:平均 (既定)、合計、最大/最小です。
   - アラートの感度:
       - 高 – 最小の偏差でアラートがトリガーされるため、トリガーされるアラート数が多くなります。
       - 中 – 高より感度が低くなるため、高感度よりもトリガーされるアラート数が少なくなります (既定値)。
       - 低 – 最も感度の低いしきい値。

    ![アラート ロジックの設定](media/alerts-dynamic-thresholds/00007.png)

7. 評価基準:
    -  アラートで特定の条件を評価する期間を **[期間]** から選択します。

    ![評価基準](media/alerts-dynamic-thresholds/007.png)

   > [!NOTE]
   > サポートされている期間値は、5 分、10 分、30 分および 1 時間です。

   一時的なスパイクによって発生したアラートのノイズを減らすためには、[Number violations to trigger the alert]\(アラートをトリガーする違反回数\) を使用することをお勧めします。 この機能を有効にすると、X 回連続してしきい値を超えた場合、あるいは、最近の Z 期間中 Y 回しきい値を超えた場合のみ、アラートをトリガーするよう設定できます。 例: 

    15 分間問題が継続した場合、5 分間で3 回連続してしきい値を超えた場合にアラートをトリガーするには、次の設定を使用します。

   ![評価基準](media/alerts-dynamic-thresholds/0008.png)

    過去 30 分中 15 分の間に、5 分間動的しきい値を超えた状態が続いた場合にアラートをトリガーするには、次の設定を使用します。

   ![評価基準](media/alerts-dynamic-thresholds/0009.png)

8. 現時点では、ユーザーは単一の条件としての動的しきい値条件を使用してアラートを設定できます。

   ![ルールを作成する](media/alerts-dynamic-thresholds/010.png)

## <a name="q--a"></a>Q & A

- Q:メトリックが長時間にわたりゆっくり変化した場合、動的しきい値を使用したアラートはトリガーされますか。

- A:おそらくトリガーされないでしょう。 動的なしきい値は、緩やかに変化する問題ではなく、大きな偏差を検出する場合に有効です。

- Q:API を使って動的しきい値を設定することができますか。

- A:今後可能になる予定です。
