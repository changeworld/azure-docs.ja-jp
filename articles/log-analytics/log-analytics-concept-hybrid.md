---
title: Azure Log Analytics エージェントを使用してハイブリッド環境でデータを収集する | Microsoft Docs
description: このトピックは、オンプレミスでまたは他のクラウド環境でホストされているコンピューターで、Log Analytics を使用してデータの収集と監視を行う方法を理解するために役立ちます。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 96feb52bd5702c899faa8d845969ae8ba0995504
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495358"
---
# <a name="collect-data-in-a-hybrid-environment-with-log-analytics-agent"></a>Log Analytics エージェントを使用してハイブリッド環境でデータを収集する

Azure Log Analytics は、次の場所で実行されている Windows または Linux オペレーティング システムからデータを収集して操作できます。

* Log Analytics VM 拡張機能を使用している [Azure 仮想マシン](log-analytics-quick-collect-azurevm.md) 
* 物理サーバーまたは仮想マシンとしてのデータセンター
* アマゾン ウェブ サービス (AWS) などのクラウドでホストされているサービス内の仮想マシン

環境内でホストされているコンピューターを Log Analytics に直接接続できます。これらのコンピューターを System Center Operations Manager 2012 R2 以降で既に監視している場合は、Operations Manager 管理グループを Log Analytics に統合して、IT サービスの運用プロセスを引き続き維持できます。  

## <a name="overview"></a>概要

