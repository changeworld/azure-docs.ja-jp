---
title: Azure Sentinel に Palo Alto Networks データを接続する | Microsoft Docs
description: Azure Sentinel に Palo Alto Networks データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 8b1331eb99fd3d061d231ae48c40a721911e74db
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475854"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Azure Sentinel に Palo Alto Networks を接続する



この記事では、Palo Alto Networks アプライアンスを Azure Sentinel に接続する方法について説明します。 Palo Alto Networks データ コネクタを使用すると、Azure Sentinel に Palo Alto Networks のログを簡単に接続でき、ダッシュ ボードの表示、カスタム アラートの作成、および調査の改善も行うことができます。 Azure Sentinel で Palo Alto Networks を使用すると、組織のインターネットの使用に関する詳細な分析情報を取得できるため、セキュリティ操作機能が強化されます。 


## <a name="how-it-works"></a>動作のしくみ

エージェントを専用の Linux マシン (VM またはオンプレミス) に展開して、Palo Alto Networks と Azure Sentinel の間の通信をサポートする必要があります。 次の図は、Azure での Linux VM の場合の設定を示しています。

 ![Azure での CEF](./media/connect-cef/cef-syslog-azure.png)

または、別のクラウド内の VM、またはオンプレミスのマシンを使用している場合にも、この設定が存在します。 

 ![オンプレミスの CEF](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>セキュリティに関する考慮事項

組織のセキュリティ ポリシーに従って、コンピューターのセキュリティを構成してください。 たとえば、企業のネットワーク セキュリティ ポリシーに合わせてネットワークを構成し、デーモンのポートとプロトコルを要件に合わせて変更することができます。   [Azure で VM をセキュリティ保護する](../virtual-machines/linux/security-policy.md)方法に関するページと[ネットワーク セキュリティのベスト プラクティス](../security/fundamentals/network-best-practices.md)に関するページの手順を行うと、コンピューターのセキュリティ構成を改善できます。

セキュリティ ソリューションと Syslog コンピューターの間で TLS 通信を使用するには、TLS で通信するように Syslog デーモン (rsyslog または syslog-ng) を構成する必要があります。[TLS -rsyslog で Syslog トラフィックを暗号化する](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)方法に関するページと [TLS –syslog-ng でログ メッセージを暗号化する](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)方法に関するページをご覧ください。

 
## <a name="prerequisites"></a>前提条件
プロキシとして使用する Linux マシンで、次のいずれかのオペレーティングシステムが実行されていることを確認します。

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
   - Syslog-ng:2.1 - 3.22.1
   - Rsyslog: v8
  
 - サポートされている Syslog RFC
   - Syslog RFC 3164
   - Syslog RFC 5424
 
コンピューターが次の要件も満たしていることを確認します。 
- アクセス許可
    - コンピューターに対する高度な権限 (sudo) が必要です。 
- ソフトウェア要件
    - コンピューターで Python が実行されていることを確認します
## <a name="step-1-deploy-the-agent"></a>手順 1: エージェントをデプロイする

この手順では、Azure Sentinel とセキュリティ ソリューションの間のプロキシとして機能する Linux マシンを選択する必要があります。 プロキシ マシンで次のスクリプトを実行する必要があります。
- Log Analytics エージェントをインストールし、必要に応じて、TCP 経由のポート 514 で Syslog メッセージをリッスンし、Azure Sentinel ワークスペースに CEF メッセージを送信するように構成します。
- ポート 25226 を使用して、CEF メッセージを Log Analytics エージェントに転送するように Syslog デーモンを構成します。
- データを収集しそれを Log Analytics に安全に送信するように Syslog エージェントを設定します。ここで、データが解析および改良されます。
 
 
1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[Palo Alto Networks]\(Palo Alto ネットワーク\)** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。 

1. **[Install and configure the Syslog agent]\(Syslog エージェントのインストールと構成\)** で、マシンの種類として Azure、他のクラウド、またはオンプレミスを選択します。 
   > [!NOTE]
   > 次の手順のスクリプトでは Log Analytics エージェントをインストールし、マシンを Azure Sentinel ワークスペースに接続するため、このマシンが他のワークスペースに接続されていないことを確認します。
1. コンピューターに対する高度な権限 (sudo) が必要です。 コマンド `python –version` を使用して、マシンに Python がインストールされていることを確認します。

1. プロキシ マシンで次のスクリプトを実行します。
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. スクリプトの実行中に、エラーまたは警告メッセージが表示されないことを確認してください。


 
## <a name="step-2-forward-palo-alto-networks-logs-to-the-syslog-agent"></a>手順 2: Palo Alto Networks のログを Syslog エージェントに転送する

Syslog のエージェントを介してお使いの Azure ワークスペースに CEF 形式で Syslog メッセージを転送するように Palo Alto Networks を構成します。
1.  「[Common Event Format (CEF) Configuration Guides (Common Event Format (CEF) 構成ガイド)](https://docs.paloaltonetworks.com/resources/cef)」にアクセスし、お使いのアプライアンスの種類に応じた PDF をダウンロードします。 ガイドのすべての手順に従って、CEF イベントを収集するように Palo Alto Networks アプライアンスを設定します。 

1.  「[Configure Syslog monitoring](https://aka.ms/asi-syslog-paloalto-forwarding)」(Syslog の監視を構成する) に移動し、ステップ 2 と 3 に従って、Palo Alto Networks アプライアンスから Azure Sentinel への CEF イベントの転送を構成します。

    1. **[Syslog server format]\(Syslog サーバーの形式\)** を **[BSD]** に設定します。

       > [!NOTE]
       > PDF からのコピー/貼り付け操作では、テキストが変更され、ランダムな文字が挿入される可能性があります。 これを回避するには、この例で示すように、テキストをエディターにコピーし、ログの形式を崩す可能性がある文字を削除した後、貼り付けます。
 
        ![CEF テキストのコピーの問題](./media/connect-cef/paloalto-text-prob1.png)

2. Log Analytics で Palo Alto Networks イベントに関連するスキーマを使用するために、**CommonSecurityLog** を検索します。

## <a name="step-3-validate-connectivity"></a>手順 3:接続の検証

1. Log Analytics を開いて、CommonSecurityLog スキーマを使用してログを受信していることを確認します。<br> ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 

1. スクリプトを実行する前に、セキュリティ ソリューションからメッセージを送信して、構成した Syslog プロキシ マシンにそれらが転送されていることを確認することをお勧めします。 
1. コンピューターに対する高度な権限 (sudo) が必要です。 コマンド `python –version` を使用して、マシンに Python がインストールされていることを確認します。
1. 次のスクリプトを実行して、エージェント、Azure Sentinel、およびセキュリティ ソリューション間の接続を確認します。 デーモンの転送が正しく構成されていること、正しいポートでリッスンしていること、およびデーモンと Log Analytics エージェント間の通信がブロックされていないことを確認します。 また、このスクリプトでは、モックメッセージ "TestCommonEventFormat" を送信して、エンドツーエンドの接続も確認されます。 <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`







## <a name="next-steps"></a>次の手順
このドキュメントでは、Palo Alto Networks アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。

