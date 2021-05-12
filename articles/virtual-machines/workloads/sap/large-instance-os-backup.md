---
title: SAP HANA on Azure (Large Instances) のオペレーティング システムのバックアップと復元 | Microsoft Docs
description: SAP HANA on Azure (Large Instances) のオペレーティング システムのバックアップと復元を実行します
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c624f2cdee61d138722632869901e2c59110b1a9
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2021
ms.locfileid: "109735120"
---
# <a name="os-backup-and-restore"></a>OS のバックアップと復元

このドキュメントでは、オペレーティング システム ファイル レベルのバックアップと復元を実行する手順について説明します。 この手順は、特定のパラメーター (Type I または Type II、リビジョン 3 以降、場所など) によって異なります。お使いのリソースのこれらのパラメータの正確な値を取得するには、Microsoft Operations にお問い合わせください。

## <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>リビジョン 3 スタンプの Type II SKU の OS バックアップと復元

次の情報は、リビジョン 3 の HANA Large Instances の **Type II SKU** に対してオペレーティング システム ファイル レベルのバックアップと復元を実行する手順について説明しています。

>[!Important]
> **この記事は、リビジョン 4 HANA L インスタンス スタンプでの Type II SKU のデプロイには適用されません。** リビジョン 4 HANA L インスタンス スタンプにデプロイされている Type II HANA L インスタンス ユニットのブート LUN は、Type I SKU が既にリビジョン 3 スタンプにあるため、ストレージ スナップショットを使用してバックアップできます。


>[!NOTE]
>OS バックアップ スクリプトは、サーバーにプレインストールされている ReaR ソフトウェアを使用します。  

Microsoft `Service Management` チームによるプロビジョニングが完了すると、既定では、サーバーは、オペレーティング システムの背後でファイル システム レベルのバックアップを実行する 2 つのバックアップ スケジュールで構成されます。 次のコマンドを使用して、バックアップ ジョブのスケジュールを確認できます。
```
#crontab –l
```
次のコマンドを使用して、いつでもバックアップ スケジュールを変更できます。
```
#crontab -e
```
### <a name="how-to-take-a-manual-backup"></a>手動バックアップを実行する方法

OS ファイル システムのバックアップは、既に **cron ジョブ** を使用してスケジュールされています。 ただし、オペレーティング システムのファイル レベル バックアップを手動で実行することもできます。 手動バックアップを行うには、次のコマンドを実行します。

```
#rear -v mkbackup
```
次の画面は、手動バックアップのサンプルを示しています。

![方法](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


### <a name="how-to-restore-a-backup"></a>バックアップを復元する方法

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

### <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>ReaR ツールをインストールして構成を変更する方法 

Relax-and-Recover (ReaR) パッケージは、HANA L インスタンスの **Type II SKU** に **プレインストール** されているため、ユーザーは何もする必要がありません。 オペレーティング システムのバックアップのために、ReaR の使用を直接開始することができます。
ただし、パッケージを自分でインストールする必要がある場合は、記載されている手順に従って、ReaR ツールをインストールして構成することができます。

**ReaR** バックアップ パッケージをインストールするには、以下のコマンドを使用します。

**SLES** オペレーティング システムでは、次のコマンドを使用します。
```
#zypper install <rear rpm package>
```
**RHEL** オペレーティング システムでは、次のコマンドを使用します。 
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

次の情報は、リビジョン 3 の HANA Large Instances の **Type II SKU** を除くすべてのリビジョンのすべての SKU に対してオペレーティング システム ファイル レベルのバックアップと復元を実行する手順について説明しています。

### <a name="how-to-take-a-manual-backup"></a>手動バックアップを実行する方法

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) から最新の Microsoft Snapshot Tools for SAP HANA を入手し、`crontab` に `--type=boot` フラグを指定して定期的に実行されるように構成します。 これにより、定期的に OS がバックアップされるようになります。 次の例は、Type-I SKU OS バックアップの `/etc/crontab` の cron スケジュールを示しています。

```
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```

次の例は、Type-II SKU OS バックアップの `/etc/crontab` の cron スケジュールを示しています。

```
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

その他のリファレンス
- [ストレージ スナップショットのセットアップ](hana-backup-restore.md#set-up-storage-snapshots)
- [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) の Microsoft Snapshot Tools for SAP HANA ガイド。

### <a name="how-to-restore-a-backup"></a>バックアップを復元する方法

OS 自体から復元操作を実行することはできません。 これについては、Microsoft Operations にサポート チケットを送信してください。 復元操作では、HLI インスタンスが電源オフ状態になっている必要があるため、適宜スケジュールしてください。

### <a name="managed-os-snapshots"></a>マネージド OS スナップショット

Azure では、HLI リソースの OS バックアップを自動的に作成できます。 これらのバックアップは 1 日 1 回作成され、3 つの最新のバックアップが Azure で保持されます。 これは、次のリージョンのすべてのお客様に対しては既定で有効になっています。
- 米国西部
- オーストラリア東部
- オーストラリア南東部
- 米国中南部
- 米国東部 2

次のリージョンでは、この機能を部分的に利用できます。
- 米国東部
- 北ヨーロッパ
- 西ヨーロッパ

この機能によって作成されるバックアップの頻度または保有期間を変更することはできません。 HLI リソースに異なる OS バックアップ戦略が必要な場合は、Microsoft Operations にサポート チケットを送信して、この機能を無効にしてから、このドキュメントの前のセクションで説明した手順を使用して OS のバックアップを作成するように Microsoft Snapshot Tools for SAP HANA を構成します。