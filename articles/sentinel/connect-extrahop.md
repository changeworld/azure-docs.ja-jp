---
title: Azure Sentinel に ExtraHop Reveal(x) を接続する | Microsoft Docs
description: ExtraHop Reveal(x) を Azure Sentinel に接続する方法について説明します。
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
ms.openlocfilehash: a639e632c695f417f540eca87c170b636701aa4d
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253034"
---
# <a name="connect-extrahop-revealx"></a>ExtraHop Reveal(x) の接続

ExtraHop Reveal(x) データ コネクタを使用すると、ExtraHop Reveal(x) システムを Azure Sentinel に簡単に接続して、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 この統合により、組織のネットワークに関する分析情報を取得できるようになり、セキュリティ運用機能が向上します。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可。

- ファームウェアのバージョンが 7.8 以降で、Unlimited (管理者) 特権を持ったユーザー アカウントが存在する ExtraHop Discover または Command アプライアンス。

## <a name="connect-azure-sentinel-to-extrahop-revealx"></a>Azure Sentinel を ExtraHop Reveal(x) に接続する

1. Azure portal で、 **[Azure Sentinel]**  >  **[Data connectors]\(データ コネクタ\)** に移動し、 **[ExtraHop Reveal(x)]** コネクタを選択します。

2. **[Open connector page]\(コネクタ ページを開く\)** を選択します。

3. **[ExtraHop Reveal(x)]** ページの指示に従います。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、ExtraHop Reveal(x) を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
- [ブックを使用](monitor-your-data.md)してデータを監視する。