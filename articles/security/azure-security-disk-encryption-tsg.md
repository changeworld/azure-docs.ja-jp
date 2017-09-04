---
title: "Azure Disk Encryption のトラブルシューティング | Microsoft Docs"
description: "この記事では、Windows および Linux IaaS VM の Microsoft Azure Disk Encryption のトラブルシューティングのヒントについて説明します。"
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: ce0e23bd-07eb-43af-a56c-aa1a73bdb747
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: devtiw
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 5f482a92b8fcd71a1b767fcc5741bc57605997ea
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure Disk Encryption トラブルシューティング ガイド

このガイドは、所属組織が Azure Disk Encryption を使用しており、ディスクの暗号化に関連する問題のトラブルシューティングを必要としている情報技術 (IT) プロフェッショナル、情報セキュリティ アナリスト、クラウド管理者を対象としています。

## <a name="troubleshooting-linux-os-disk-encryption"></a>Linux OS ディスクの暗号化のトラブルシューティング

Linux OS ディスクの暗号化には、完全なディスク暗号化プロセスを実行する前に OS ドライブをマウント解除する必要があります。   これが失敗したときは、多くの場合、"次の操作後のマウント解除に失敗しました" というエラー メッセージが表示されます。

これは、ほとんどの場合、サポートされているストック ギャラリー イメージから編集または変更されたターゲット VM 環境に対して OS ディスク暗号化を実行しようとしている場合です。  OS ドライブのマウントを解除する拡張機能に影響を与える可能性のある、サポートされているイメージの状態の例は次のとおりです。
- カスタマイズされたイメージが、サポートされているファイル システムやパーティション構成に一致しなくなった。
- ウイルス対策、Docker、SAP、MongoDB、Apache Cassandra などのアプリケーションでカスタマイズされたイメージが暗号化前に OS で実行されている。  これらのアプリケーションは終了することが難しく、これらで OS ドライブに対しファイル ハンドルが開いたままになっていると、ドライブのマウント解除が失敗し、エラーの原因となります。
- カスタム スクリプトが暗号化の手順に近い時刻に実行された。これも、エラーの原因になる場合があります。 これは、Resource Manager テンプレートに、同時に実行する複数の拡張機能が定義されている場合、またはカスタム スクリプト拡張機能またはその他のアクションがディスクの暗号化と同時に実行された場合に発生する可能性があります。   このような手順を、連続した複数の手順にわけるか、分離して実行することで問題を解決できます。
- 暗号化を有効にする前に SELinux が無効化されていないとマウント解除の手順は失敗します。  SELinux は暗号化が完了した後、再度有効にできます。
- OS ディスクが LVM スキームを使用している場合 (一部の LVM データ ディスクはサポートされていますが、LVM OS ディスクはサポートされていません)
- 最小メモリ要件が満たされない場合 (OS ディスクの暗号化には 7 GB を推奨します)
- データ ドライブが /mnt/ ディレクトリ以下で、または相互 (たとえば /mnt/data1、/mnt/data2、/data3 + /data3/data4 など) に再帰的にマウントされている場合
- その他の Linux 用の Azure Disk Encryption の[前提条件](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption)が満たされていない場合

## <a name="unable-to-encrypt"></a>暗号化できない

場合によっては、ディスクの暗号化が「OS ディスクの暗号化が開始されました」というメッセージが表示されたまま先へ進まず、SSH が無効になっているように見えることがあります。 このプロセスは、ストック ギャラリー イメージで完了するまでに 3 ～ 16 時間かかることがあります。  数テラバイトのデータ ディスクを追加した場合、数日かかることもあります。 Linux OS ディスク暗号化のシーケンスでは、OS ドライブのマウントを一時的に解除し、OS ディスク全体をブロックごとに暗号化します。その後、暗号化された状態に再度マウントします。   Windows 上の Azure Disk Encryption とは異なり、Linux のディスク暗号化では、暗号化が行われている間、VM を同時に使用できません。  ディスクのサイズやストレージ アカウントが Standard または Premium （SSD） ストレージのどちらを使用しているか含め、VM のパフォーマンス特性によって、暗号化を完了するために必要な時間が大きく異なります。

