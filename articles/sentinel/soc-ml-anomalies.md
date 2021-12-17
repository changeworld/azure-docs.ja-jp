---
title: Microsoft Sentinel で SOC-ML 異常検知を使用して脅威を検出する | Microsoft Docs
description: この記事では、Microsoft Sentinel の新しい SOC-ML 異常検出機能を使用する方法について説明します。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 233f83083a061bf12caae58172d2c80bab876fcf
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519840"
---
# <a name="use-soc-ml-anomalies-to-detect-threats-in-microsoft-sentinel"></a>Microsoft Sentinel で SOC-ML 異常検知を使用して脅威を検出する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - SOC-ML 異常検知は現在 **プレビュー版** です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="what-are-soc-ml-anomalies"></a>SOC-ML 異常検知とは

攻撃者と防御者がサイバーセキュリティの激しい競争において絶えず優位に立とうとする中、攻撃者は常に検出を回避する方法を見つけ出します。 とはいえ、攻撃においては必ず、攻撃対象のシステムに通常とは異なる挙動が現れます。 機械学習に基づく、Microsoft Sentinel の SOC-ML 異常検知では、追加設定なしですぐに使用できる分析ルール テンプレートによってこの挙動を特定します。 異常そのものが悪意のある挙動や疑わしい動作を意味しているとは限らないものの、それを活用して検出、調査、脅威追求の能力を高めることができます。

- **検出能力を向上するための追加のシグナル**: セキュリティ アナリストは異常を使用して新しい脅威を検出し、既存の検出の効率を高めます。 1 つの異常は悪意のある挙動を示す強力なシグナルではないものの、キル チェーンのさまざまな地点で発生する複数の異常を組み合わせるなら、累積効果は強力なものとなります。 異常によって特定される通常とは異なる挙動をアラート発生の条件に設定することにより、セキュリティ アナリストは既存の検出能力を向上させることもできます。

- **調査における証拠**: セキュリティ アナリストは、調査の際に異常を使用することにより、侵害の確証、調査のための新しい方向性の発見、潜在的な影響の評価を行うことができます。 こうした効率化により、セキュリティ アナリストは調査に費やす時間を短縮することができます。

- **プロアクティブな脅威の追及の出発点**: 脅威を追求する担当者たちは、自分たちのクエリによって疑わしい挙動が明らかになったかどうかを判断するための状況証拠として、異常を活用できます。 挙動が疑わしいものである場合、さらなる追及のために進むべき方向性が異常によって指し示されることにもなります。 異常から得られるこのような手掛かりにより、脅威の検出にかかる時間は短くなり、危害を及ぼすおそれは小さくなります。

異常検知は強力なツールですが、ノイズが多いことでも有名です。 通常、特定の環境に合わせるための退屈なチューニングや複雑な後処理が必要になります。 Microsoft Sentinel SOC-ML の異常検知テンプレートは Microsoft のデータ サイエンス チームによってチューニングされ、追加設定なしにすぐに使える値が備わっていますが、さらにチューニングが必要な場合でも、その手順はシンプルで機械学習の知識は必要ありません。 多くの異常のしきい値やパラメーターは構成可能で、使い慣れた分析ルールのユーザー インターフェイスを使用して微調整できます。 インターフェイス内で元のしきい値およびパラメーターのパフォーマンスと新しいものとを比較でき、テスト中やフライト化の段階で必要に応じてさらに調整できます。 異常検知がパフォーマンスの目標を満たしたら、新しいしきい値またはパラメーターによる異常検知を、ボタンをクリックするだけで運用環境にレベル上げできます。 Microsoft Sentinel SOC-ML 異常検知を使用すると、労力をかけずに異常検知の利点を得ることができます。

## <a name="next-steps"></a>次の手順

このドキュメントでは、Microsoft Sentinel で SOC-ML がどのように異常の検出に役立つかを学びました。

- [異常ルールの表示、作成、管理、微調整](work-with-anomaly-rules.md)の方法をご確認ください。
- [ほかの種類の分析ルール](detect-threats-built-in.md)についてご確認ください。
