---
title: One Identity Safeguard を Azure Sentinel に接続する | Microsoft Docs
description: One Identity Safeguard を Azure Sentinel に接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: c9af01ac6e033de79ce4cac22b5b941239eda9e7
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253176"
---
# <a name="connect-one-identity-safeguard"></a>One Identity Safeguard を接続する
One Identity Safeguard データ コネクタは、標準の Common Event Format (CEF) コネクタを Safeguard for Privileged Sessions 固有のダッシュボードで拡張します。 このコネクタを使用して、デバイスによって生成されるイベントを視覚化、アラート、調査などの目的で簡単に使用できます。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可。

## <a name="connect-azure-sentinel-to-one-identity-safeguard"></a>Azure Sentinel を One Identity Safeguard に接続する

1. Azure portal で、 **[Azure Sentinel]**  >  **[Data connectors]\(データ コネクタ\)** に移動し、 **[One Identity Safeguard]** コネクタを選択します。

2. **[Open connector page]\(コネクタ ページを開く\)** を選択します。

3. **[One Identity Safeguard]** ページに記載されている手順に従います。


## <a name="next-steps"></a>次のステップ
このドキュメントでは、One Identity Safeguard を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
- [ブックを使用](monitor-your-data.md)してデータを監視する。