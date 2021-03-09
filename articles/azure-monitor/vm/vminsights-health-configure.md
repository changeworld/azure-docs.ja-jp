---
title: VM insights のゲストの正常性 (プレビュー) で監視を構成する
description: Azure portal を使用して VM insights のゲストの正常性 (プレビュー) の既定の監視を変更する方法について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/14/2020
ms.openlocfilehash: 0f6599bb9f379cf471dafbb83a7deefbb05c0dbe
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052213"
---
# <a name="configure-monitoring-in-vm-insights-guest-health-preview"></a>VM insights のゲストの正常性 (プレビュー) で監視を構成する
VM insights のゲストの正常性を使用すると、一定間隔でサンプリングされる一連のパフォーマンス測定値によって定義される、仮想マシンの正常性を表示できます。 この記事では、Azure portal を使用して既定の監視を変更する方法について説明します。 また、[データ収集ルールを使用した監視の構成](vminsights-health-configure-dcr.md)に必要なモニターの基本的な概念についても説明します。

## <a name="open-monitor-configuration"></a>モニター構成を開く
Azure portal で、モニターを選択してから **[構成]** タブを選択して、モニター構成を開きます。

[![モニター詳細の [構成]](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>モニターを有効または無効にする
ユニット モニターと集計モニターのいずれにも、モニターを有効または無効にできる **[Health monitor status]\(正常性モニターの状態\)** 設定があります。 モニターを有効にすると、その正常性が表示され、VM の正常性の設定に使用されます。 モニターが無効になっている場合、その正常性は計算されず、VM の正常性の設定には使用されません。

| 設定 | 説明 |
|:---|:---|
| Enabled | モニターは、親の設定に関係なく有効になります。 |
| 無効 | モニターは、親の設定に関係なく無効になります。 |
| 親と同じ | モニターは、親の設定によって有効または無効になります。 |

モニターが無効になっている場合、次の例に示すように、すべての基準が使用不可として表示されます。

![無効になっているモニター](media/vminsights-health-configure/disabled-monitor.png)


> [!NOTE]
> 親モニターが無効になっている場合は、すべての子モニターも無効になります。 子モニターを明示的に有効にすると、親も有効になりますが、その構成の状態は変わりません。 この場合は、親モニターで次のメッセージを受け取ります。
>
> "*モニターの構成済みの状態が '無効' になっているため不一致が生じていますが、正常性状態にそれが反映されていません。これは、構成された変更が伝達されているか、その子モニターのいずれかが明示的に有効にされているためです。* "

## <a name="enable-or-disable-virtual-machine"></a>仮想マシンを有効または無効にする
ある VM の監視を無効にして、すべてのモニターを一時的に停止することができます。 メンテナンスを実行している場合などに、VM の監視を無効にすることがあります。

| 設定 | 説明 |
|:---|:---|
| Enabled  | コンピューターの正常性状態が表示されます。 |
| 無効 | コンピューターの正常性状態が **無効** として表示されます。 アラートは作成されません。 |

## <a name="health-state-logic"></a>正常性状態のロジック
ユニット モニターの正常性状態のロジックでは、その正常性状態を設定するための基準を定義します。 モニターに設定する正常性状態の数と、各正常性状態がどのように計算されるかのしきい値を指定できます。

![サンプルの正常性基準](media/vminsights-health-configure/sample-health-criteria.png)

集計モニターでは、正常性状態のロジックを指定しません。 この正常性状態は、その下にあるユニット モニターによって、それらの正常性のロールアップに従って設定されます。

ユニット モニターにはそれぞれ、2 つまたは 3 つの正常性状態があります。 それぞれには、必ず正常状態と、必要に応じて警告状態、クリティカル状態、またはその両方があります。 警告およびクリティカルの状態にはそれぞれ、どのような場合にモニターがこの状態に設定されるかを定義する一意の基準が必要です。 正常状態は、他の状態の基準が満たされない場合に設定されるため、この状態には基準がありません。

次の例では、CPU 使用率が次の正常性状態に設定されています。

- 90% を超える場合は、クリティカル。
- 80% 以上の場合は、警告。
- 80% 未満の場合は、正常。 これは、他の条件のいずれも当てはまらない場合という条件であるため、暗黙的に指定されます。

## <a name="next-steps"></a>次のステップ

- [データ収集ルールを使用してモニターを大規模に構成します。](vminsights-health-configure-dcr.md)