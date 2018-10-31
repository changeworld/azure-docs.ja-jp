---
title: Azure Update Management でのエージェント チェック結果について
description: Update Management エージェントの問題をトラブルシューティングする方法を説明します。
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/10/2018
ms.topic: conceptual
ms.service: automation
ms.component: update-management
manager: carmonm
ms.openlocfilehash: 1b258d115e7d9962ecab4b93dbcd98d13f5977c7
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956682"
---
# <a name="understand-the-agent-check-results-in-update-management"></a>Update Management でのエージェント チェック結果について

Azure 以外のマシンの Update Management では、いくつかの理由で**準備完了**と表示されないことがあります。 Update Management では、Hybrid Worker エージェントの正常性を検査して背後にある問題を判別することができます。 この記事では、トラブルシューティング ツールを Azure portal から、およびオフライン時に実行する方法について説明します。

## <a name="start-the-troubleshooter"></a>トラブルシューティングの開始

ポータルの **[Update Agent Readiness (Update エージェントの準備)]** 列の下の **[トラブルシューティング]** リンクをクリックすると、**Troubleshoot Update Agent (Update エージェントのトラブルシューティング)** ページが起動します。 このページには、エージェントの問題とともにこの記事へのリンクが表示され、問題のトラブルシューティングに役立ちます。

![VM リスト ページ](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> 検査を行うには VM が実行中である必要があります。 VM が実行されていない場合、**[VM の開始]** ボタンが表示されます。

**Troubleshoot Update Agent (Update エージェントのトラブルシューティング)** ページで **[チェックの実行]** をクリックすると、トラブルシューティング ツールが開始します。 トラブルシューティング ツールでは [[コマンドの実行]](../../virtual-machines/windows/run-command.md) を使ってマシンに対してスクリプトを実行し、エージェントの依存関係を検証します。 トラブルシューティング ツールの実行が完了すると、チェック結果が返されます。

![トラブルシューティング ページ](../media/update-agent-issues/troubleshoot-page.png)

完了すると、結果がウィンドウに返されます。 [チェック セクション](#pre-requisistes-checks)には、各チェックの対象が示されます。

![Update エージェントのチェック ページ](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>前提条件のチェック

### <a name="operating-system"></a>オペレーティング システム

OS チェックでは、Hybrid Runbook Worker が次のいずれかのオペレーティング システムを実行しているかどうかを検証します。 

|オペレーティング システム  |メモ  |
|---------|---------|
|Windows Server 2008、Windows Server 2008 R2 RTM    | 更新プログラムの評価のみをサポートします。         |
|Windows Server 2008 R2 SP1 以降     |.NET Framework 4.5 以降が必要です。 ([.NET Framework のダウンロード](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 以降が必要です。 ([WMF 4.0 のダウンロード](https://www.microsoft.com/download/details.aspx?id=40855))。<br/> より高い信頼性を確保するには Windows PowerShell 5.1 を使用することをお勧めします   ([WMF 5.1 のダウンロード](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) および 7 (x64)      | Linux エージェントは、更新リポジトリへのアクセスが必要です。 分類に基づく修正プログラムでは、CentOS に既定では設定されていない、セキュリティ データを返すための 'yum' が必須です。         |
|Red Hat Enterprise 6 (x86/x64) および 7 (x64)     | Linux エージェントは、更新リポジトリへのアクセスが必要です。        |
|SUSE Linux Enterprise Server 11 (x86/x64) および 12 (x64)     | Linux エージェントは、更新リポジトリへのアクセスが必要です。        |
|Ubuntu 14.04 LTS および 16.04 LTS (x86/x64)      |Linux エージェントは、更新リポジトリへのアクセスが必要です。         |

### <a name="net-45"></a>.NET 4.5

.NET Framework チェックでは、最小要件の [.NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653) がシステムに存在するかどうか検証します。

### <a name="wmf-51"></a>WMF 5.1

WMF チェックでは、必要なバージョンの Windows Management Framework がシステムに存在するかどうか検証します。 サポートされる最小バージョンは [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) です。 Hybrid Runbook Worker の信頼性を向上させるには [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616) をインストールすることをお勧めします。

### <a name="tls-12"></a>TLS 1.2

このチェックでは、TLS 1.2 を使って通信を暗号化しているかどうか判別します。 TLS 1.0 はプラットフォームでサポートされなくなったので、クライアントで TLS 1.2 を使用して Update Management と通信することをお勧めします。

## <a name="connectivity-checks"></a>接続チェック

### <a name="registration-endpoint"></a>登録エンドポイント

このチェックでは、エージェントがエージェント サービスと正しく通信できるかどうかを判別します。

Hybrid Runbook Worker エージェントが登録エンドポイントと通信できるように、プロキシとファイアウォールが構成されている必要があります。 アドレスと開くポートの一覧については、[Hybrid Worker 用のネットワーク計画](../automation-hybrid-runbook-worker.md#network-planning)を参照してください

### <a name="operations-endpoint"></a>操作エンドポイント

このチェックでは、エージェントが Job Runtime Data Service と正しく通信できるかどうかを判別します。

Hybrid Runbook Worker エージェントが Job Runtime Data Service と通信できるように、プロキシとファイアウォールが構成されている必要があります。 アドレスと開くポートの一覧については、「[Hybrid Worker 用のネットワーク計画](../automation-hybrid-runbook-worker.md#network-planning)」を参照してください

## <a name="vm-service-health-checks"></a>VM サービス正常性チェック

### <a name="monitoring-agent-service-status"></a>エージェント サービスの状態の監視

このチェックでは、Microsoft Monitoring Agent (`HealthService`) がマシンで実行されているかどうかを判別します。

このサービスのトラブルシューティングの詳細については、「[Microsoft Monitoring Agent が実行されていない場合](hybrid-runbook-worker.md#mma-not-running)」を参照してください。

Microsoft Monitoring Agent をインストールするには、「[Microsoft Monitoring Agent のインストールと構成](/log-analytics/log-analytics-concept-hybrid.md#install-and-configure-agent)」を参照してください

### <a name="monitoring-agent-service-events"></a>エージェント サービス イベントの監視

このチェックでは、過去 24 時間にマシン上の Operations Manager ログに `4502` イベントが記録されているかどうか判別します。

このイベントの詳細については、このイベントに関する[トラブルシューティング ガイド](hybrid-runbook-worker.md#event-4502)を参照してください。

## <a name="access-permissions-checks"></a>アクセス許可のチェック

### <a name="machinekeys-folder-access"></a>MachineKeys フォルダー アクセス

暗号化フォルダー アクセス チェックでは、ローカル システム アカウントから `C:\ProgramData\Microsoft\Crypto\RSA` にアクセスできるかどうかを検査します

## <a name="troubleshoot-offline"></a>オフライン トラブルシューティング

スクリプトをローカルに実行することで、Hybrid Runbook Worker のトラブルシューティング ツールをオフラインで使用できます。 このスクリプト ([Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration)) は PowerShell ギャラリーに含まれています。 このスクリプトの出力例を次に示します。

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.1.7601 (Windows Server 2008 R2 SP1) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .Net Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             : 
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>次の手順

Hybrid Runbook Worker のその他の問題をトラブルシューティングする方法については、「[Hybrid Runbook Worker のトラブルシューティング](hybrid-runbook-worker.md)」を参照してください
