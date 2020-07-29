---
title: Proofpoint Targeted Attack Protection (TAP) データを Azure Sentinel に接続する | Microsoft Docs
description: Proofpoint Targeted Attack Protection (TAP) データを Azure Sentinel に接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: e4185de879fa6136531e6d4c64908befa1d3bc45
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86528067"
---
# <a name="connect-your-proofpoint-tap-to-azure-sentinel-with-azure-function"></a>Azure Functions を使用して Proofpoint TAP を Azure Sentinel に接続する

Proofpoint Targeted Attack Protection (TAP) コネクタを使用すると、Azure Sentinel にすべての [Proofpoint TAP](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection) セキュリティ ソリューションのログを簡単に接続して、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 Proofpoint TAP と Azure Sentinel の統合では、Azure Functions を使用して REST API によりログ データをプルします。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-proofpoint-tap"></a>Proofpoint TAP を構成して接続する

Azure Functions では、Proofpoint TAP から直接イベントとログを統合およびプルし、Azure Sentinel に転送できます。

1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[Proofpoint TAP]** コネクタを選択します。

1. **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. **[Proofpoint TAP]** ページの指示に従います。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、Log Analytics の **ProofpointTAPMessagesBlocked_CL**、**ProofpointTAPMessagesDelivered_CL**、**ProofpointTAPClicksPermitted_CL**、および **ProofpointTAPClicksBlocked_CL** テーブルの下にデータが表示されます。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Functions アプリを使用して Proofpoint TAP を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
