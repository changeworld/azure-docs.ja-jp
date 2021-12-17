---
title: SAP HANA on Azure (Large Instances) のオペレーティング システムのバックアップと復元 | Microsoft Docs
description: SAP HANA on Azure (Large Instances) のオペレーティング システムのバックアップと復元を実行する方法を説明します。
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/22/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e7d2621aab6d49c8bd6f4b6083c35d5e7299bf50
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630511"
---
# <a name="os-backup-and-restore"></a>OS のバックアップと復元

この記事では、オペレーティング システム (OS) のファイルレベルのバックアップと復元を実行する手順について説明します。 この手順は、Type I、Type II、リビジョン 3 以上、保存先などのパラメーターによって異なります。 リソースのこれらのパラメーターの値を取得するには、Microsoft Operations にお問い合わせください。

## <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>リビジョン 3 スタンプの Type II SKU の OS バックアップと復元

次の情報は、HANA Large Instances リビジョン 3 の **Type II SKU** に対してオペレーティング システム ファイルレベルのバックアップと復元を実行する手順について説明しています。

>[!Important]
> **この記事は、リビジョン 4 HANA L インスタンス スタンプでの Type II SKU のデプロイには適用されません。** リビジョン 4 HANA Large Instance スタンプにデプロイされている Type II HANA Large Instance のブート LUN は、ストレージ スナップショットを使用してバックアップできます。これは、既にリビジョン 3 スタンプにある Type I SKU にも当てはまります。


>[!NOTE]
>OS バックアップ スクリプトは、サーバーにプレインストールされている ReaR ソフトウェアを使用します。  

Microsoft サービス管理チームによるプロビジョニングが完了すると、サーバーは、既定では OS のファイルシステム レベルのバックアップを実行する 2 つのスケジュールで構成されます。 次のコマンドを使用して、バックアップ ジョブのスケジュールを確認できます。

```
#crontab –l
```
次のコマンドを使用して、いつでもバックアップ スケジュールを変更できます。
```
#crontab -e
```
### <a name="take-a-manual-backup"></a>手動バックアップの取得

OS ファイル システムのバックアップは、既に **cron ジョブ** を使用してスケジュールされています。 ただし、オペレーティング システムのファイルレベルのバックアップを手動で実行することもできます。 手動バックアップを行うには、次のコマンドを実行します。

```
#rear -v mkbackup
```
次の画面は、手動バックアップのサンプルを示しています。

