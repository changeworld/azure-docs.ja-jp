---
title: Infoblox Network Identity Operating System (NIOS) データを Azure Sentinel に接続する | Microsoft Docs
description: Infoblox Network Identity Operating System (NIOS) データを Azure Sentinel に接続する方法について説明します。
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
ms.openlocfilehash: ed4f2d769dbda3dec7b353fddfd1e5e0f3d00f9b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86528263"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>Infoblox NIOS を Azure Sentinel に接続する

この記事では、[Infoblox Network Identity Operating System (NIOS) アプライアンス](https://www.infoblox.com/glossary/network-identity-operating-system-nios/)を Azure Sentinel に接続する方法について説明します。 Infoblox NIOS データ コネクタを使用すると、Azure Sentinel に Infoblox のログを簡単に接続し、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 Infoblox NIOS と Azure Sentinel 間の統合には、Syslog を使用します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>Infoblox のログを Syslog エージェントに転送する  

Syslog のエージェントを介して Azure ワークスペースに Syslog メッセージを転送するように Infoblox を構成します。

1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[Infoblox NIOS]** コネクタを選択します。

1. **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. **[Infoblox NIOS]** ページの指示に従います。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、Log Analytics の Syslog の下にデータが表示されます。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Infoblox NIOS を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。