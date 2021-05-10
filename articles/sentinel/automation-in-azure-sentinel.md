---
title: Azure Sentinel の正規化はじめに | Microsoft Docs
description: この記事では、Azure Sentinel のセキュリティ オーケストレーション、オートメーション、応答 (SOAR) の機能について説明し、その SOAR コンポーネントであるオートメーション ルールとプレイブックについて説明します。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 54d7c997ce17c927a692e84f6094e3a08f707af7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608831"
---
# <a name="security-orchestration-automation-and-response-soar-in-azure-sentinel"></a>Azure Sentinel でのセキュリティ オーケストレーション、オートメーション、および応答 (SOAR)

この記事では、Azure Sentinel のセキュリティ オーケストレーション、オートメーション、応答 (SOAR) の機能について説明します。また、セキュリティの脅威に応えて自動化ルールとプレイブックを使用することにより、SOC の有効性が向上し、時間とリソースを節約できます。

## <a name="azure-sentinel-as-a-soar-solution"></a>SOAR ソリューションとしての Azure Sentinel

### <a name="the-problem"></a>問題

通常、SIEM または SOC チームには、セキュリティ アラートやインシデントが日常的に押し寄せています。その量のあまりの多さに、対応するスタッフは圧倒されています。 この結果、多くのアラートが無視され、多くのインシデントが調査されず、気付かずに行われる攻撃に対して組織は脆弱な状態のままになっていることが非常によくあります。

### <a name="the-solution"></a>解決策

Azure Sentinel は、セキュリティ情報およびイベント管理 (SIEM) システムに加えて、セキュリティオーケストレーション、オートメーション、および応答 (SOAR) のためのプラットフォームでもあります。 主な目的の 1 つは、セキュリティ オペレーション センターとスタッフ (SOC/SecOps) が担当する、定期的で予測可能な強化、応答、および修復のタスクを自動化することです。これにより、アップタイムやリソースが解放され、高度な脅威を詳細に調査したり検索したりできます。 オートメーションは、インシデント処理と応答の自動化を一元的に管理するオートメーション ルールから、事前に定義された一連のアクションを実行して脅威対応タスクに強力で柔軟な高度な自動化を提供するプレイブックに、Azure Sentinel でいくつかの異なるフォームを使用します。

## <a name="automation-rules"></a>オートメーション ルール

オートメーション ルールとは、Azure Sentinel における新しい概念です。 この機能により、ユーザーはインシデント処理の自動化を一元的に管理できます。 自動化ルールを使用すると、(以前のようにアラートにだけでなく) インシデントにもプレイブックを割り当てられるほか、一度に複数の分析ルールに対する応答を自動化したり、プレイブックを必要とせずにインシデントのタグ付け、割り当て、クローズを自動的に行ったり、実行されるアクションの順序を制御したりできます。 オートメーション ルールにより、Azure Sentinel での自動化の使用が効率化され、インシデント オーケストレーション プロセスの複雑なワークフローを簡略化できます。

詳細については、「[オートメーション ルールの詳細な説明](automate-incident-handling-with-automation-rules.md)」ページを参照してください。

> [!IMPORTANT]
>
> - **オートメーション ルール** 機能は現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="playbooks"></a>プレイブック

プレイブックは、Azure Sentinel からルーチンとして実行できる応答と修復アクションやロジックのコレクションです。 プレイブックは、脅威への対応を自動化し、調整するのに役立ちます。これは、内外の他のシステムと統合でき、分析ルールまたは自動化ルールによってトリガーされたときに、それぞれ、特定のアラートやインシデントに対応して自動的に実行されるように設定できます。 また、インシデント ページから、アラートに応じて手動でオンデマンドで実行することもできます。

Azure Sentinel のプレイブックは、エンタープライズ全体のシステムでタスクとワークフローをスケジュール、自動化、および調整するのに役立つクラウド サービスである [Azure Logic Apps](../logic-apps/logic-apps-overview.md) で作成されたワークフローに基づいています。 つまり、プレイブックは、Logic Apps の統合とオーケストレーションの機能、使いやすいデザインツール、階層 1 の Azure サービスのスケーラビリティ、信頼性、サービス レベルのすべての機能とカスタマイズ性を活用できます。

詳細については、「[プレイブックの詳細な説明](automate-responses-with-playbooks.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Sentinel がオートメーションを使用して、SOC の効果と効率を向上させる方法について学習しました。

- インシデント処理の自動化の詳細については、「[Azure Sentinel でのインシデント処理の自動化](automate-incident-handling-with-automation-rules.md)」を参照してください。
- 高度な自動化オプションの詳細については、「 [Azure Sentinel でのプレイブックを使用した脅威の応答の自動化](automate-responses-with-playbooks.md)」を参照してください。
- オートメーション ルールとプレイブックを実装する方法については、「[チュートリアル: Azure Sentinel での脅威応答の自動化」を](tutorial-respond-threats-playbook.md)参照してください。
