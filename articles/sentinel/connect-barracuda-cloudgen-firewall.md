---
title: Barracuda CloudGen Firewall を Azure Sentinel に接続する | Microsoft Docs
description: Barracuda CloudGen Firewall を Azure Sentinel に接続する方法について説明します。
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
ms.openlocfilehash: 1ff3d5481b02e587d993bb4a5d7abf6aecd22ee8
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254054"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>Barracuda CloudGen Firewall の接続

Barracuda CloudGen Firewall (CGFW) コネクタを使用すると、Barracuda CGFW のログを Azure Sentinel に簡単に接続して、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 これにより、組織のネットワークに関するより詳細な分析情報が得られ、セキュリティ運用機能が向上します。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可。

- Syslog 経由でログをエクスポートするように Barracuda CloudGen Firewall を構成する必要があります。

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>Azure Sentinel を Barracuda CloudGen Firewall に接続する

1. Azure portal で、 **[Azure Sentinel]**  >  **[Data connectors]\(データ コネクタ\)** に移動し、 **[Barracuda CloudGen Firewall]** コネクタを選択します。

1. **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. **[Barracuda CloudGen Firewall]** ページの指示に従います。

## <a name="next-steps"></a>次の手順

このドキュメントでは、Barracuda CloudGen Firewall を Azure Sentinel に接続する方法を説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
- [ブックを使用](monitor-your-data.md)してデータを監視する。