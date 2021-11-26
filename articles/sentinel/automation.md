---
title: Microsoft Sentinel でのオートメーションの概要 | Microsoft Docs
description: この記事では、Microsoft Sentinel のセキュリティ オーケストレーション、オートメーション、応答 (SOAR) の機能について紹介し、その SOAR コンポーネントであるオートメーション ルールとプレイブックについて説明します。
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
ms.openlocfilehash: d9c547ed30bf0c580526721cdaa48078a4538315
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725441"
---
# <a name="security-orchestration-automation-and-response-soar-in-microsoft-sentinel"></a>Microsoft Sentinel でのセキュリティ オーケストレーション、オートメーション、応答 (SOAR)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Sentinel のセキュリティ オーケストレーション、オートメーション、応答 (SOAR) の機能について説明します。また、セキュリティの脅威に応えて自動化ルールとプレイブックを使用することにより、SOC の有効性が向上し、時間とリソースを節約できることを示します。

## <a name="microsoft-sentinel-as-a-soar-solution"></a>SOAR ソリューションとしての Microsoft Sentinel

### <a name="the-problem"></a>問題

通常、SIEM または SOC チームには、セキュリティ アラートやインシデントが日常的に押し寄せています。その量のあまりの多さに、対応するスタッフは圧倒されています。 この結果、多くのアラートが無視され、多くのインシデントが調査されず、気付かずに行われる攻撃に対して組織は脆弱な状態のままになっていることが非常によくあります。

### <a name="the-solution"></a>解決策

Microsoft Sentinel は、セキュリティ情報およびイベント管理 (SIEM) システムに加えて、セキュリティ オーケストレーション、オートメーション、応答 (SOAR) のためのプラットフォームでもあります。 主な目的の 1 つは、セキュリティ オペレーション センターとスタッフ (SOC/SecOps) が担当する、定期的で予測可能な強化、応答、および修復のタスクを自動化することです。これにより、アップタイムやリソースが解放され、高度な脅威を詳細に調査したり検索したりできます。 Microsoft Sentinel におけるオートメーションは、インシデント処理と応答の自動化を一元的に管理するオートメーション ルールから、事前に定義された一連のアクションを実行して、脅威対応タスクに対し強力かつ柔軟な先進自動化を提供するプレイブックまで、いくつかの異なる形態を取ります。

## <a name="automation-rules"></a>オートメーション ルール

オートメーション ルールとは、Microsoft Sentinel における新しい概念です。 この機能により、ユーザーはインシデント処理の自動化を一元的に管理できます。 自動化ルールを使用すると、(以前のようにアラートにだけでなく) インシデントにもプレイブックを割り当てられるほか、一度に複数の分析ルールに対する応答を自動化したり、プレイブックを必要とせずにインシデントのタグ付け、割り当て、クローズを自動的に行ったり、実行されるアクションの順序を制御したりできます。 オートメーション ルールにより、Microsoft Sentinel での自動化の使用が効率化され、インシデント オーケストレーション プロセスの複雑なワークフローを簡略化できます。

詳細については、「[オートメーション ルールの詳細な説明](automate-incident-handling-with-automation-rules.md)」ページを参照してください。

> [!IMPORTANT]
>
> - **オートメーション ルール** 機能は現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="playbooks"></a>プレイブック

プレイブックは、Microsoft Sentinel からルーチンとして実行できる応答と修復アクションやロジックのコレクションです。 プレイブックは、脅威への対応を自動化し、調整するのに役立ちます。これは、内外の他のシステムと統合でき、分析ルールまたは自動化ルールによってトリガーされたときに、それぞれ、特定のアラートやインシデントに対応して自動的に実行されるように設定できます。 また、インシデント ページから、アラートに応じて手動でオンデマンドで実行することもできます。

Microsoft Sentinel のプレイブックは、エンタープライズ全体のシステムでタスクとワークフローをスケジュール、自動化、調整するのに役立つクラウド サービスである [Azure Logic Apps](../logic-apps/logic-apps-overview.md) で作成されたワークフローに基づいています。 つまり、プレイブックは、Logic Apps の統合とオーケストレーションの機能、使いやすいデザインツール、階層 1 の Azure サービスのスケーラビリティ、信頼性、サービス レベルのすべての機能とカスタマイズ性を活用できます。

詳細については、「[プレイブックの詳細な説明](automate-responses-with-playbooks.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Microsoft Sentinel がオートメーションを使用して、SOC 運用の効果と効率を向上させる方法について学習しました。

- インシデント処理の自動化の詳細については、[Microsoft Sentinel でのインシデント処理を自動化する](automate-incident-handling-with-automation-rules.md)方法に関するページを参照してください。
- 高度な自動化オプションの詳細については、「[Microsoft Sentinel のプレイブックを使用して脅威への対応を自動化する](automate-responses-with-playbooks.md)」を参照してください。
- オートメーション ルールとプレイブックを実装する方法については、[プレイブックを使用して Microsoft Sentinel で脅威への対応を自動化する方法のチュートリアル](tutorial-respond-threats-playbook.md)に関するページを参照してください。
