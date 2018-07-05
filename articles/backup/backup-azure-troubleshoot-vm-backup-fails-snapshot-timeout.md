---
title: 'Azure Backup の失敗のトラブルシューティング: ゲスト エージェントの状態を確認できない'
description: エージェント、拡張機能、ディスクに関する Azure Backup のエラーの症状、原因、解決策。
services: backup
author: genlin
manager: cshepard
keywords: Azure Backup; VM エージェント; ネットワーク接続;
ms.service: backup
ms.topic: troubleshooting
ms.date: 06/25/2018
ms.author: genli
ms.openlocfilehash: 09cfda3c2c790297b0961ecac92cba61c9e6de6f
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754202"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Azure Backup の失敗のトラブルシューティング: エージェント/拡張機能に関する問題

この記事では、VM エージェントと拡張機能との通信に関連する Azure Backup エラーの解決に役立つ可能性のあるトラブルシューティング手順について説明します。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="vm-agent-unable-to-communicate-with-azure-backup"></a>VM エージェントが Azure Backup と通信できない

エラー メッセージ: "VM Agent unable to communicate with Azure Backup (VM エージェントが Azure Backup と通信できません)"<br>
エラー コード: "UserErrorGuestAgentStatusUnavailable"

Backup サービスに VM を登録してスケジュール設定すると、Backup サービスは、VM エージェントと通信してジョブを開始し、ポイントインタイム スナップショットを作成します。 以下のいずれかの状況によって、スナップショットをトリガーできない場合があります。 スナップショットがトリガーされずにバックアップが失敗する可能性があります。 次のトラブルシューティング手順を上から順に実行した後で、必要な操作を再試行してください。

