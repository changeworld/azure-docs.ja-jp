---
title: Azure Sentinel に F5 データを接続する | Microsoft Docs
description: Azure Sentinel に F5 データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 6f33cecca1c67f91d0f2be64ab156f45ee500521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588265"
---
# <a name="connect-f5-to-azure-sentinel"></a>F5 を Azure Sentinel に接続する

この記事では、F5 アプライアンスを Azure Sentinel に接続する方法について説明します。 F5 データ コネクタを使用すると、Azure Sentinel に F5 のログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、および調査の改善も行うことができます。 Azure Sentinel で F5 を使用すると、組織のインターネット使用状況に関するより多くの洞察が得られ、セキュリティ操作機能が強化されます。 

## <a name="configure-your-f5-to-send-cef-messages"></a>CEF メッセージを送信するように F5 を構成する

1. F5 の[アプリケーション セキュリティ イベント ログの構成](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)に関するページを参照して、次のガイドラインを使用して、リモート ログを設定する手順に従います。
   - **[Remote storage type]** (リモート ストレージの種類) を **[CEF]** に設定します。
   - **[Protocol]\(プロトコル\)** を **[TCP]** に設定します。
   - **[IP アドレス]** を Syslog サーバーの IP アドレスに設定します。
   - **ポート番号**を **514** またはエージェントで使用するように設定するポートに設定します。
   - **[Maximum Query String Size]** (クエリ文字列の最大サイズ) を、エージェントで設定するサイズに設定できます。

1. Log Analytics で CEF イベントに関連するスキーマを使用するために、`CommonSecurityLog` を検索します。

1. 「[手順 3: 接続の検証](connect-cef-verify.md)」に進みます。


## <a name="next-steps"></a>次のステップ
このドキュメントでは、F5 を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。

