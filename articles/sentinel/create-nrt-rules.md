---
title: Microsoft Sentinel でほぼリアルタイム (NRT) の検出分析ルールを使用する | Microsoft Docs
description: この記事では、Microsoft Sentinel でほぼリアルタイム (NRT) の検出分析ルールを表示および作成する方法について説明します。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 33f6417193944a4596e6900af5c68ae52aa4aca4
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721482"
---
# <a name="work-with-near-real-time-nrt-detection-analytics-rules-in-microsoft-sentinel"></a>Microsoft Sentinel でほぼリアルタイム (NRT) の検出分析ルールを使用する

> [!IMPORTANT]
>
> - ほぼリアルタイム (NRT) の規則は、現在 **プレビュー** 中です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Microsoft Sentinel の[ほぼリアルタイム分析ルール](near-real-time-rules.md)では、面倒な設定なし、かつ最新の方法で脅威を検出できます。 この型のルールは、わずか1分サイクル間隔でクエリを実行することにより、応答性が高くなるように設計されています。

当分の間、これらのテンプレートの適用は制限されますが、テクノロジは急速に進化し、成長しています。

## <a name="view-near-real-time-nrt-rules"></a>ほぼリアルタイム (NRT) の規則を表示する

1. Microsoft Sentinel のナビゲーション メニューから **[分析]** を選択します。

1. [分析 **] ブレードの** **[アクティブなルール]** タブで **、NRT** テンプレートの一覧をフィルター処理します：

    1. **[ルールの種類]** フィルターをクリックし、下に表示されるドロップダウン リストをクリックします。

    1. [すべて選択 **] のマークを解除** し **、NRT をマークします**。

    1. 必要に応じて、ドロップダウン リストの上部をクリックして取り消してから、 **[OK]** をクリックします。

## <a name="create-nrt-rules"></a>NRT 規則を作成する

NRT ルールは、定期的なスケジュールされたクエリ分析ルールを作成 [するのと同じ方法で作成します](detect-threats-custom.md)：

1. Microsoft Sentinel のナビゲーション メニューから **[分析]** を選択します。

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

このドキュメントでは、Microsoft Sentinel でほぼリアルタイム (NRT) の分析ルールを作成する方法について説明しました。

- [Microsoft Sentinel のほぼリアルタイム (NRT) の分析ルール](near-real-time-rules.md)詳細については確認してください。
- その他の[分析ルールの種類](detect-threats-built-in.md)を確認します。
