---
title: Azure Sentinel でほぼリアルタイム (NRT) の分析ルールを使用して脅威をすばやく検出する | Microsoft Docs
description: この記事では、Microsoft Sentinel で新しい NRT (ほぼリアルタイム) 分析ルールを利用し、脅威を速やかに検出する方法について説明します。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4cafdcc07ec0cfcd5aa3a7731c7a9571441183fd
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712930"
---
# <a name="detect-threats-quickly-with-near-real-time-nrt-analytics-rules-in-microsoft-sentinel"></a>Microsoft Sentinel でほぼリアルタイム (NRT) の分析ルールを使用し、脅威をすばやく検出する

> [!IMPORTANT]
>
> - ほぼリアルタイム (NRT) の規則は、現在 **プレビュー** 中です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="what-are-near-real-time-nrt-analytics-rules"></a>ほぼリアルタイム (NRT) 分析ルールとは？

セキュリティの脅威に直面した場合、時間と速度が本質的です。 脅威を含め、迅速に分析して対応できるよう、脅威が実現する場合は、脅威に注意する必要があります。 Microsoft Sentinel のほぼリアルタイム (NRT) 分析ルールを使用すると、オンプレミス SIEM に匹敵するスピードで脅威を検出し、特定のシナリオで応答時間を短縮できます。

Microsoft Sentinel の[ほぼリアルタイム分析ルール](detect-threats-built-in.md#nrt)では、面倒な設定なし、かつ最新の方法で脅威を検出できます。 この型のルールは、わずか1分サイクル間隔でクエリを実行することにより、応答性が高くなるように設計されています。

## <a name="how-do-they-work"></a>どのように動作するでしょうか。

NRT ルールは、1 分ごとに 1 回実行し、前の 1 分間に取り込まれたイベントをキャプチャして、可能な限り最新の情報を提供できるハードコードされています。

インジェストのタイム ラグを考慮して組み込みの 5 分間の延期期間で実行される定期的なスケジュールされたルールとは異なり、NRT ルールはわずか 2 分の延期期間で実行され、ソースでの生成時間 (TimeGenerated フィールド) ではなく、イベントのインジェスト時間に対してクエリを実行することで、インジェスト延期期間の問題を解決します。 この結果により、検出の周波数と正確性の両方が向上します。 (この問題をもっと完全に理解するには、「[クエリのスケジュール設定とアラートのしきい値](detect-threats-custom.md#query-scheduling-and-alert-threshold)」と「[スケジュールされた分析ルールでのインジェスト遅延の処理](ingestion-delay.md)」を参照してください)

NRT ルールには、スケジュールされた分析ルールとしての機能と特徴の多くがあります。 アラート エンリッチメント機能の完全なセットを使用できます。エンティティをマップし、カスタムの詳細をサーフェスし、アラートの詳細の動的コンテンツを構成できます。 アラートをインシデントにグループ化する方法を選択し、結果を生成した後にクエリの実行を一時的に抑制できます。また、ルールから生成されたアラートやインシデントに応答して実行する自動化ルールとプレイブックを定義できます。

当分の間、これらのテンプレートの適用は制限されますが、テクノロジは急速に進化し、成長しています。

## <a name="considerations"></a>考慮事項
現在、NRT 規則の使用には、以下の制限事項が適用されます：

1. 現時点では、顧客ごとに 20 を超えるルールを定義できます。

1. この型のルールは新しいので、その構文は現在制限されますが、徐々に進化します。 そのため、現時点では、以下の制限が適用されます：

    1. NRT 規則で定義されたクエリは、 **1 つのテーブルのみ** を参照できます。 ただし、クエリでは、複数のウォッチリストと脅威インテリジェンス フィードを参照できます。

    1. 和集合または結合を使用することはできません。

    1. このルールの型はほぼリアルタイムなので、組み込みの延期期間を最小 (2 分) に短縮しました。

    1. NRT ルールでは、イベント世代時間 (TimeGenerated フィールドで表されます) ではなくインジェスト時間が使用されます。データ ソースの延期期間とインジェスト時間の待機時間は無視しても問題ない (上記を参照)。

    1. クエリは、1 つのワークスペースでのみ実行できます。 ワークスペース間の機能はありません。

    1. イベントグループ化はありません。 NRT ルールでは、適用可能なすべてのイベントをグループに含む 1 つのアラートが生成されます。

## <a name="next-steps"></a>次の手順

このドキュメントでは、Microsoft Sentinel のほぼリアルタイム (NRT) 分析ルールのしくみを説明しました。

- [NRT 規則を作成する](create-nrt-rules.md)方法について学習します。
- [ほかの種類の分析ルール](detect-threats-built-in.md)についてご確認ください。