ステータスを確認するには、[Get AzureRmVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) コマンドから返される ProgressMessage フィールドをポーリングすることができます。   OS ドライブが暗号化されている間は、VM はメンテナンス状態に入り、進行中のプロセスの中断を防ぐため SSH も無効になります。  暗号化が実行されている大部分の間 EncryptionInProgress は報告され、数時間後に、VM の再起動を求める VMRestartPending メッセージが表示されます。  For example:


```
PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

VM の再起動を求めるメッセージに従って VM を再起動し、ターゲットに最終的な手順が実行され再起動されるまで 2 ～ 3 分待ちます。暗号化が完了したことを示すステータス メッセージが表示されます。   このメッセージが表示されると、OS ドライブを使用する準備ができ、VM も再度使用できるようになります。

この順序に従って手順が進まない場合や、ブート情報、進行状況メッセージ、またはこのプロセス中に OS の暗号化が失敗したことを報告するその他のエラー メッセージ （たとえば、このガイドで説明されている「マウント解除に失敗しました」エラー）が表示された場合は、VM をスナップショットまたは暗号化直前のバックアップに復元することをお勧めします。  再試行する前に、VM の特性を再評価して、すべての前提条件が満たされていることを確認することをお勧めします。

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>ファイアウォール内の Azure Disk Encryption のトラブルシューティング
ファイアウォール、プロキシ要件、またはネットワーク セキュリティ グループ (NSG) 設定によって接続が制限されていると、必要なタスクを実行するための拡張機能に障害が出ることがあります。   この場合、「拡張機能の状態が VM で取得できません」などのステータス メッセージが表示され、シナリオが想定どおりに完了しません。  以下のセクションでは、調査することが推奨される一般的なファイアウォールの問題について説明します。

### <a name="network-security-groups"></a>ネットワーク セキュリティ グループ
適用されるネットワーク セキュリティ グループ設定で、ディスクの暗号化のために規定されている、ネットワーク構成[の前提条件](https://docs.microsoft.com/azure/security/azure-security-disk-encryption#prerequisites)を満たすようエンドポイントが設定されている必要があります。

### <a name="azure-keyvault-behind-firewall"></a>ファイアウォール内の Aazure Keyvault
VM は、キー コンテナーにアクセスできる必要があります。 [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall) チームが管理しているファイアウォール内からのキー コンテナーへのアクセスに関するガイダンスを参照してください。

### <a name="linux-package-management-behind-firewall"></a>ファイアウォール内の Linux パッケージの管理
実行時の Linux の Azure Disk Encryption では、暗号化を有効にする前に、ターゲット ディストリビューションのパッケージ管理システムを使用して、必要な前提条件コンポーネントをインストールする必要があります。  ファイアウォールの設定が原因で、これらのコンポーネントを VM にダウンロードおよびインストールできないと、想定される次のシーケンスが失敗します。    これを構成する手順は、ディストリビューションによって異なります。  Red hat では、プロキシが必要な場合、サブスクリプション マネージャーおよび yum が正しく設定されていることが重要になります。  このトピックについては、[この](https://access.redhat.com/solutions/189533) Red Hat サポート記事を参照してください。  

## <a name="troubleshooting-windows-server-2016-server-core"></a>Windows Server 2016 Server Core のトラブルシューティング

Windows Server 2016 Server Core では既定で、bdehdcfg コンポーネントを使用できません。 このコンポーネントは、Azure Disk Encryption で必要です。

この問題を解決するには、次の 4 つのファイルを Windows Server 2016 Data Center VM から Server Core イメージの c:\windows\system32 フォルダーにコピーします。

```
bdehdcfg.exe
bdehdcfglib.dll
bdehdcfglib.dll.mui
bdehdcfg.exe.mui
```

次に、次のコマンドを実行します。

```
bdehdcfg.exe -target default
```

これにより 550 MB のシステム パーティションが作成され、再起動した後、Diskpart を使用してボリュームを確認し、続行できます。  

For example:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
## <a name="see-also"></a>関連項目
このドキュメントでは、Azure Disk Encryption で暗号化する際に発生する一般的な問題の詳細と、トラブルシューティング方法について説明しました。 このサービスとその機能の詳細については、以下を参照してください。

- [Azure Security Center でディスクの暗号化を適用する](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure Virtual Machine の暗号化](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [保存時の Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

