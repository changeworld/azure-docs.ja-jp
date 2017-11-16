---
title: "リモート監視ソリューションでのデバイス検出に関する問題 - Azure | Microsoft Docs"
description: "このチュートリアルでは、ルールとアクションを使用して、リモート監視ソリューションでしきい値に基づくデバイスの問題を自動的に検出する方法を示します。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 11/10/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 173ffbdd70313ef5a0d2af2cf1c8996d2395274a
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2017
---
# <a name="detect-issues-using-threshold-based-rules"></a>しきい値に基づくルールを使用して問題を検出する

このチュートリアルでは、リモート監視ソリューションのルール エンジンの機能を示します。 これらの機能を紹介するために、このチュートリアルでは Contoso IoT アプリケーションのシナリオを使用します。

Contoso には、**Chiller** デバイスによって報告される圧力が 250 PSI を超える場合に重大アラートを生成するルールがあります。 運用者であるあなたは、初期の圧力の急上昇を探すことで、問題のあるセンサーを搭載している可能性のある **Chiller** デバイスを特定する必要があります。 これらのデバイスを特定するため、圧力が 150 PSI を超えた場合に警告を生成するルールを作成します。

このチュートリアルで学習する内容は次のとおりです。

>[!div class="checklist"]
> * ソリューション内のルールを表示する
> * 新しいルールの作成
> * 既存のルールを編集する
> * 規則を削除する

## <a name="prerequisites"></a>前提条件

このチュートリアルを実行するには、お使いの Azure サブスクリプションにデプロイされたリモート監視ソリューションのインスタンスが必要です。

リモート監視ソリューションをまだデプロイしていない場合は、「[Deploy the remote monitoring preconfigured solution (リモート監視の事前構成済みソリューションのデプロイ)](iot-suite-remote-monitoring-deploy.md)」のチュートリアルを完了しておく必要があります。

## <a name="view-the-rules-in-your-solution"></a>ソリューション内のルールを表示する

ソリューションの **[Rules & Actions]\(ルールとアクション\)** ページには、現在のすべてのルールの一覧が表示されます。

![ルールとアクション ページ](media/iot-suite-remote-monitoring-automate/rulesactions.png)

**Chiller** デバイスに適用されるルールだけを表示するには、フィルターを適用します。

![ルールの一覧をフィルター処理する](media/iot-suite-remote-monitoring-automate/rulesactionsfilter.png)

一覧でルールを選択すると、詳細情報の表示と編集ができます。

![ルールの詳細を表示する](media/iot-suite-remote-monitoring-automate/rulesactionsdetail.png)

1 つまたは複数のルールを無効化、有効化、または削除するには、一覧で複数のルールを選択します。

![複数のルールを選択する](media/iot-suite-remote-monitoring-automate/rulesactionsmultiselect.png)

## <a name="create-a-new-rule"></a>新しいルールの作成

**Chiller** デバイスの圧力が 150 PSI を超えるときに警告を生成する新しいルールを追加するには、**[新しい規則]** を選択します。

![ルールを作成する](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule.png)

次の値を使用してルールを作成します。

| 設定          | 値                                 |
| ---------------- | ------------------------------------- |
| 名前             | Chiller 警告                       |
| ソース           | **Chiller**デバイス グループ              |
| トリガー フィールド    | 圧力                              |
| トリガーの演算子 | より大きい                          |
| トリガーの値    | 150                                   |
| 重大度レベル   | 警告                               |
| アラーム イベントのテキスト | Chiller の圧力が 150 PSI を超えました |

新しいルールを保存するには、**[適用]** を選択します。

**[Rules & Actions]\(ルールとアクション\)** ページまたは、**[ダッシュボード]** ページでルールがトリガーされた時間を表示できます。

## <a name="edit-an-existing-rule"></a>既存のルールを編集する

既存のルールを変更するには、ルールの一覧で選択します。 次に、**[Rule Detail]\(ルールの詳細\)** パネルで **[編集モード]** を選択します。

![ルールの編集](media/iot-suite-remote-monitoring-automate/rulesactionsedit.png)

## <a name="disable-a-rule"></a>ルールを無効にする

一時的にルールをオフに切り替えるには、ルールの一覧で無効にできます。 無効にするルールを選択し、**[無効にする]** を選択します。 一覧のルールの **[ステータス]** が、ルールが無効化されたことを示す状態に変わります。 同じ手順を使用して、以前無効にしたルールを再度有効にすることができます。

![ルールを無効にする](media/iot-suite-remote-monitoring-automate/rulesactionsdisable.png)

一覧で複数のルールを選択すると、同時に複数のルールを有効または無効にすることができます。

## <a name="delete-a-rule"></a>規則を削除する

ルールを完全に削除するには、ルールの一覧でルールを選択し、**[削除]** を選択します。

一覧で複数のルールを選択すると、同時に複数のルールを削除できます。

## <a name="next-steps"></a>次のステップ

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