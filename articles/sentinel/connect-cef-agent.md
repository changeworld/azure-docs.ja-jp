---
title: エージェントをデプロイして CEF データを Azure Sentinel Preview に接続する |Microsoft Docs
description: Azure Sentinel に CEF データを接続する方法について説明します。
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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: b0c9335357cb793ea76e1dbe68575f716a50372a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588469"
---
# <a name="step-1-deploy-the-agent"></a>手順 1:エージェントをデプロイする


このステップでは、Azure Sentinel とセキュリティ ソリューションの間のプロキシとして機能する Linux マシンを選択する必要があります。 プロキシ マシンで次のスクリプトを実行する必要があります。
- Log Analytics エージェントをインストールし、Syslog メッセージをリッスンするために必要に応じて構成します。
- TCP ポート 514 を使用して Syslog メッセージをリッスンするように Syslog デーモンを構成し、TCP ポート 25226 を使用して CEF メッセージだけを Log Analytics エージェントに転送します。
- Syslog エージェントを設定して、データを収集して安全に Azure Sentinel に送信し、そこでデータが解析および補強されるようにします。
 
## <a name="deploy-the-agent"></a>エージェントをデプロイする
 
1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[Common Event Format (CEF)]** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。 

1. **[Install and configure the Syslog agent]\(Syslog エージェントのインストールと構成\)** で、マシンの種類として Azure、他のクラウド、またはオンプレミスを選択します。 
   > [!NOTE]
   > 次の手順のスクリプトでは Log Analytics エージェントをインストールし、コンピューターを Azure Sentinel ワークスペースに接続するため、このコンピューターが他のワークスペースに接続されていないことを確認してください。
1. マシンに対する管理者特権のアクセス許可 (sudo) が必要です。 コマンド `python –version` を使用して、コンピューターに Python がインストールされていることを確認します。

1. プロキシ マシンで次のスクリプトを実行します。
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. スクリプトの実行中に、エラーまたは警告メッセージが表示されないことを確認してください。

[手順 2 に進みます:セキュリティソリューションを構成して、CEF メッセージを転送するようにします](connect-cef-solution-config.md) 。


## <a name="next-steps"></a>次のステップ
このドキュメントでは、CEF アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。

