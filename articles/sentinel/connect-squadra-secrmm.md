---
title: Squadra Technologies secRMM のデータを Azure Sentinel に接続する | Microsoft Docs
description: Squadra Technologies secRMM のデータを Azure Sentinel に接続する方法について説明します。
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588112"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Squadra Technologies secRMM のデータを Azure Sentinel に接続する 

> [!IMPORTANT]
> Azure Sentinel の Squadra Technologies Security リムーバブル メディア マネージャー (secRMM) データ コネクタは、現在、パブリック プレビューです。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


Squadra Technologies secRMM コネクタを使用すると、Squadra Technologies secRMM セキュリティ ソリューション ログを Azure Sentinel に簡単に接続できます。 ダッシュボードを表示したり、カスタム アラートを作成したり、調査を改善することができます。 このコネクタは、USB リムーバブル ストレージのイベントに関する分析情報を提供します。 Squadra Technologies secRMM と Azure Sentinel との統合には、REST API を使用します。


> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Squadra Technologies secRMM を構成して接続する 

Squadra Technologies secRMM では、ログを統合して、Azure Sentinel に直接エクスポートできます。
1. Azure Sentinel ポータルで、[データ コネクタ] をクリックし、Squadra Technologies secRMM を選択してから、[コネクタ ページを開く] を選択します。

2. [Azure Sentinel の Squadra Technologies のオンボード ガイドに](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf)記載されている手順に従って、Azure Sentinel で Squadra secRMM データを取得します。   


## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、Log Analytics の CustomLogs secRMM_CL の下にデータが表示されます。
Log Analytics で Squadra Technologies secRMM に関連するスキーマを使用するために、secRMM_CL を検索します。

## <a name="validate-connectivity"></a>接続の検証
ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 


## <a name="next-steps"></a>次のステップ
このドキュメントでは、Squadra Technologies secRMM を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。

