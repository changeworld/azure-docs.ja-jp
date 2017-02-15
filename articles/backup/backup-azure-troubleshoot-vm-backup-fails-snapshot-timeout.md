---
title: "Azure VM のバックアップの失敗: スナップショットの状態について VM エージェントと通信できませんでした - スナップショット VM サブタスクがタイムアウトしました | Microsoft Docs"
description: "スナップショットの状態について VM エージェントと通信できませんでしたというエラーに関連する Azure VM のバックアップの失敗の症状の原因と解決策。 スナップショット VM サブタスクのタイムアウト エラー"
services: backup
documentationcenter: 
author: genlin
manager: cfreeman
editor: 
ms.assetid: 4b02ffa4-c48e-45f6-8363-73d536be4639
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jimpark; markgal;genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 87387e4f182214fa0c34a6a1358c6cc2648be906


---
# <a name="azure-vm-backup-fails-could-not-communicate-with-the-vm-agent-for-snapshot-status---snapshot-vm-sub-task-timed-out"></a>Azure VM のバックアップの失敗: スナップショットの状態について VM エージェントと通信できませんでした - スナップショット VM サブタスクがタイムアウトしました
## <a name="summary"></a>概要
Azure Backup に仮想マシン (VM) を登録して、スケジュール設定すると、Azure Backup サービスは、スケジュールされた時刻に VM のバックアップ拡張機能と通信して、バックアップ ジョブを開始し、特定の時点のスナップショットを作成します。 スナップショットをトリガーできず、バックアップの失敗につながる条件があります。 この記事では、スナップショットのタイムアウト エラーに関連した Azure VM のバックアップの失敗に関連する問題のトラブルシューティング手順について説明します。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>症状
サービスとしてのインフラストラクチャ (IaaS) VM に対する Microsoft Azure Backup が失敗し、[Azure Portal](https://portal.azure.com/) のジョブ エラーの詳細で次のエラー メッセージが返されます。

**スナップショットの状態について VM エージェントと通信できませんでした - スナップショット VM サブタスクがタイムアウトしました。**

## <a name="cause"></a>原因
このエラーの一般的な原因には、次の 4 つがあります。

* VM がインターネットにアクセスできない。
* VM にインストールされている Microsoft Azure VM エージェントが古くなっている (Linux VM の場合)。
* バックアップ拡張機能の更新または読み込みに失敗した。
* スナップショットの状態を取得できなかったか、スナップショットを作成できなかった。

## <a name="cause-1-the-vm-does-not-have-internet-access"></a>原因 1: VM がインターネットにアクセスできない
デプロイ要件によっては、VM がインターネットにアクセスできない場合や、設定されている制限により、Azure インフラストラクチャにアクセスできない場合があります。

バックアップ拡張機能が正常に機能するためには、Azure のパブリック IP アドレスへの接続が必要です。 これは、バックアップ拡張機能が Azure Storage エンドポイント (HTTP URL) に VM のスナップショットを管理するコマンドを送信するためです。 拡張機能がパブリック インターネットにアクセスできない場合は、最終的にバックアップが失敗します。

### <a name="solution"></a>解決策
このシナリオでは、次のいずれかの方法を使用して問題を解決します。

* Azure データセンターの IP 範囲をホワイトリストに登録する
* フローに対する HTTP トラフィック用のパスを作成する

### <a name="to-whitelist-the-azure-datacenter-ip-ranges"></a>Azure データセンターの IP 範囲をホワイトリストに登録するには
1. ホワイトリストに登録する [Azure データセンターの IP の一覧](https://www.microsoft.com/download/details.aspx?id=41653) を取得します。
2. New-NetRoute コマンドレットを使用して、IP アドレスのブロックを解除します。 管理者特権での PowerShell ウィンドウ (管理者として実行) で、Azure VM 内でこのコマンドレットを実行します。
3. 規則をネットワーク セキュリティ グループ (NSG) に追加して IP へのアクセスを許可します (NSG を使用している場合)。

### <a name="to-create-a-path-for-http-traffic-to-flow"></a>HTTP トラフィックがフローするためのパスを作成するには
1. ネットワーク制限 (NSG など) を設定している場合は、トラフィックをルーティングするための HTTP プロキシ サーバーをデプロイします。
2. ネットワーク セキュリティ グループ (NSG) を使用している場合は、規則を追加して HTTP プロキシからインターネットへのアクセスを許可します。

[VM をバックアップできるように HTTP プロキシを設定する](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups)方法を確認してください。

## <a name="cause-2-the-microsoft-azure-vm-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>原因 2: VM にインストールされている Microsoft Azure VM エージェントが古くなっている (Linux VM の場合)
### <a name="solution"></a>解決策
Linux VM の場合、エージェントに関連するエラーまたは拡張機能に関連するエラーのほとんどは、古い VM エージェントに影響する問題が原因で発生します。 一般的なガイドラインとして、この問題のトラブルシューティングを行うには、まず次の手順を実行します。

1. [最新の Azure VM エージェントをインストールします](https://github.com/Azure/WALinuxAgent)。
2. Azure エージェントが VM で実行されていることを確認します。 これを行うには、次のコマンドを実行します: ```ps -e```
   
    このプロセスが実行されていない場合は、次のコマンドを使用してプロセスを再起動します。
   
    Ubuntu の場合: ```service walinuxagent start```
   
    他のディストリビューション: ```service waagent start
   ```
3. [Configure the auto restart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Run a new test backup. If the failures persist, please collect logs from the following folders for further debugging.
   
    We require the following logs from the customer’s VM:
   
   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

If we require verbose logging for waagent, follow these steps to enable this:

1. In the /etc/waagent.conf file, locate the following line:
   
    Enable verbose logging (y|n)
2. Change the **Logs.Verbose** value from n to y.
3. Save the change, and then restart waagent by following the previous steps in this section.

## Cause 3: The backup extension fails to update or load
If extensions cannot be loaded, then Backup fails because a snapshot cannot be taken.

### Solution
For Windows guests:

1. Verify that the iaasvmprovider service is enabled and has a startup type of automatic.
2. If this is not the configuration, enable the service to determine whether the next backup succeeds.

For Linux guests:

The latest version of VMSnapshot Linux (extension used by backup) is 1.0.91.0

If the backup extension still fails to update or load, you can force the VMSnapshot extension to be reloaded by uninstalling the extension. The next backup attempt will reload the extension.

### To uninstall the extension
1. Go to the [Azure portal](https://portal.azure.com/).
2. Locate the particular VM that has backup problems.
3. Click **Settings**.
4. Click **Extensions**.
5. Click **Vmsnapshot Extension**.
6. Click **uninstall**.

This will cause the extension to be reinstalled during the next backup.

## Cause 4: The snapshots status cannot be retrieved or the snapshots cannot be taken
VM backup relies on issuing snapshot command to underlying storage. The backup can fail because it has no access to storage or because of a delay in snapshot task execution.

### Solution
The following conditions can cause snapshot task failure:

| Cause | Solution |
| --- | --- |
| VMs that have Microsoft SQL Server Backup configured. By default, VM Backup runs a VSS Full backup on Windows VMs. On VMs that are running SQL Server-based servers and on which SQL Server Backup is configured, snapshot execution delays may occur. |Set following registry key if you are experiencing backup failures because of snapshot issues.<br><br>[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| VM status is reported incorrectly because the VM is shut down in RDP. If you shut down the virtual machine in RDP, check the portal to determine whether that VM status is reflected correctly. |If it’s not, shut down the VM in the portal by using the ”Shutdown” option in the VM dashboard. |
| Many VMs from the same cloud service are configured to back up at the same time. |It’s a best practice to spread out the VMs from the same cloud service to have different backup schedules. |
| The VM is running at high CPU or memory usage. |If the VM is running at high CPU usage (more than 90 percent) or high memory usage, the snapshot task is queued and delayed and eventually times out. In this situation, try on-demand backup. |
| The VM cannot get host/fabric address from DHCP. |DHCP must be enabled inside the guest for IaaS VM Backup to work.  If the VM cannot get host/fabric address from DHCP response 245, then it cannot download ir run any extensions. If you need a static private IP, you should configure it through the platform. The DHCP option inside the VM should be left enabled. View more information about [Setting a Static Internal Private IP](../virtual-network/virtual-networks-reserved-private-ip.md). |




<!--HONumber=Nov16_HO3-->


