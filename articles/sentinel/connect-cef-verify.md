---
title: Azure Sentinel への接続を検証する | Microsoft Docs
description: セキュリティ ソリューションの接続を検証して、CEF メッセージが Azure Sentinel に転送されていることを確認します。
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
ms.openlocfilehash: e224f6d5cfd82dfc6cb1ce107d111ee0e031247b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588435"
---
# <a name="step-3-validate-connectivity"></a>手順 3:接続の検証



エージェントをデプロイし、セキュリティ ソリューションを構成して CEF メッセージを転送したら、この記事を使って Azure Sentinel とセキュリティ ソリューションとの接続を確認する方法を理解します。 

## <a name="how-to-validate-connectivity"></a>接続を検証する方法

1. Log Analytics を開いて、CommonSecurityLog スキーマを使用してログを受信していることを確認します。<br> ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 

1. スクリプトを実行する前に、セキュリティ ソリューションからメッセージを送信して、構成した Syslog プロキシ マシンに転送されていることを確認することをお勧めします。 
1. マシンに対する管理者特権のアクセス許可 (sudo) が必要です。 コマンド `python –version` を使用して、コンピューターに Python がインストールされていることを確認します。
1. 次のスクリプトを実行して、エージェント、Azure Sentinel、セキュリティ ソリューションの間の接続を確認します。 これにより、デーモンの転送が正しく構成されていること、正しいポートでリッスンしていること、デーモンと Log Analytics エージェントの間の通信がブロックされていないことが確認されます。 また、このスクリプトは、モック メッセージ「TestCommonEventFormat」を送信して、エンドツーエンドの接続を確認します。 <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>次のステップ
このドキュメントでは、CEF アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。

