---
title: Azure Sentinel に Fortinet データを接続する | Microsoft Docs
description: Azure Sentinel に Fortinet データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 7a44d63b834a7b6b580909005a440637bf730918
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475790"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Azure Sentinel に Fortinet を接続する



この記事では、Fortinet アプライアンスを Azure Sentinel に接続する方法について説明します。 Fortinet データ コネクタを使用すると、Azure Sentinel に Fortinet のログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 Azure Sentinel で Fortinet を使用すると、組織のインターネット使用状況に関するより多くの分析情報が得られ、セキュリティ操作機能が強化されます。 


## <a name="how-it-works"></a>動作のしくみ

エージェントを専用の Linux マシン (VM またはオンプレミス) にデプロイして、Fortinet と Azure Sentinel の間の通信をサポートする必要があります。 次の図は、Azure での Linux VM の場合の設定を示しています。

 ![Azure での CEF](./media/connect-cef/cef-syslog-azure.png)

あるいは、別のクラウドの VM かオンプレミスのマシンを使用する場合は、以下の設定が存在することになります。 

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
   - Syslog-ng:2.1 から 3.22.1
   - Rsyslog: v8
  
 - サポートされている Syslog RFC
   - Syslog RFC 3164
   - Syslog RFC 5424
 
マシンが次の要件も満たしていることを確認します。 
- アクセス許可
    - マシンに対する管理者特権のアクセス許可 (sudo) が必要です。 
- ソフトウェア要件
    - マシンで Python が実行されていることを確認します
## <a name="step-1-deploy-the-agent"></a>手順 1: エージェントをデプロイする

この手順では、Azure Sentinel とセキュリティ ソリューションの間のプロキシとして機能する Linux マシンを選択する必要があります。 プロキシ マシンで次のスクリプトを実行する必要があります。
- Log Analytics エージェントをインストールし、必要に応じて、ポート 514 で TCP 経由で Syslog メッセージをリッスンし、Azure Sentinel ワークスペースに CEF メッセージを送信するように構成します。
- ポート 25226 を使用して、CEF メッセージを Log Analytics エージェントに転送するように Syslog デーモンを構成します。
- Syslog エージェントを設定して、データを収集して安全に Log Analytics に送信し、そこでデータが解析および補強されるようにします。
 
 
1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[Fortinet]** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。 

1. **[Syslog エージェントのインストールと構成]** で、マシンの種類として Azure、他のクラウド、またはオンプレミスを選択します。 
   > [!NOTE]
   > 次の手順のスクリプトでは Log Analytics エージェントをインストールし、マシンを Azure Sentinel ワークスペースに接続するため、このマシンが他のワークスペースに接続されていないことを確認します。
1. マシンに対する管理者特権のアクセス許可 (sudo) が必要です。 コマンド `python –version` を使用して、マシンに Python がインストールされていることを確認します。

1. プロキシ マシンで次のスクリプトを実行します。
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. スクリプトの実行中に、エラーまたは警告メッセージが表示されないことを確認してください。

 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>手順 2:Fortinet のログを Syslog エージェントに転送する

Syslog エージェントを介して、CEF 形式で Syslog メッセージを Azure ワークスペースに転送するように Fortinet を構成します。

1. お使いの Fortinet アプライアンスで CLI を開き、次のコマンドを実行します。

        config log syslogd setting
        set format cef
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - サーバーの **IP アドレス**を、エージェントの IP アドレスに置き換えます。
    - **syslog のポート**を **514** に設定するか、エージェントに設定されているポートに設定します。
    - 初期の FortiOS のバージョンで CEF 形式を有効にするため、コマンド セット **csv disable** を実行する必要が生じる場合があります。
 
   > [!NOTE] 
   > 詳細については、[Fortinet のドキュメント ライブラリ](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)を参照してください。 バージョンを選択してから、**Handbook** (ハンドブック) と **Log Message Reference** (ログ メッセージ リファレンス) を利用します。

 Azure Monitor Log Analytics で Fortinet イベントに関連するスキーマを使用する場合は、`CommonSecurityLog` を検索します。


## <a name="step-3-validate-connectivity"></a>手順 3:接続の検証

1. Log Analytics を開いて、CommonSecurityLog スキーマを使用してログを受信していることを確認します。<br> ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 

1. スクリプトを実行する前に、セキュリティ ソリューションからメッセージを送信して、構成した Syslog プロキシ マシンにそれらが転送されていることを確認することをお勧めします。 
1. マシンに対する管理者特権のアクセス許可 (sudo) が必要です。 コマンド `python –version` を使用して、マシンに Python がインストールされていることを確認します。
1. 次のスクリプトを実行して、エージェント、Azure Sentinel、セキュリティ ソリューションの間の接続を確認します。 これにより、デーモンの転送が正しく構成されていること、正しいポートでリッスンしていること、デーモンと Log Analytics エージェントの間の通信がブロックされていないことが確認されます。 また、このスクリプトでは、モック メッセージ "TestCommonEventFormat" を送信して、エンドツーエンドの接続も確認されます。 <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`




## <a name="next-steps"></a>次の手順
この記事では、Fortinet アプライアンスを Azure Sentinel に接続する方法について学びました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。