**原因 1:[ VM がインターネットにアクセスできない](#the-vm-has-no-internet-access)**  
**原因 2:[ エージェントが VM にインストールされているが応答しない (Windows VM の場合)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**原因 3:[ VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 4:[ スナップショットの状態を取得できないか、スナップショットを作成できない](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**    
**原因 5:[ バックアップ拡張機能の更新または読み込みに失敗した](#the-backup-extension-fails-to-update-or-load)**  

## <a name="snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>仮想マシンがネットワークに接続されていないためにスナップショット操作に失敗した

エラー メッセージ: "仮想マシンがネットワークに接続していないため、スナップショット操作に失敗しました"<br>
エラー コード: "ExtensionSnapshotFailedNoNetwork"

Azure Backup サービスに VM を登録して、スケジュール設定すると、Backup サービスは、VM のバックアップ拡張機能と通信してジョブを開始し、ポイントインタイム スナップショットを作成します。 以下のいずれかの状況によって、スナップショットをトリガーできない場合があります。 スナップショットがトリガーされなかった場合、バックアップ エラーが発生する可能性があります。 次のトラブルシューティング手順を上から順に実行した後で、必要な操作を再試行してください。    
**原因 1:[ VM がインターネットにアクセスできない](#the-vm-has-no-internet-access)**  
**原因 2:[ スナップショットの状態を取得できないか、スナップショットを作成できない](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 3:[ バックアップ拡張機能の更新または読み込みに失敗した](#the-backup-extension-fails-to-update-or-load)**  

## <a name="vmsnapshot-extension-operation-failed"></a>VMSnapshot 拡張機能の操作に失敗した

エラー メッセージ: "VMSnapshot の拡張操作に失敗しました"<br>
エラー コード: "ExtentionOperationFailed"

Azure Backup サービスに VM を登録して、スケジュール設定すると、Backup サービスは、VM のバックアップ拡張機能と通信してジョブを開始し、ポイントインタイム スナップショットを作成します。 以下のいずれかの状況によって、スナップショットをトリガーできない場合があります。 スナップショットがトリガーされなかった場合、バックアップ エラーが発生する可能性があります。 次のトラブルシューティング手順を上から順に実行した後で、必要な操作を再試行してください。  
**原因 1:[ スナップショットの状態を取得できないか、スナップショットを作成できない](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 2:[ バックアップ拡張機能の更新または読み込みに失敗した](#the-backup-extension-fails-to-update-or-load)**  
**原因 3:[ エージェントが VM にインストールされているが応答しない (Windows VM の場合)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 4:[ VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backup-fails-because-the-vm-agent-is-unresponsive"></a>VM エージェントが応答しないためにバックアップに失敗する

エラー メッセージ: "スナップショットの状態について VM エージェントと通信できませんでした" <br>
エラー コード: "GuestAgentSnapshotTaskStatusError"

Azure Backup サービスに VM を登録して、スケジュール設定すると、Backup サービスは、VM のバックアップ拡張機能と通信してジョブを開始し、ポイントインタイム スナップショットを作成します。 以下のいずれかの状況によって、スナップショットをトリガーできない場合があります。 スナップショットがトリガーされなかった場合、バックアップ エラーが発生する可能性があります。 次のトラブルシューティング手順を上から順に実行した後で、必要な操作を再試行してください。  
**原因 1:[ エージェントが VM にインストールされているが応答しない (Windows VM の場合)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 2:[ VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 3:[ VM がインターネットにアクセスできない](#the-vm-has-no-internet-access)**  

## <a name="backup-fails-with-an-internal-error"></a>内部エラーでバックアップに失敗する

エラー メッセージ: "バックアップ操作は内部エラーのため失敗しました - 数分以内に操作をやり直してください。" <br>
エラー コード: "BackUpOperationFailed"/"BackUpOperationFailedV2"

Azure Backup サービスに VM を登録して、スケジュール設定すると、Backup サービスは、VM のバックアップ拡張機能と通信してジョブを開始し、ポイントインタイム スナップショットを作成します。 以下のいずれかの状況によって、スナップショットをトリガーできない場合があります。 スナップショットがトリガーされなかった場合、バックアップ エラーが発生する可能性があります。 次のトラブルシューティング手順を上から順に実行した後で、必要な操作を再試行してください。  
**原因 1:[ VM がインターネットにアクセスできない](#the-vm-has-no-internet-access)**  
**原因 2:[ エージェントが VM にインストールされているが応答しない (Windows VM の場合)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 3:[ VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 4:[ スナップショットの状態を取得できないか、スナップショットを作成できない](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 5:[ バックアップ拡張機能の更新または読み込みに失敗した](#the-backup-extension-fails-to-update-or-load)**  
**原因 6:[ リソース グループのロックが原因で、Backup サービスに古い復元ポイントを削除するためのアクセス許可がない](#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock)**

## <a name="causes-and-solutions"></a>原因とソリューション

### <a name="the-vm-has-no-internet-access"></a>VM がインターネットにアクセスできない
デプロイ要件に準拠したインターネット アクセスが VM にありません。 または、Azure インフラストラクチャへのアクセスを禁止する制限が設けられている可能性があります。

バックアップ拡張機能が正常に機能するには、Azure のパブリック IP アドレスへの接続が必要です。 この拡張機能が、VM のスナップショットを管理するコマンドを、Azure Storage エンドポイント (HTTP URL) に送信するためです。 拡張機能がパブリック インターネットにアクセスできない場合は、最終的にバックアップが失敗します。

VM トラフィックのルーティングに、プロキシ サーバーをデプロイすることもできます。
##### <a name="create-a-path-for-https-traffic"></a>HTTP トラフィック用のパスを作成する

1. ネットワーク制限 (ネットワーク セキュリティ グループなど) を設定している場合は、トラフィックをルーティングするための HTTP プロキシ サーバーをデプロイします。
2. HTTP プロキシ サーバーからインターネットへのアクセスを許可するには、規則をネットワーク セキュリティ グループに追加します (ネットワーク セキュリティ グループがある場合)。

VM バックアップの HTTP プロキシを設定する方法については、「[Azure 仮想マシンをバックアップする環境の準備](backup-azure-arm-vms-prepare.md#establish-network-connectivity)」を参照してください。

バックアップ VM またはトラフィックがルーティングされるプロキシ サーバーのいずれかに、Azure のパブリック IP アドレスへのアクセスが必要です。

####  <a name="solution"></a>解決策
この問題を解決するには、次の方法のいずれかを試してください。

##### <a name="allow-access-to-azure-storage-that-corresponds-to-the-region"></a>リージョンに対応する Azure Storage へのアクセスを許可する

[サービス タグ](../virtual-network/security-overview.md#service-tags)を使用し、特定のリージョンのストレージに接続できます。 ストレージ アカウントへのアクセスを許可するルールが、インターネット アクセスをブロックするルールよりも優先度が高いことを確認してください。 

![リージョンのストレージ タグが与えられたネットワーク セキュリティ グループ](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

サービス タグを構成する詳細な手順については、[こちらのビデオ](https://youtu.be/1EjLQtbKm1M)をご覧ください。

> [!WARNING]
> ストレージ サービスのタグはプレビュー版であり、 特定のリージョンでのみ利用できます。 リージョンの一覧については、[ストレージのサービス タグ](../virtual-network/security-overview.md#service-tags)に関するページを参照してください。

Azure Managed Disks を使用する場合、ファイアウォールで別途ポート (ポート 8443) が開放されている必要があります。

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>エージェントが VM にインストールされているが応答しない (Windows VM の場合)

#### <a name="solution"></a>解決策
VM エージェントが破損しているまたはサービスが停止している可能性があります。 VM エージェントを再インストールすることで最新バージョンを入手できます。 その際に、サービスとの通信も再開されます。

1. VM サービス (services.msc) で Windows ゲスト エージェント サービスが実行されているかどうかを確認します。 Windows ゲスト エージェント サービスの再起動とバックアップの開始を試みます。    
2. コントロール パネルの [サービス] に Windows ゲスト エージェント サービスが表示されない場合は、**[プログラムと機能]** に移動し、Windows ゲスト エージェント サービスがインストールされているかどうかを確認してください。
4. Windows ゲスト エージェント サービスが **[プログラムと機能]** に表示される場合は、Windows ゲスト エージェントをアンインストールします。
5. [最新バージョンのエージェント MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) をダウンロードしてインストールします。 インストールを実行するには、管理者権限が必要です。
6. [サービス] に Windows ゲスト エージェント サービスが表示されることを確認します。
7. オンデマンド バックアップを実行します。 
    * ポータルの **[今すぐバックアップ]** を選択します。

さらに、VM に [Microsoft .NET 4.5 がインストールされていること](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)を確認します。 VM エージェントがサービスと通信するためには .NET 4.5 が必要です。

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM にインストールされているエージェントが古くなっている (Linux VM の場合)

#### <a name="solution"></a>解決策
Linux VM の場合、エージェントに関連するエラーまたは拡張機能に関連するエラーのほとんどは、古い VM エージェントに影響する問題が原因で発生します。 この問題のトラブルシューティングを行うには、次の一般的なガイドラインに従います。

1. [Linux VM エージェントを更新](../virtual-machines/linux/update-agent.md)する手順に従います。

 > [!NOTE]
 > ディストリビューション リポジトリを通してのみエージェントを更新することを "*強くお勧め*" します。 エージェント コードを直接 GitHub からダウンロードして、更新することはお勧めしません。 最新のエージェントをディストリビューションで使用できない場合は、そのエージェントをインストールする方法をディストリビューション サポートにお問い合わせください。 最新のエージェントを確認するには、GitHub リポジトリの [Windows Azure Linux エージェント](https://github.com/Azure/WALinuxAgent/releases)のページをご覧ください。

2. `ps -e` コマンドを実行して、Azure エージェントが VM で実行されていることを確認します。

 このプロセスが実行されていない場合は、次のコマンドを使用してプロセスを再起動します。

 * Ubuntu の場合: `service walinuxagent start`
 * その他のディストリビューションの場合: `service waagent start`

3. [エージェントの自動再起動を構成します](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)。
4. 新しいテスト バックアップを実行します。 エラーが解決しない場合は、VM から次のログを収集してください。

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

waagent の詳細ログが必要な場合は、次の手順に従います。

1. /etc/waagent.conf ファイルで、次の行を見つけます: **Enable verbose logging (y|n)**
2. **Logs.Verbose** の値を *n* から *y* に変更します。
3. 変更を保存した後、このセクションで前述した手順を実行して waagent を再起動します。

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>スナップショットの状態を取得できないか、スナップショットを作成できない
VM のバックアップは、基礎となるストレージ アカウントへのスナップショット コマンドの発行に依存します。 ストレージ アカウントにアクセスできないか、スナップショット タスクの実行が遅れているために、バックアップが失敗することがあります。

#### <a name="solution"></a>解決策
次の場合にスナップショットのタスクが失敗することがあります。

| 原因 | 解決策 |
| --- | --- |
| VM が リモート デスクトップ プロトコル (RDP) でシャットダウンされているため、VM の状態が正しく報告されない。 | RDP で VM をシャットダウンした場合は、ポータルでその VM の状態が正しいかどうかを確認します。 正しくない場合は、VM のダッシュボードの **[シャットダウン]** オプションを使用して、ポータルで VM をシャットダウンします。 |
| VM が DHCP からホスト/ファブリック アドレスを取得できない。 | IaaS VM バックアップが正しく機能するには、ゲスト内で DHCP が有効になっている必要があります。 VM が DHCP 応答 245 からホスト/ファブリック アドレスを取得できない場合は、拡張機能をダウンロードしたり実行したりできません。 静的プライベート IP が必要な場合は、プラットフォームを通じて構成する必要があります。 VM 内の DHCP オプションは有効のままにしておいてください。 詳細については、[静的内部プライベート IP の設定](../virtual-network/virtual-networks-reserved-private-ip.md)に関するページをご覧ください。 |

### <a name="the-backup-extension-fails-to-update-or-load"></a>バックアップ拡張機能の更新または読み込みに失敗した
拡張機能を読み込むことができないと、スナップショットを作成できないため、バックアップに失敗します。

#### <a name="solution"></a>解決策

VMSnapshot 拡張機能に再読み込みを強制する 拡張機能をアンインストールします。 次回のバックアップ時に、拡張機能が再度読み込まれます。

拡張機能をアンインストールするには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) で、バックアップ エラーが発生している VM に移動します。
2. **[設定]** を選択します。
3. **[拡張機能]** を選択します。
4. **[Vmsnapshot 拡張機能]** を選択します。
5. **[アンインストール]** を選択します。

Linux VM で、VMSnapshot 拡張機能が Azure Portal に表示されない場合は、[Azure Linux エージェントを更新](../virtual-machines/linux/update-agent.md)してから、バックアップを実行してください。 

この手順を済ませておくと、次回のバックアップ時に拡張機能が再インストールされます。

### <a name="backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock"></a>リソース グループのロックが原因で、バックアップ サービスに古い復元ポイントを削除するためのアクセス許可がない
これは、リソース グループがユーザーによってロックされる管理された VM に固有の問題です。 この場合、以前の復元ポイントをバックアップ サービスで削除することはできません。 復元ポイントの上限は 18 個であるため、それを超える新しいバックアップは失敗します。

#### <a name="solution"></a>解決策

この問題を解決するには、リソース グループからロックを削除し、次の手順を使用して復元ポイント コレクションを削除します。 
 
1. VM が存在するリソース グループのロックを解除します 
2. Chocolatey を使用して ARMClient をインストールします。 <br>
   https://github.com/projectkudu/ARMClient
3. ARMClient にサインインします。 <br>
    `.\armclient.exe login`
4. VM に対応する復元ポイント コレクションを取得します。 <br>
    `.\armclient.exe get https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30`

    例: `.\armclient.exe get https://management.azure.com/subscriptions/f2edfd5d-5496-4683-b94f-b3588c579006/resourceGroups/winvaultrg/providers/Microsoft.Compute/restorepointcollections/AzureBackup_winmanagedvm?api-version=2017-03-30`
5. 復元ポイント コレクションを削除します。 <br>
    `.\armclient.exe delete https://management.azure.com/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Compute/restorepointcollections/AzureBackup_<VM-Name>?api-version=2017-03-30` 
6. 次のスケジュールされたバックアップで、復元ポイント コレクションと新しい復元ポイントが自動的に作成されます。

完了したら、VM リソース グループでロックをもう一度元に戻せます。 
