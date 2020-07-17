---
title: F5 BIG IP データを Azure Sentinel に接続する | Microsoft Docs
description: F5 BIG IP データを Azure Sentinel に接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: ae361c74b261bdd6a5673040f868392282b573ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588282"
---
# <a name="connect-your-f5-big-ip-appliance"></a>F5 BIG-IP アプライアンスを接続する 

> [!IMPORTANT]
> Azure Sentinel の F5 BIG-IP データ コネクタは、現在パブリック プレビュー段階にあります。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

F5 BIG-IP コネクタを使用すると、すべての F5 BIG-IP のログを簡単に Azure Sentinel に接続して、ワークブックの表示、カスタム アラートの作成、調査の改善を行うことができます。 これにより、組織のネットワークに関するより詳しい分析情報が得られ、セキュリティ運用機能が向上します。 F5 BIG-IP と Azure Sentinel の統合には、REST API を使用します。


> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-f5-big-ip"></a>F5 BIG IP を構成して接続する 

F5 BIG-IP では、ログを統合して、Azure Sentinel に直接エクスポートできます。

1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[F5 BIG IP]** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。 
1. F5 BIG IP を接続するには、JSON 宣言をシステムの API エンドポイントにポストする必要があります。 この実行方法の手順については、[F5 BIG-IP と Azure Sentinel との統合](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)に関する記事をご覧ください。
8. F5 BIG IP コネクタのページで、「[Streaming data to Azure Log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics)」の下の説明に従って、ワークスペース ID とプライマリ キーをコピーして貼り付けます。
1. F5 BIG IP の手順を完了すると、Azure Sentinel コネクタのページに、接続されたデータの種類が表示されます。
1. F5 BIG IP イベントに対して関連するスキーマを Log Analytics で使用するには、**F5Telemetry_LTM_CL**、**F5Telemetry_system_CL**、および **F5Telemetry_ASM_CL** を検索してください。


## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 



## <a name="next-steps"></a>次のステップ
このドキュメントでは、F5 BIG IP を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。