![log-analytics-agent-direct-connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

収集したデータを分析して操作する前に、Log Analytics サービスにデータを送信するすべてのコンピューターにエージェントをインストールして接続しておく必要があります。 オンプレミス コンピューターへのエージェントのインストールには、Setup、コマンド ライン、Azure Automation の Desired State Configuration (DSC) を使用します。 

Linux と Windows のエージェントは、TCP ポート 443 を介して Log Analytics サービスとアウトバウンド通信を行います。コンピューターがファイアウォールまたはプロキシ サーバーに接続してインターネット経由で通信している場合は、後述する前提条件を確認して、必要なネットワーク構成を把握してください。  組織の IT セキュリティ ポリシーによってネットワーク上のコンピューターにインターネットへの接続が許可されない場合は、[OMS ゲートウェイ](log-analytics-oms-gateway.md)をセットアップし、ゲートウェイ経由で Log Analytics に接続するようエージェントを構成できます。 その後、エージェントは、構成情報を受信したり、有効にしたデータ収集ルールとソリューションに応じて収集されたデータを送信したりできます。 

System Center Operations Manager 2012 R2 以降でコンピューターを監視している場合は、Log Analytics サービスとマルチホームしてデータを収集し、サービスに転送することで、[Operations Manager](log-analytics-om-agents.md) で引き続き監視できます。 Log Analytics に統合された Operations Manager 管理グループで監視されている Linux コンピューターは、データ ソースの構成の受信と、収集されたデータの管理グループを介した転送は行いません。 Windows エージェントは最大 4 つのワークスペースに報告できますが、Linux エージェントは単一のワークスペースへの報告のみをサポートします。  

Linux と Windows のエージェントは、Log Analytics への接続だけではなく、Azure Automation もサポートします。これにより、Hybrid Runbook Worker ロールと、Change Tracking や Update Management などの管理ソリューションがホストされます。  Hybrid Runbook Worker ロールの詳細については、[Azure Automation の Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md) に関する記事を参照してください。  

## <a name="supported-windows-operating-systems"></a>サポートされている Windows オペレーティング システム
Windows エージェントでは、次のバージョンの Windows オペレーティング システムが正式にサポートされています。

* Windows Server 2008 Service Pack 1 (SP1) 以降
* Windows 7 SP1 以降

## <a name="supported-linux-operating-systems"></a>サポートされている Linux オペレーティング システム
次の Linux ディストリビューションは公式にサポートされています。  ただし、Linux エージェントは、ここに記載されていないディストリビューションでも動作する可能性があります。  記載されている各メジャー バージョンのマイナー リリースは、特に記載がない限りすべてサポートされます。  

* Amazon Linux 2012.09 ～ 2015.09 (x86/x64)
* CentOS Linux 5、6、および 7 (x86/x64)  
* Oracle Linux 5、6、および 7 (x86/x64) 
* Red Hat Enterprise Linux Server 5、6、および 7 (x86/x64)
* Debian GNU/Linux 6、7、および 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 および 12 (x86/x64)

## <a name="tls-12-protocol"></a>TLS 1.2 プロトコル
Log Analytics へのデータの転送時のセキュリティを保証するため、少なくとも Transport Layer Security (TLS) 1.2 を使用するようにエージェントを構成することを強くお勧めします。 以前のバージョンの TLS/SSL (Secure Sockets Layer) は脆弱であることが確認されています。現在、これらは下位互換性を維持するために使用可能ですが、**推奨されていません**。  詳細については、「[TLS 1.2 を使用して安全にデータを送信する](log-analytics-data-security.md#sending-data-securely-using-tls-12)」を参照してください。 

## <a name="network-firewall-requirements"></a>ネットワーク ファイアウォールの要件
Linux および Windows エージェントが Log Analytics と通信するために必要なプロキシとファイアウォールの構成情報を次に示します。  

|エージェントのリソース|ポート |方向 |バイパス HTTPS 検査|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |ポート 443 |受信および送信|はい |  
|*.oms.opinsights.azure.com |ポート 443 |受信および送信|はい |  
|*.blob.core.windows.net |ポート 443 |受信および送信|はい |  
|*.azure-automation.net |ポート 443 |受信および送信|はい |  


Azure Automation Hybrid Runbook Worker を使用して Automation サービスに接続および登録し、お使いの環境で Runbook を使用することを計画している場合、[Hybrid Runbook Worker 用のネットワークの構成](../automation/automation-hybrid-runbook-worker.md#network-planning)に関する記事に説明されているポート番号と URL にアクセスできる必要があります。 

Windows および Linux エージェントは、HTTPS プロトコルを使用したプロキシ サーバーまたは OMS ゲートウェイ経由の Log Analytics サービスへの通信をサポートします。  匿名認証と基本認証 (ユーザー名/パスワード) の両方がサポートされます。  サービスに直接接続されている Windows エージェントの場合、プロキシの構成は、インストール時や、[デプロイ後](log-analytics-agent-manage.md#update-proxy-settings)にコントロール パネルまたは PowerShell を使って、指定されます。  

Linux エージェントの場合、プロキシ サーバーは、インストール時や、[インストール後](log-analytics-agent-manage.md#update-proxy-settings)に proxy.conf 構成ファイルを修正することによって、指定されます。  Linux エージェント プロキシ構成の値には次の構文があります。

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> プロキシ サーバーで認証が不要な場合でも、Linux エージェントは擬似ユーザー/パスワードの指定を必要とします。 これは、どのようなユーザー名とパスワードでもかまいません。

|プロパティ| 説明 |
|--------|-------------|
|Protocol | https |
|user | プロキシ認証のオプションのユーザー名 |
|password | プロキシ認証のオプションのパスワード |
|proxyhost | プロキシ サーバー/OMS ゲートウェイのアドレスまたは FQDN |
|port | プロキシ サーバー/OMS ゲートウェイのオプションのポート番号 |

次に例を示します。`https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> パスワードに "\@" などの特殊文字を使用した場合、値が正しく解析されないためにプロキシ接続エラーが発生します。  この問題を回避するには、[URLDecode](https://www.urldecoder.org/) などのツールを使用して、URL 内にパスワードをエンコードします。  

## <a name="install-and-configure-agent"></a>エージェントをインストールして構成する 
オンプレミス コンピューターの Log Analytics への直接接続は、要件に応じて、さまざまな方法で実現できます。 次の表は、どの方法が組織で最も効果的であるかを判断できるように、各方法について説明しています。

|ソース | 方法 | 説明|
|-------|-------------|-------------|
| Windows コンピューター|- [手動インストール](log-analytics-agent-windows.md)<br>- [Azure Automation DSC](log-analytics-agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Azure Stack を使用する Azure Resource Manager テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |コマンド ラインから、または Azure Automation DSC や [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications) などの自動化された方法を使用して、Microsoft Monitoring エージェントをインストールします。データセンターに Microsoft Azure Stack が配置されている場合は、Azure Resource Manager テンプレートも使用できます。| 
|Linux コンピューター| [手動インストール](log-analytics-quick-collect-linux-computer.md)|GitHub でホストされているラッパー スクリプトを呼び出して Linux エージェントをインストールします。 | 
| System Center Operations Manager|[Operations Manager を Log Analytics に統合する](log-analytics-om-agents.md) | Operations Manager と Log Analytics 間の統合を構成して、Linux と Windows のコンピューターから収集したデータを報告するために管理グループに転送します。|  

## <a name="next-steps"></a>次の手順

* [データ ソース](log-analytics-data-sources.md)を見直して、Windows または Linux コンピューターからデータを収集するために使用できるデータ ソースを理解します。 

* [ログ検索](log-analytics-log-searches.md) について学習し、データ ソースとソリューションから収集されたデータを分析します。 

* Log Analytics に機能を追加し、OMS リポジトリにデータを収集する [ソリューション](log-analytics-add-solutions.md) について学習します。
