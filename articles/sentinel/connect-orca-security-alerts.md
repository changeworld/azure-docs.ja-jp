---
title: Orca アラートを Azure Sentinel に接続する | Microsoft Docs
description: Orca セキュリティ アラート データを Azure Sentinel に接続して、ダッシュボードの表示、カスタム アラートの作成、および調査の改善を行う方法を説明します。
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
ms.openlocfilehash: 1f2f67281079142f0c4fe4985738dc48332a107d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86528299"
---
# <a name="connect-your-orca-alerts-to-azure-sentinel"></a>Orca アラートを Azure Sentinel に接続する 

Orca セキュリティ アラート コネクタを使用すると、[Orca アラート](https://orca.security/)のセキュリティ ソリューション アラートを Azure Sentinel に簡単に取り込むことができます。これにより、アラートをブック内で表示し、これを利用してカスタム アラートを作成し、調査を向上させる目的で組み入れることができます。 Orca セキュリティ アラートと Azure Sentinel 間の統合には、REST API を使用します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-orca-security-alerts"></a>Orca セキュリティ アラートを構成して接続する

Orca セキュリティ アラートでは、ログを統合して、Azure Sentinel に直接エクスポートできます。

1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[Orca Security alerts]\(Orca セキュリティ アラート\)** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。

2. Orca プラットフォームから統合を完了するには、 https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integration を参照してください。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、**OrcaAlerts_CL** の表の **CustomLogs** にある Log Analytics にデータが表示されます。
Log Analytics で Orca アラートに関連するスキーマを使用するには、`OrcaAlerts_CL` を検索します。

## <a name="validate-connectivity"></a>接続の検証
ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 


## <a name="next-steps"></a>次のステップ
このドキュメントでは、Orca セキュリティ アラートを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。

