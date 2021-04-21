---
author: v-amallick
ms.service: Azure Backup
ms.topic: include
ms.date: 04/16/2021
ms.author: v-amallick
ms.openlocfilehash: e7af0819a94661a1008d96b7d0738c30a4a80c18
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716751"
---
1. バックアップの対象として選択されている Azure VM に対して、Azure Backup はユーザーが指定したバックアップ スケジュールに従ってバックアップ ジョブを開始します。
1. 最初のバックアップ時に、バックアップ拡張機能が VM にインストールされます (VM が実行されている場合)。
    - Windows VM の場合、[VMSnapshot 拡張機能](../articles/virtual-machines/extensions/vmsnapshot-windows.md)がインストールされます。
    - Linux VM の場合、[VMSnapshotLinux](../articles/virtual-machines/extensions/vmsnapshot-linux.md) 拡張機能がインストールされます。
1. 実行されている Windows VM については、Backup は Windows ボリューム シャドウ コピー サービス (VSS) と連携して VM のアプリ整合性スナップショットを取得します。
    - 既定では、Backup によって完全 VSS バックアップが取得されます。
    - Backup はアプリ整合性スナップショットを取得できない場合、基になるストレージのファイル整合性スナップショットを取得します (VM の停止中はアプリケーションの書き込みは行われないため)。
1. Linux VM の場合、Backup ではファイル整合性スナップショットが取得されます。 アプリ整合性スナップショットの場合は、事前/事後スクリプトを手動でカスタマイズする必要があります。
1. Backup でスナップショットが取得された後、コンテナーにデータが転送されます。
    - バックアップは、各 VM ディスクを並列にバックアップすることで最適化されます。
    - バックアップされているディスクごとに、Azure Backup はディスク上のブロックを読み取り、前回のバックアップ以降に変更されたデータ ブロックのみ (差分) を識別して転送します。
    - スナップショット データはコンテナーにすぐにコピーされない場合があります。 ピーク時には、数時間かかる場合があります。 毎日のバックアップ ポリシーでは、VM のバックアップの合計時間は 24 時間未満になります。
1. Azure Backup がここで有効化された後に Windows VM に加えられた変更は次のとおりです。
    - Microsoft Visual C++ 2013 redistributable (x64) - 12.0.40660 が VM にインストールされています。
    - ボリューム シャドウ コピー サービス (VSS) のスタートアップの種類が手動から自動に変更されています。
    - IaaSVmProvider Windows サービスが追加されます。

1. データ転送が完了すると、スナップショットが削除され、回復ポイントが作成されます。

![Azure 仮想マシンのバックアップ アーキテクチャ](../articles/backup/media/backup-azure-vms-introduction/vmbackup-architecture.png)
