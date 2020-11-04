---
title: Azure Sentinel Preview に CEF データを接続する | Microsoft Docs
description: Linux マシンをプロキシとして使用して、Azure Sentinel に共通イベント形式 (CEF) メッセージを送信する外部ソリューションを接続します。
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
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: dae8ce6cbad1ae08898ae439c1f621bef185b5df
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747894"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>共通イベント形式を使用して外部ソリューションを接続する

CEF メッセージを送信する外部ソリューションを接続するとき、Azure Sentinel と接続するための 3 つの手順があります。

手順 1: [Syslog/CEF フォワーダーをデプロイして CEF に接続する](connect-cef-agent.md) 手順 2:[ソリューション固有の手順を実行する](connect-cef-solution-config.md) 手順 3:[接続を検証する](connect-cef-verify.md)

この記事では、接続のしくみを説明し、前提条件を示します。また、Syslog に加えて共通イベント形式 (CEF) メッセージを送信するエージェントをセキュリティ ソリューションにデプロイする手順を示します。 

> [!NOTE] 
> データは、Azure Sentinel を実行しているワークスペースの地域に保存されます。

この接続を確立するには、アプライアンスと Azure Sentinel 間の通信をサポートするために、Syslog フォワーダー サーバーをデプロイする必要があります。  サーバーは、Linux 用の Log Analytics エージェントがインストールされた専用の Linux マシン (VM またはオンプレミス) で構成されます。 

次の図は、Azure の Linux VM を使用した場合の設定を示しています。

 ![Azure での CEF](./media/connect-cef/cef-syslog-azure.png)

あるいは、別のクラウドの VM かオンプレミスのマシンを使用する場合には、以下の設定となります。 

 ![オンプレミスの CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>セキュリティに関する考慮事項

組織のセキュリティ ポリシーに従って、コンピューターのセキュリティを構成してください。 たとえば、企業のネットワーク セキュリティ ポリシーに合わせてネットワークを構成し、デーモンのポートとプロトコルを要件に合わせて変更することができます。 [Azure で VM をセキュリティ保護する](../virtual-machines/security-policy.md)方法に関するページと[ネットワーク セキュリティのベスト プラクティス](../security/fundamentals/network-best-practices.md)に関するページの手順を行うと、マシンのセキュリティ構成を改善できます。

Syslog ソースと Syslog フォワーダーの間で TLS 通信を使用するには、TLS で通信するために Syslog デーモン (rsyslog または syslog-ng) を構成する必要があります。[TLS -rsyslog で Syslog トラフィックを暗号化する](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)方法に関するページと [TLS –syslog-ng でログ メッセージを暗号化する](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)方法に関するページをご覧ください。
 
## <a name="prerequisites"></a>前提条件

プロキシとして使用する Linux マシンで、次のいずれかのオペレーティングシステムが実行されていることを確認します。

- 64 ビット
  - CentOS 7 とサブバージョン、およびそれ以降 (6 は不可)
  - Amazon Linux 2017.09
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 とサブバージョン、およびそれ以降 (6 は不可)
  - Debian GNU/Linux 8 および 9
  - Ubuntu Linux 14.04 LTS、16.04 LTS、および 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32 ビット
   - CentOS 7
   - Oracle Linux 7
   - Red Hat Enterprise Linux Server 7
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
    - マシンで Python 2.7 が実行されていることを確認します。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、CEF アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。

