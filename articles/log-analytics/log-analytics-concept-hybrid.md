---
title: "Azure Log Analytics を使用して環境からデータを収集する | Microsoft Docs"
description: "このトピックは、オンプレミスでまたは他のクラウド環境でホストされているコンピューターで、Log Analytics を使用してデータの収集と監視を行う方法を理解するために役立ちます。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: magoedte
ms.openlocfilehash: d12743b752c42e6a7373e9c15df6dac71b7f9d27
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="collect-data-from-computers-in-your-environment-with-log-analytics"></a>Azure Log Analytics を使用して環境内のコンピューターからデータを収集する

Azure Log Analytics は、以下に存在する Windows または Linux コンピューターからデータを収集して操作できます。

* Log Analytics VM 拡張機能を使用している [Azure 仮想マシン](log-analytics-quick-collect-azurevm.md) 
* 物理サーバーまたは仮想マシンとしてのデータセンター
* アマゾン ウェブ サービス (AWS) などのクラウドでホストされているサービス内の仮想マシン

環境内でホストされているコンピューターを Log Analytics に直接接続できます。これらのコンピューターを System Center Operations Manager 2012 R2 または 2016 で既に監視している場合は、Operations Manager 管理グループを Log Analytics に統合して、サービス操作のプロセスと戦略を引き続き維持できます。  

## <a name="overview"></a>概要

![log-analytics-agent-direct-connect-diagram](media/log-analytics-concept-hybrid/log-analytics-on-prem-comms.png)

収集したデータを分析して操作する前に、Log Analytics サービスにデータを送信するすべてのコンピューターにエージェントをインストールして接続しておく必要があります。 オンプレミス コンピューターへのエージェントのインストールには、Setup、コマンド ライン、Azure Automation の Desired State Configuration (DSC) を使用します。 

Linux と Windows のエージェントは、TCP ポート 443 を介して Log Analytics サービスとアウトバウンド通信を行います。コンピューターがファイアウォールまたはプロキシ サーバーに接続してインターネット経由で通信している場合は、「[プロキシ サーバーまたは OMS ゲートウェイで使用するためのエージェントの構成](#configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway)」で、適用する必要がある構成変更を確認してください。 System Center 2016 - Operations Manager または Operations Manager 2012 R2 でコンピューターを監視している場合は、Log Analytics サービスとマルチホームしてデータを収集し、サービスに転送することで、[Operations Manager](log-analytics-om-agents.md) で引き続き監視できます。 Log Analytics に統合された Operations Manager 管理グループで監視されている Linux コンピューターは、データ ソースの構成の受信と、収集されたデータの管理グループを介した転送は行いません。 Windows エージェントは最大 4 つのワークスペースに報告できますが、Linux エージェントは単一のワークスペースへの報告のみをサポートします。  

Linux と Windows のエージェントは、Log Analytics への接続だけではなく、Azure Automation との接続もサポートします。これにより、Hybrid Runbook Worker ロールと、Change Tracking や Update Management などの管理ソリューションがホストされます。  Hybrid Runbook Worker ロールの詳細については、[Azure Automation の Hybrid Runbook Worker](../automation/automation-offering-get-started.md#automation-architecture-overview) に関する記事を参照してください。  

IT セキュリティ ポリシーで、ネットワーク上のコンピューターによるインターネットへの接続が許可されていない場合、有効にしたソリューションに応じて、エージェントが OMS ゲートウェイに接続して構成情報を受信し、収集されたデータを送信するように構成できます。 Linux または Windows エージェントが OMS ゲートウェイ経由で Log Analytics サービスと通信するように構成する方法の詳細と手順については、「[インターネットにアクセスできないコンピューターを OMS ゲートウェイを使って OMS に接続する](log-analytics-oms-gateway.md)」を参照してください。 

> [!NOTE]
> Windows 用のエージェントは、トランスポート層セキュリティ (TLS) 1.0 と 1.1 のみをサポートします。  
> 

## <a name="prerequisites"></a>前提条件
始める前に、次の詳細を見直して、前提条件が満たされていることを確認してください。

### <a name="windows-operating-system"></a>Windows オペレーティング システム
Windows エージェントでは、次のバージョンの Windows オペレーティング システムが正式にサポートされています。

* Windows Server 2008 Service Pack 1 (SP1) 以降
* Windows 7 SP1 以降

#### <a name="network-configuration"></a>ネットワーク構成
Windows エージェントが Log Analytics と通信するために必要なプロキシとファイアウォールの構成情報を次に示します。 トラフィックはネットワークから Log Analytics サービスへの送信です。 

| エージェントのリソース | ポート | バイパス HTTPS 検査|
|----------------|-------|------------------------|
|*.ods.opinsights.azure.com |443 | あり |
|*.oms.opinsights.azure.com | 443 | あり | 
|*.blob.core.windows.net | 443 | あり | 
|*.azure-automation.net | 443 | あり | 

### <a name="linux-operating-systems"></a>Linux オペレーティング システム
次の Linux ディストリビューションは公式にサポートされています。  ただし、Linux エージェントは、ここに記載されていないディストリビューションでも動作する可能性があります。

* Amazon Linux 2012.09 ～ 2015.09 (x86/x64)
* CentOS Linux 5、6、および 7 (x86/x64)
* Oracle Linux 5、6、および 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6、および 7 (x86/x64)
* Debian GNU/Linux 6、7、および 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 および 12 (x86/x64)

#### <a name="network-configuration"></a>ネットワーク構成
Linux エージェントが Log Analytics と通信するために必要なプロキシとファイアウォールの構成情報を次に示します。 トラフィックはネットワークから Log Analytics サービスへの送信です。 

|エージェントのリソース| ポート |  
|------|---------|  
|*.ods.opinsights.azure.com | ポート 443|   
|*.oms.opinsights.azure.com | ポート 443|   
|*.blob.core.windows.net | ポート 443|   
|*.azure-automation.net | ポート 443|  

Linux エージェントは、HTTPS プロトコルを使用したプロキシ サーバーまたは OMS ゲートウェイ経由の Log Analytics サービスへの通信をサポートします。  匿名認証と基本認証 (ユーザー名/パスワード) の両方がサポートされます。  プロキシ サーバーは、インストール中、またはインストール後に proxy.conf 構成ファイルを変更して指定することができます。  

プロキシ構成の値には次の構文があります。

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> プロキシ サーバーで認証が不要な場合でも、Linux エージェントは擬似ユーザー/パスワードの指定を必要とします。 これは、どのようなユーザー名とパスワードでもかまいません。

|プロパティ| 説明 |
|--------|-------------|
|プロトコル | https |
|user | プロキシ認証のオプションのユーザー名 |
|password | プロキシ認証のオプションのパスワード |
|proxyhost | プロキシ サーバー/OMS ゲートウェイのアドレスまたは FQDN |
|port | プロキシ サーバー/OMS ゲートウェイのオプションのポート番号 |

次に例を示します。`https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> パスワードで "@" などの特殊文字を使用した場合、値が正しく解析されないためにプロキシ接続エラーが発生します。  この問題を回避するには、[URLDecode](https://www.urldecoder.org/) などのツールを使用して、URL 内にパスワードをエンコードします。  

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