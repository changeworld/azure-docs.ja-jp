---
title: リモート監視ソリューションでのデバイス検出に関する問題 - Azure | Microsoft Docs
description: このチュートリアルでは、ルールとアクションを使用して、リモート監視ソリューションでしきい値に基づくデバイスの問題を自動的に検出する方法を示します。
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 5acf35ed19a5b6baa2885fd58cfb7fbbe1ac3cd8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="detect-issues-using-threshold-based-rules"></a>しきい値に基づくルールを使用して問題を検出する

このチュートリアルでは、リモート監視ソリューションのルール エンジンの機能を示します。 これらの機能を紹介するために、このチュートリアルでは Contoso IoT アプリケーションのシナリオを使用します。

Contoso には、**Chiller** デバイスによって報告される圧力が 250 PSI を超える場合に重大アラートを生成するルールがあります。 運用者であるあなたは、初期の圧力の急上昇を探すことで、問題のあるセンサーを搭載している可能性のある **Chiller** デバイスを特定する必要があります。 これらのデバイスを特定するため、圧力が 150 PSI を超えた場合に警告を生成するルールを作成します。

さらに、過去 5 分間で **Chiller** デバイスの平均湿度が 80% を超え、過去 5 分間で **Chiller** デバイスの温度が華氏 75 度を超えたときに重大アラートがトリガーされる必要があるとも言われました。

このチュートリアルで学習する内容は次のとおりです。

>[!div class="checklist"]
> * ソリューション内のルールを表示する
> * 新しいルールの作成
> * 複数の条件で新しいルールを作成する
> * 既存のルールを編集する
> * 規則を削除する

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、お使いの Azure サブスクリプションにリモート監視ソリューションのインスタンスをデプロイしておく必要があります。

まだリモート監視ソリューションをデプロイしていない場合は、「[リモート監視ソリューション アクセラレータをデプロイする](iot-suite-remote-monitoring-deploy.md)」チュートリアルを実行する必要があります。

## <a name="view-the-rules-in-your-solution"></a>ソリューション内のルールを表示する

ソリューションの **[ルール]** ページには、現在のすべてのルールの一覧が表示されます。

![ルールとアクション ページ](media/iot-suite-remote-monitoring-automate/rulesactions_v2.png)

**Chiller** デバイスに適用されるルールだけを表示するには、フィルターを適用します。

![ルールの一覧をフィルター処理する](media/iot-suite-remote-monitoring-automate/rulesactionsfilter_v2.png)

一覧でルールを選択すると、詳細情報の表示と編集ができます。

![ルールの詳細を表示する](media/iot-suite-remote-monitoring-automate/rulesactionsdetail_v2.png)

1 つまたは複数のルールを無効化、有効化、または削除するには、一覧で複数のルールを選択します。

![複数のルールを選択する](media/iot-suite-remote-monitoring-automate/rulesactionsmultiselect_v2.png)

## <a name="create-a-new-rule"></a>新しいルールの作成

**Chiller** デバイスの圧力が 150 PSI を超えるときに警告を生成する新しいルールを追加するには、**[新しい規則]** を選択します。

![ルールを作成する](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_v2.png)

次の値を使用してルールを作成します。

| Setting          | 値                                 |
| ---------------- | ------------------------------------- |
| 規則の名前        | Chiller 警告                       |
| [説明]      | Chiller の圧力が 150 PSI を超えました |
| デバイス グループ     | **Chillers** デバイス グループ             |
| 計算      | すぐに                               |
| 条件 1 フィールド| pressure                              |
| 条件 1 演算子 | より大きい                      |
| 条件 1 値    | 150                               |
| 重大度レベル  | 警告                               |

新しいルールを保存するには、**[適用]** を選択します。

**[ルール]** ページまたは、**[ダッシュボード]** ページでルールがトリガーされた時間を表示できます。

## <a name="create-a-new-rule-with-multiple-conditions"></a>複数の条件で新しいルールを作成する

過去 5 分間で **Chiller** デバイスの平均湿度が 80% を超え、過去 5 分間で **Chiller** デバイスの温度が華氏 75 度を超えたときに重大アラートを生成する複数の条件がある新しいルールを作成するには、**[新しい規則]** を選択します。

![複数のルールを作成する](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_mult_v2.png)

次の値を使用してルールを作成します。

| Setting          | 値                                 |
| ---------------- | ------------------------------------- |
| 規則の名前        | Chiller の湿度と温度が危険    |
| [説明]      | 湿度と温度が危険です |
| デバイス グループ     | **Chillers** デバイス グループ             |
| 計算      | 平均                               |
| 期間      | 5                                     |
| 条件 1 フィールド| 湿度                              |
| 条件 1 演算子 | より大きい                      |
| 条件 1 値    | 80                               |
| 重大度レベル  | 重大                              |

2 つ目の条件を追加するには、[+ 条件の追加] をクリックします。

![条件 2 を作成する](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2.png)

新しい条件で次の値を使用します。

| Setting          | 値                                 |
| ---------------- | ------------------------------------- |
| 条件 2 フィールド| 温度                           |
| 条件 2 演算子 | より大きい                      |
| 条件 2 値    | 75                                |

新しいルールを保存するには、**[適用]** を選択します。

**[ルール]** ページまたは、**[ダッシュボード]** ページでルールがトリガーされた時間を表示できます。

## <a name="edit-an-existing-rule"></a>既存のルールを編集する

既存のルールを変更するには、ルールの一覧で選択します。

![ルールの編集](media/iot-suite-remote-monitoring-automate/rulesactionsedit_v2.png)

<!--## Disable a rule

To temporarily switch off a rule, you can disable it in the list of rules. Choose the rule to disable, and then choose **Disable**. The **Status** of the rule in the list changes to indicate the rule is now disabled. You can re-enable a rule that you previously disabled using the same procedure.

![Disable rule](media/iot-suite-remote-monitoring-automate/rulesactionsdisable.png)

You can enable and disable multiple rules at the same time if you select multiple rules in the list.-->

<!--## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の方法について説明しました。

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * ソリューション内のルールを表示する
> * 新しいルールの作成
> * 既存のルールを編集する
> * 規則を削除する

しきい値ベースのルールを使用して問題を検出する方法について説明したので、次に推奨される手順を以下に示します。

* [デバイスの管理と構成を行う](./iot-suite-remote-monitoring-manage.md)。
* [トラブルシューティングを行ってデバイスの問題を修復する](./iot-suite-remote-monitoring-maintain.md)。
* [シミュレートされたデバイスを使用してソリューションをテストする](iot-suite-remote-monitoring-test.md)。

<!-- Next tutorials in the sequence -->