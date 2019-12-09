---
title: Azure Sentinel Preview に CEF データを接続する | Microsoft Docs
description: Azure Sentinel に CEF データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: rkarlin
ms.openlocfilehash: 0fbdba5c3fbfdfab5267407ccec9c611d74a5e02
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463977"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>共通イベント形式を使用して外部ソリューションを接続する



この記事では、外部セキュリティ ソリューションで Azure Sentinel を接続し、Syslog の上部に Common Event Format (CEF) メッセージを送信する方法について説明します。 

> [!NOTE] 
> データは、Azure Sentinel を実行しているワークスペースの地域に保存されます。

## <a name="how-it-works"></a>動作のしくみ

エージェントを専用の Linux マシン (VM またはオンプレミス) に展開して、アプライアンスと Azure Sentinel の間の通信をサポートする必要があります。 次の図は、Azure での Linux VM の場合の設定を示しています。

 ![Azure での CEF](./media/connect-cef/cef-syslog-azure.png)

あるいは、別のクラウドの VM かオンプレミスのマシンを使用する場合は、以下の設定が存在することになります。 

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
 
マシンが次の要件も満たしていることを確認します。 
- アクセス許可
    - マシンに対する管理者特権のアクセス許可 (sudo) が必要です。 
- ソフトウェア要件
    - マシンで Python が実行されていることを確認します
## <a name="step-1-deploy-the-agent"></a>手順 1: エージェントをデプロイする

このステップでは、Azure Sentinel とセキュリティ ソリューションの間のプロキシとして機能する Linux マシンを選択する必要があります。 プロキシ マシンで次のスクリプトを実行する必要があります。
- Log Analytics エージェントをインストールし、Syslog メッセージをリッスンするために必要に応じて構成します。
- TCP ポート 514 を使用して Syslog メッセージをリッスンするように Syslog デーモンを構成し、TCP ポート 25226 を使用して CEF メッセージだけを Log Analytics エージェントに転送します。
- Syslog エージェントを設定して、データを収集して安全に Azure Sentinel に送信し、そこでデータが解析および補強されるようにします。
 
 
1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[Common Event Format (CEF)]** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。 

1. **[Install and configure the Syslog agent]\(Syslog エージェントのインストールと構成\)** で、マシンの種類として Azure、他のクラウド、またはオンプレミスのいずれかを選択します。 
   > [!NOTE]
   > 次の手順のスクリプトでは Log Analytics エージェントをインストールし、コンピューターを Azure Sentinel ワークスペースに接続するため、このコンピューターが他のワークスペースに接続されていないことを確認してください。
1. マシンに対する管理者特権のアクセス許可 (sudo) が必要です。 コマンド `python –version` を使用して、コンピューターに Python がインストールされていることを確認します。

1. プロキシ マシンで次のスクリプトを実行します。
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. スクリプトの実行中に、エラーまたは警告メッセージが表示されないことを確認してください。


## <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>手順 2: CEF メッセージを送信するように、セキュリティ ソリューションを構成する

1. アプライアンスでこれらの値を設定して、このアプライアンスが Log Analytics エージェントに基づいて、必要な形式で必要なログを Azure Sentinel Syslog エージェントに送信するようにします。 Azure Sentinel エージェントの Syslog デーモンでこれらのパラメーターを変更できれば、ご利用のアプライアンスでもこれらのパラメーターを変更することができます。
    - プロトコル = TCP
    - ポート = 514
    - 形式 = CEF
    - IP アドレス - CEF メッセージを、この目的専用の仮想マシンの IP アドレスに送信していることを確認します。

   > [!NOTE]
   > このソリューションでは、Syslog RFC 3164 または RFC 5424 をサポートしています。


1. Log Analytics で CEF イベントに関連するスキーマを使用するために、`CommonSecurityLog` を検索します。

## <a name="step-3-validate-connectivity"></a>手順 3:接続の検証

1. Log Analytics を開いて、CommonSecurityLog スキーマを使用してログを受信していることを確認します。<br> ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 

1. スクリプトを実行する前に、セキュリティ ソリューションからメッセージを送信して、構成した Syslog プロキシ マシンに転送されていることを確認することをお勧めします。 
1. マシンに対する管理者特権のアクセス許可 (sudo) が必要です。 コマンド `python –version` を使用して、コンピューターに Python がインストールされていることを確認します。
1. 次のスクリプトを実行して、エージェント、Azure Sentinel、セキュリティ ソリューションの間の接続を確認します。 これにより、デーモンの転送が正しく構成されていること、正しいポートでリッスンしていること、デーモンと Log Analytics エージェントの間の通信がブロックされていないことが確認されます。 また、このスクリプトは、モック メッセージ「TestCommonEventFormat」を送信して、エンドツーエンドの接続を確認します。 <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>次の手順
このドキュメントでは、CEF アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。

