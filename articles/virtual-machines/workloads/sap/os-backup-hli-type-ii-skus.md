---
title: SAP HANA on Azure (L インスタンス) Type II SKU のオペレーティング システムのバックアップと復元 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) Type II SKU のオペレーティング システムのバックアップと復元を実行します
services: virtual-machines-linux
documentationcenter: ''
author: jaawasth
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: jaawasth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 245c371a002ebfdad4dd0765ba3d283c81c86f03
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110631712"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>リビジョン 3 スタンプの Type II SKU の OS バックアップと復元

このドキュメントでは、リビジョン 3 の HANA L インスタンスの **Type II SKU** に対してオペレーティング システム ファイル レベルのバックアップと復元を実行する手順について説明します。 

>[!Important]
> **この記事は、リビジョン 4 HANA L インスタンス スタンプでの Type II SKU のデプロイには適用されません。** リビジョン 4 HANA L インスタンス スタンプにデプロイされている Type II HANA L インスタンス ユニットのブート LUN は、Type I SKU が既にリビジョン 3 スタンプにあるため、ストレージ スナップショットを使用してバックアップできます。


>[!NOTE]
> * OS のバックアップ スクリプトには xfsdump ユーティリティが使用されます。  
> * このドキュメントは完全なルート ファイル システムのバックアップをサポートしますが、**増分バックアップはサポート対象外** です。
> * バックアップの作成中は、同じシステムにファイルが書き込まれないようにしてください。  そうしないと、バックアップ中に書き込まれたファイルがバックアップから漏れる能性があります。
> * Revision 3 の HANA Large Instances の Type II SKU では、ReaR バックアップが非推奨となっています。
> * この手順は、OS が破損するさまざまなシナリオを背景に社内でテストされています。 ただし、OS についての全責任はお客様が負います。ご自身のシナリオにこのドキュメントを利用する際は、あらかじめ十分にテストすることをお勧めします。
> * この手順は SLES OS でテストされています。
> * メジャー バージョン アップグレード (SLES 12.x から SLES 15x へのアップグレードなど) はサポートされません。
> * この手順で OS を復元するには、Microsoft のサポートが必要です。復旧にはコンソール アクセスが必要となるためです。 Microsoft に復旧を支援してもらえるようサポート チケットを作成してください。


## <a name="how-to-take-a-manual-backup"></a>手動バックアップを実行する方法

手動バックアップを実行するには:

1. バックアップ ツールをインストールします。
   ```
   zypper in xfsdump
   ```

2. 完全なバックアップを作成します。 
   ```
   xfsdump -l 0 -f /data1/xfs_dump /
   ```

   次の画面は、手動バックアップのサンプルを示しています。
   
    [![ダンプ キャプチャ](media/HowToHLI/OSBackupTypeIISKUs/dump-capture.png)](media/HowToHLI/OSBackupTypeIISKUs/dump-capture.png#lightbox)


3. 重要: data1 パーティションも破損しているシナリオでは、NFS ボリュームにもバックアップのコピーを保存してください。
   ```
   cp /data1/xfs_dump /osbackup/
   ```

4. 通常のディレクトリとファイルをダンプから除外するために、chattr でファイルにタグを付けてください。
   * chattr -R +d <ディレクトリ>
   * chattr +d <ファイル>
   * "-e" オプションを指定して xfsdump を実行
   * 注: nfs ファイル システム [ntfs] を除外することはできません




## <a name="how-to-restore-a-backup"></a>バックアップを復元する方法

>[!NOTE]
> * この手順は、Microsoft の運用チームに協力を要請する必要があります。
> * この手順で OS を復元するには、Microsoft のサポートが必要です。復旧にはコンソール アクセスが必要となるためです。 Microsoft に復旧を支援してもらえるようサポート チケットを作成してください。
> * 完全なファイルシステムを Microsoft が復元します。

1. OS の ISO をシステムにマウントします。

2. レスキュー モードに切り替えます。

3. 読み取り/書き込みモードで data1 (またはダンプが格納される nfs ボリューム) パーティションをマウントします。
   ```
   mount -o rw /dev/md126p4 /mnt1
   ```
4. 読み取り/書き込みモードでルートをマウントします。
   ```
   mount -o rw /dev/md126p2 /mnt2
   ```
5. ファイルシステムを復元します。
   ```
   xfsrestore -f /mnt1/xfs_dump /mnt2
   ```
   [![復元のスクリーンショット](media/HowToHLI/OSBackupTypeIISKUs/restore-screenshot.png)](media/HowToHLI/OSBackupTypeIISKUs/restore-screenshot.png#lightbox)
6. システムを再起動します。
   ```
   reboot
   ```

ポスト チェックに失敗する場合、コンソール アクセスが必要です。OS ベンダーと Microsoft に協力を要請してください。

## <a name="post-restore-check"></a>復元後のチェック

1. システムの属性が完全に復元されていることを確認します。
   * ネットワークが稼動していること。
   * NFS ボリュームがマウントされていること。
2. RAID が構成されていることを確認します。実際の RAID デバイスに置き換えてください。
   ```
   mdadm -D /dev/md126
   ```
   [![RAID の状態](media/HowToHLI/OSBackupTypeIISKUs/raid-status.png)](media/HowToHLI/OSBackupTypeIISKUs/raid-status.png#lightbox)

3. RAID ディスクが同期され、構成がクリーンな状態になっていることを確認します。
   * RAID ディスクの同期には少し時間がかかります。100% 同期された状態になるまでに数分かかる場合もあります。

4. HANA DB を起動し、HANA が正しく動作していることを確認します。

5. HANA が表示されていること、またエラーが発生していないことを確認します。
   ```
   hdbinfo
   ```
   [![HANA の状態](media/HowToHLI/OSBackupTypeIISKUs/hana-status.png)](media/HowToHLI/OSBackupTypeIISKUs/hana-status.png#lightbox)

6. ポスト チェックに失敗する場合、コンソール アクセスが必要です。OS ベンダーと Microsoft に協力を要請してください。