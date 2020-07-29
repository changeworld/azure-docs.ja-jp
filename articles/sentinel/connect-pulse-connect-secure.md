---
title: Azure Sentinel に Pulse Connect Secure データを接続する | Microsoft Docs
description: Azure Sentinel に Pulse Connect Secure データを接続する方法について説明します。
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
ms.openlocfilehash: 01d41b20c543262280e215cf4e31fd0cccce877f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86528066"
---
# <a name="connect-your-pulse-connect-secure-to-azure-sentinel"></a>Pulse Connect Secure を Azure Sentinel に接続する

この記事では、[Pulse Connect Secure](https://www.pulsesecure.net/products/pulse-connect-secure/) アプライアンスを Azure Sentinel に接続する方法について説明します。 Pulse Connect Secure データ コネクタを使用すると、Azure Sentinel に Pulse Connect Secure のログを簡単に接続して、ダッシュ ボードの表示、カスタム アラートの作成、および調査の改善を行うことができます。 Pulse Connect Secure と Azure Sentinel 間の統合には、Syslog を使用します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="forward-pulse-connect-secure-logs-to-the-syslog-agent"></a>Pulse Connect Secure ログを Syslog エージェントに転送する  

Syslog のエージェントを介して Azure ワークスペースに Syslog メッセージを転送するように Pulse Connect Secure を構成します。

1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[Pulse Connect Secure]** コネクタを選択します。

1. **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. **[Pulse Connect Secure]** ページの指示に従います。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、Log Analytics の Syslog の下にデータが表示されます。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Pulse Connect Secure を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。