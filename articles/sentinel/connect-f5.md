---
title: Azure Sentinel に F5 ASM データを接続する | Microsoft Docs
description: F5 ASM データ コネクタを使用して、F5 ASM ログを Azure Sentinel にプルする方法について説明します。 F5 ASM データをブックに表示し、アラートを作成し、調査を改善します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/20/2020
ms.author: yelevin
ms.openlocfilehash: 632b55b699df409049f77abecfca4c3cf75d3e73
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252672"
---
# <a name="connect-f5-asm-to-azure-sentinel"></a>F5 ASM を Azure Sentinel に接続する

この記事では、F5 ASM データ コネクタを使用して、F5 ASM ログを Azure Sentinel に簡単にプルする方法について説明します。 これにより、ブックに F5 ASM データを表示し、それを使用してカスタムアラートを作成して組み込むことで、調査を向上させることができます。 Azure Sentinel に F5 ASM データがあることで、組織で使用している Web アプリケーションのセキュリティに関するより多くの分析情報が得られ、セキュリティ操作機能が強化されます。 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="configure-your-f5-asm-to-send-cef-messages"></a>CEF メッセージを送信するように F5 ASM を構成する

1. [F5 のアプリケーション セキュリティ イベント ログの構成](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)に関するページを参照し、次のガイドラインを使用して、リモート ログを設定してください。
   - **[Remote storage type]** (リモート ストレージの種類) を **[CEF]** に設定します。
   - **[Protocol]\(プロトコル\)** を **[TCP]** に設定します。
   - **[IP アドレス]** を Syslog サーバーの IP アドレスに設定します。
   - **ポート番号** を **514** またはエージェントで使用するように設定するポートに設定します。
   - **[Maximum Query String Size]** (クエリ文字列の最大サイズ) を、エージェントで設定するサイズに設定できます。

1. Log Analytics で CEF イベントに関連するスキーマを使用するために、`CommonSecurityLog` を検索します。

1. 「[手順 3:接続の検証](connect-cef-verify.md)」に進みます。


## <a name="next-steps"></a>次のステップ
このドキュメントでは、F5 ASM を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](./detect-threats-built-in.md)の概要。
- [ブックを使用](monitor-your-data.md)してデータを監視する。