![方法](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


### <a name="restore-a-backup"></a>バックアップを復元する

バックアップから、完全バックアップまたは個々のファイルを復元できます。 復元するには、次のコマンドを使用します。

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
復元後、ファイルは現在の作業ディレクトリに回復されます。

次のコマンドは、バックアップ ファイル *backup.tar.gz* からのファイル */etc/fstab* の復元を示しています。
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>ファイルは、バックアップから復元された後、必要な場所にコピーする必要があります。

次のスクリーンショットは、完全バックアップの復元を示しています。

![コマンド プロンプト ウィンドウのスクリーンショット。復元を確認できます。](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

### <a name="install-the-rear-tool-and-change-the-configuration"></a>ReaR ツールをインストールして構成を変更する 

Relax-and-Recover (ReaR) パッケージは、HANA Large Instances の **Type II SKU** に **プレインストール** されています。 お客様が対処する必要ありません。 オペレーティング システムのバックアップには、直接、ReaR ツールの使用を開始できます。

ただし、パッケージを自分でインストールする必要がある場合は、次の手順に従って、ReaR ツールをインストールして構成することができます。

**ReaR** バックアップ パッケージをインストールするには、以下のコマンドを使用します。

**SLES** オペレーティング システムの場合、次のコマンドを使用します。
```
#zypper install <rear rpm package>
```
**RHEL** オペレーティング システムの場合、次のコマンドを使用します。 

```
#yum install rear -y
```
ReaR ツールを構成するには、 */etc/rear/local.conf* ファイルの **OUTPUT_URL** パラメーターと **BACKUP_URL** パラメーターを更新する必要があります。

```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

次のスクリーンショットは、完全バックアップの復元を示しています。![コマンド プロンプト ウィンドウのスクリーンショット。ReaR ツールで復元します。](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)


## <a name="os-backup-and-restore-for-all-other-skus"></a>他のすべての SKU の OS のバックアップと復元

次の情報は、リビジョン 3 の HANA Large Instances の **Type II SKU** を除くすべてのリビジョンのすべての SKU に対してオペレーティング システムのファイルレベルでのバックアップと復元を実行する手順について説明しています。

### <a name="take-a-manual-backup"></a>手動バックアップの取得

「[Azure アプリケーション整合性スナップショット ツールとは](../../../azure-netapp-files/azacsnap-introduction.md)」で始まる一連の記事で説明されている最新の Microsoft Snapshot Tools for SAP HANA を入手してください。 それらを次の記事の説明に従って、構成してテストします。

- [Azure アプリケーション整合性スナップショット ツールを構成する](../../../azure-netapp-files/azacsnap-cmd-ref-configure.md)
- [Azure アプリケーション整合性スナップショット ツールをテストする](../../../azure-netapp-files/azacsnap-cmd-ref-test.md) 

このレビューでは、「[Azure アプリケーション整合性スナップショット ツールを使用してバックアップする](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md)」の説明に従って `crontab` を介してバックアップを定期的に実行するために準備します。 

詳細については、次のリファレンスを参照してください。

- [Azure アプリケーション整合性スナップショット ツールをインストールする](../../../azure-netapp-files/azacsnap-installation.md)
- [Azure アプリケーション整合性スナップショット ツールを構成する](../../../azure-netapp-files/azacsnap-cmd-ref-configure.md)
- [Azure アプリケーション整合性スナップショット ツールをテストする](../../../azure-netapp-files/azacsnap-cmd-ref-test.md)
- [Azure アプリケーション整合性スナップショット ツールを使用してバックアップする](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md)
- [Azure アプリケーション整合性スナップショット ツールを使用して詳細を取得する](../../../azure-netapp-files/azacsnap-cmd-ref-details.md)
- [Azure アプリケーション整合性スナップショット ツールを使用して削除する](../../../azure-netapp-files/azacsnap-cmd-ref-delete.md)
- [Azure アプリケーション整合性スナップショット ツールを使用して復元する](../../../azure-netapp-files/azacsnap-cmd-ref-restore.md)
- [Azure アプリケーション整合性スナップショット ツールを使用したディザスター リカバリー](../../../azure-netapp-files/azacsnap-disaster-recovery.md)
- [Azure アプリケーション整合性スナップショット ツールのトラブルシューティング](../../../azure-netapp-files/azacsnap-troubleshoot.md)
- [Azure アプリケーション整合性スナップショット ツールを使用するためのヒントとテクニック](../../../azure-netapp-files/azacsnap-tips.md)


### <a name="restore-a-backup"></a>バックアップを復元する

OS 自体から復元操作を実行することはできません。 Microsoft Operations にサポート チケットを送信する必要があります。 復元操作の場合、HANA Large Instance (HLI) が電源オフ状態になっている必要があるため、適宜スケジュールしてください。

### <a name="managed-os-snapshots"></a>マネージド OS スナップショット

Azure では、HLI リソースの OS バックアップを自動的に作成できます。 これらのバックアップは 1 日 1 回作成され、最新のバックアップが最大 3 つまで Azure で保持されます。 これらのバックアップは、次のリージョンのすべてのお客様に対しては既定で有効になっています。
- 米国西部
- オーストラリア東部
- オーストラリア南東部
- 米国中南部
- 米国東部 2

次のリージョンについては、この機能を部分的に利用できます。
- 米国東部
- 北ヨーロッパ
- 西ヨーロッパ

この機能によって作成されるバックアップの頻度または保有期間を変更することはできません。 HLI リソースに異なる OS バックアップ戦略が必要な場合は、Microsoft Operations にサポート チケットを送信して、この機能をオプトアウトすることができます。 次に、前のセクション「[手動バックアップを実行する](#take-a-manual-backup)」で説明した手順を使用して OS のバックアップをとるように Microsoft Snapshot Tools for SAP HANA を構成します。

## <a name="next-steps"></a>次のステップ

HANA Large Instances の kdump を有効にする方法について説明します。

> [!div class="nextstepaction"]
> [SAP HANA on Azure Large Instances の kdump](hana-large-instance-enable-kdump.md)
