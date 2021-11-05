---
title: ほぼリアルタイム (NRT) の検出分析ルールを使用して、Azure Sentinel |Microsoft Docs
description: この記事では、ほぼリアルタイム (NRT) の検出分析ルールを表示して作成する方法について説明Azure Sentinel。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/29/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 24492258021e3fc50a94926baad6578631b06125
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091610"
---
# <a name="work-with-near-real-time-nrt-detection-analytics-rules-in-azure-sentinel"></a>ほぼリアルタイム (NRT) の検出分析ルールを使用して、Azure Sentinel

> [!IMPORTANT]
>
> - ほぼリアルタイム (NRT) の規則は、現在プレビュー中 **です**。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Azure Sentinelの[ニア・リアルタイム分析ルール](near-real-time-rules.md)は、すぐにでも最新の脅威検知を提供します。 この種類のルールは、クエリを 1 分間隔で実行することで応答性が高く設計されています。

当分の間、これらのテンプレートの適用は制限されますが、テクノロジは急速に進化し、成長しています。

## <a name="view-near-real-time-nrt-rules"></a>ほぼリアルタイム (NRT) の規則を表示する

1. Azure Sentinel のナビゲーション メニューから **[分析]** を選択します。

1. [分析 **] ブレードの** **[アクティブなルール]** タブで **、NRT** テンプレートの一覧をフィルター処理します：

    1. **[ルールの種類]** フィルターをクリックし、下に表示されるドロップダウン リストをクリックします。

    1. [すべて選択 **] のマークを解除** し **、NRT をマークします**。

    1. 必要に応じて、ドロップダウン リストの上部をクリックして取り消してから、 **[OK]** をクリックします。

## <a name="create-nrt-rules"></a>NRT 規則を作成する

NRT ルールは、定期的なスケジュールされたクエリ分析ルールを作成 [するのと同じ方法で作成します](detect-threats-custom.md)：

1. Azure Sentinel のナビゲーション メニューから **[分析]** を選択します。

1. ボタン **バーから** [作成] を選択し、ドロップダウン リストから **[NRT** クエリ規則] を選択します。

    :::image type="content" source="media/create-nrt-rules/create-nrt-rule.png" alt-text="新しい NRT 規則を作成します。":::

1. 分析ルール ウィザードの指示 [**に従います**](detect-threats-custom.md)。

    NRT ルールの構成は、ほとんどの点で、スケジュールされた分析ルールの構成と同じです。 

    - クエリ ロジックで [**ウォッチリスト**](watchlists.md) と [**脅威インテリジェンス フィード**](understand-threat-intelligence.md) を参照できます。

    - すべてのアラート エンリッチメントメソッド ([**エンティティ マッピング**](map-data-fields-to-entities.md)、[**カスタムの詳細**](surface-custom-details-in-alerts.md)、および [**アラートの詳細**](customize-alert-details.md))を使用できます。

    - アラートをインシデントにグループ化する方法と、特定の結果が生成された場合にクエリを抑制する方法を選択できます。

    - アラートとインシデントの両方に対する応答を自動化できます。

    ただし [ **、NRT ルール**](near-real-time-rules.md#considerations)の性質と制限により、スケジュールされた分析ルールの次の機能は *ウィザードでは使用* できません：

    - **クエリのスケジュール** 設定は構成できません。クエリは、1 分のルックバック期間で 1 分に 1 回実行する自動的にスケジュール設定されます。 
    - **アラートは** 常に生成されるので、アラートのしきい値は関係ありません。
    - **イベントは常** に、イベントをキャプチャするルールによって作成されたアラートにグループ化されるので、イベント グループ構成は使用できません。 NRT ルールでは、イベントごとにアラートを生成できません。

    さらに、クエリ自体には次の要件があります：

    - クエリ自体は 1 つのテーブルのみを参照できます。また、共用体や結合を含めすることはできません。

    - ワークスペース間でクエリを実行できません。

    - アラートのサイズ制限により、クエリではステートメントを使用して、テーブルから必要なフィールドのみを含 `project` める必要があります。 そうしないと、表示する情報が切り捨てられる可能性があります。

## <a name="next-steps"></a>次の手順

このドキュメントでは、ほぼリアルタイム (NRT) の分析ルールを Azure Sentinel。

- のほぼリアルタイム [(NRT)](near-real-time-rules.md)分析ルールの詳細については、Azure Sentinel。
- その他の[分析ルールの種類](detect-threats-built-in.md)を確認します。
