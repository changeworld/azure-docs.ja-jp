---
title: Azure Sentinel に Cisco データを接続する | Microsoft Docs
description: Azure Sentinel に Cisco データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 4985593c6fc86f7d80243990c1cce10ce25a2998
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498726"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Cisco ASA を Azure Sentinel に接続する



この記事では、Cisco ASA アプライアンスを Azure Sentinel に接続する方法について説明します。 Cisco ASA データ コネクタを使用すると、Azure Sentinel に Cisco ASA のログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、および調査の改善も行うことができます。 Azure Sentinel で Cisco ASA を使用すると、組織のインターネット使用状況に関するより多くの洞察が得られ、セキュリティ操作機能が強化されます。 


## <a name="how-it-works"></a>動作のしくみ

エージェントを専用の Linux マシン (VM またはオンプレミス) に展開して、Cisco ASA と Azure Sentinel の間の通信をサポートする必要があります。 次の図は、Azure での Linux VM の場合の設定を示しています。

 ![Azure での CEF](./media/connect-cef/cef-syslog-azure.png)

または、別のクラウド内の VM、またはオンプレミスのマシンを使用する場合にも、この設定が存在します。 

 ![オンプレミスの CEF](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>セキュリティに関する考慮事項

組織のセキュリティ ポリシーに従って、コンピューターのセキュリティを構成してください。 たとえば、企業のネットワーク セキュリティ ポリシーに合わせてネットワークを構成し、デーモンのポートとプロトコルを要件に合わせて変更することができます。   [Azure で VM をセキュリティ保護する](../virtual-machines/linux/security-policy.md)方法に関するページと[ネットワーク セキュリティのベスト プラクティス](../security/fundamentals/network-best-practices.md)に関するページの手順を行うと、コンピューターのセキュリティ構成を改善できます。

セキュリティ ソリューションと Syslog コンピューターの間で TLS 通信を使用するには、TLS で通信するように Syslog デーモン (rsyslog または syslog-ng) を構成する必要があります。[TLS -rsyslog で Syslog トラフィックを暗号化する](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)方法に関するページと [TLS –syslog-ng でログ メッセージを暗号化する](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)方法に関するページをご覧ください。

 
## <a name="prerequisites"></a>前提条件
プロキシとして使用する Linux マシンで、次のいずれかのオペレーティング システムが実行されていることを確認します。

- 64 ビット
  - CentOS 6 および 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 および 7
  - Red Hat Enterprise Linux Server 6 および 7
  - Debian GNU/Linux 8 および 9
  - Ubuntu Linux 14.04 LTS、16.04 LTS、および 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32 ビット
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 および 9
   - Ubuntu Linux 14.04 LTS および 16.04 LTS
 
 - デーモンのバージョン
   - Syslog-ng: 2.1 から 3.22.1
   - Rsyslog: v8
  
 - サポートされている Syslog RFC
   - Syslog RFC 3164
   - Syslog RFC 5424
 
コンピューターが次の要件も満たしていることを確認します。 
- アクセス許可
    - コンピューターに対する管理者特権のアクセス許可 (sudo) が必要です。 
- ソフトウェア要件
    - コンピューターで Python が実行されていることを確認します
## <a name="step-1-deploy-the-agent"></a>手順 1: エージェントをデプロイする

この手順では、Azure Sentinel とセキュリティ ソリューションの間のプロキシとして機能する Linux マシンを選択する必要があります。 プロキシ マシンで次のスクリプトを実行する必要があります。
- Log Analytics エージェントをインストールし、必要に応じて、TCP 経由のポート 514 で Syslog メッセージをリッスンし、Azure Sentinel ワークスペースに CEF メッセージを送信するように構成します。
- ポート 25226 を使用して、CEF メッセージを Log Analytics エージェントに転送するように Syslog デーモンを構成します。
- データを収集しそれを Log Analytics に安全に送信するように Syslog エージェントを設定します。ここで、データが解析および改良されます。
 
 
1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[Cisco ASA]** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。 

1. **[Install and configure the Syslog agent]\(Syslog エージェントのインストールと構成\)** で、マシンの種類として Azure、他のクラウド、またはオンプレミスのいずれかを選択します。 
   > [!NOTE]
   > 次の手順のスクリプトでは Log Analytics エージェントをインストールし、マシンを Azure Sentinel ワークスペースに接続するため、このマシンが他のワークスペースに接続されていないことを確認します。
1. コンピューターに対する管理者特権のアクセス許可 (sudo) が必要です。 次のコマンド `python –version` を使用して、マシンに Python が備わっていることを確認します。

1. プロキシ マシンで次のスクリプトを実行します。
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. スクリプトの実行中に、エラーまたは警告メッセージが表示されないことを確認してください。

 
## <a name="step-2-forward-cisco-asa-logs-to-the-syslog-agent"></a>手順 2: Cisco ASA のログを Syslog エージェントに転送する

Cisco ASA は CEF をサポートしていないため、ログは Syslog として送信され、Azure Sentinel エージェントはそれらを CEF ログのように解析する方法を認識しています。 Syslog のエージェントを介して Azure ワークスペースに Syslog メッセージを転送するように Cisco ASA を構成します。

[外部 Syslog サーバーに Syslog メッセージを送信する](https://aka.ms/asi-syslog-cisco-forwarding)方法に関するページに進み、指示に従って接続を設定します。 入力を求められたら、次のパラメーターを使用します。
- **port** を 514 またはエージェントで設定したポートに設定します。
- **syslog_ip** をエージェントの IP アドレスに設定します。

Log Analytics で Cisco イベントに関連するスキーマを使用するために、`CommonSecurityLog` を検索します。

## <a name="step-3-validate-connectivity"></a>手順 3: 接続の検証

1. Log Analytics を開いて、CommonSecurityLog スキーマを使用してログを受信していることを確認します。<br> ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 

1. スクリプトを実行する前に、セキュリティ ソリューションからメッセージを送信して、構成した Syslog プロキシ マシンにそれらが転送されていることを確認することをお勧めします。 
1. コンピューターに対する管理者特権のアクセス許可 (sudo) が必要です。 次のコマンド `python –version` を使用して、マシンに Python が備わっていることを確認します。
1. 次のスクリプトを実行して、エージェント、Azure Sentinel、セキュリティ ソリューション間の接続を確認します。 デーモンの転送が正しく構成されていること、正しいポートでリッスンしていること、デーモンと Log Analytics エージェント間の通信がブロックされていないことを確認します。 また、このスクリプトでは、モック メッセージ "TestCommonEventFormat" を送信して、エンドツーエンドの接続も確認されます。 <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`





## <a name="next-steps"></a>次の手順
このドキュメントでは、Cisco ASA アプライアンスを Azure Sentinel に接続する方法について学びました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。

