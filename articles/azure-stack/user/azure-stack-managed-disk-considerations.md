---
title: Azure Stack のマネージド ディスクに関する相違点と考慮事項 | Microsoft Docs
description: Azure Stack でマネージド ディスクを操作する際の相違点と考慮事項について説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: jiahan
ms.openlocfilehash: ecbd555809799619d61a7ff33911b7f28dcb3005
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262712"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack Managed Disks: 相違点と考慮事項

この記事は、[Azure Stack の Managed Disks](azure-stack-manage-vm-disks.md) と [Azure の Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) の既知の相違点をまとめたものです。 Azure Stack と Azure の違いの概要については、「[主な考慮事項](azure-stack-considerations.md)」をご覧ください。

マネージド ディスクを使用すると、VM ディスクに関連付けられている[ストレージ アカウント](../azure-stack-manage-storage-accounts.md)を管理できるため、IaaS VM のディスク管理が簡素化されます。

> [!Note]  
> Azure Stack の Managed Disks は 1808 更新プログラムから使用できます。 1811 更新プログラムから Azure Stack ポータルを使用して仮想マシンを作成すると、既定で有効になります。
  

## <a name="cheat-sheet-managed-disk-differences"></a>チート シート:マネージド ディスクの相違点

| 機能 | Azure (グローバル) | Azure Stack |
| --- | --- | --- |
|保存データの暗号化 |Azure Storage Service Encryption (SSE)、Azure Disk Encryption (ADE)     |BitLocker 128 ビット AES 暗号化      |
|イメージ          | マネージド カスタム イメージのサポート |まだサポートされていません|
|バックアップ オプション |Azure Backup サービスのサポート |まだサポートされていません |
|ディザスター リカバリーのオプション |Azure Site Recovery のサポート |まだサポートされていません|
|ディスクの種類     |Premium SSD、Standard SSD (プレビュー)、および Standard HDD |Premium SSD、Standard HDD |
|Premium Disk  |完全にサポートされます |プロビジョニング可能ですがパフォーマンス制限や保証がありません  |
|Premium Disk の IOP  |ディスク サイズによって異なります  |ディスクあたりの 2300 IOP |
|Premium ディスクのスループット |ディスク サイズによって異なります |ディスクあたり 145 MB/秒 |
|ディスク サイズ  |Azure Premium ディスク:P4 (32 GiB) ～ P80 (32 TiB)<br>Azure Standard SSD ディスク:E10 (128 GiB) ～ E80 (32 TiB)<br>Azure Standard HDD ディスク:S4 (32 GiB) ～ S80 (32 TiB) |M4:32 GiB<br>M6:64 GiB<br>M10:128 GiB<br>M15:256 GiB<br>M20:512 GiB<br>M30:1,024 GiB |
|ディスクのスナップショット コピー|実行中の VM にアタッチされている Azure のマネージド ディスクのスナップショットはサポートされています|まだサポートされていません |
|ディスクのパフォーマンス分析 |サポートされる合計メトリックおよびディスクあたりのメトリック |まだサポートされていません |
|移行      |VM を再作成する必要なしに既存のアンマネージド Azure Resource Manager VM から移行するためのツールが提供されます  |まだサポートされていません |

> [!NOTE]  
> Azure Stack のマネージド ディスクの IOP とスループットは、プロビジョニングされた数値に代わりの上限数です。これは Azure Stack で実行されているハードウェアとワークロードによって影響を受ける可能性があります。

## <a name="metrics"></a>メトリック

ストレージ メトリックにも相違点があります。

- Azure Stack では、ストレージ メトリックのトランザクション データで、内部と外部のネットワーク帯域幅は区別されません。
- ストレージ メトリックの Azure Stack トランザクション データには、マウントされたディスクへの仮想マシンのアクセスは含まれません。

## <a name="api-versions"></a>API のバージョン

Azure Stack のマネージド ディスクは次の API バージョンをサポートしています。

- 2017-03-30

## <a name="configuration"></a>構成

1808 以降の更新プログラムを適用した後、Managed Disks を使用する前に次の構成を実行する必要があります。

- 1808 更新プログラムの前にサブスクリプションが作成された場合は、次の手順に従ってサブスクリプションを更新します。 そうでないと、このサブスクリプションに VM をデプロイする操作がエラー メッセージ "ディスク マネージャーでの内部エラーです" で失敗することがあります。
   1. テナント ポータルで、**[サブスクリプション]** に移動して、サブスクリプションを検索します。 **[リソース プロバイダー]** をクリックし、**[Microsoft.Compute]** をクリックした後、**[再登録]** をクリックします。
   2. 同じサブスクリプションで、**[アクセス制御 (IAM)]** に移動し、**[Azure Stack – マネージド ディスク]** がリストに含まれていることを確認します。
- マルチテナント環境を使用している場合は、[この記事](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)の次の手順に従って、各ゲスト ディレクトリを再構成するように (お客様の組織内またはサービス プロバイダーからの) クラウド オペレーターに依頼してください。 そうでないと、そのゲスト ディレクトリに関連付けられているサブスクリプションに VM をデプロイする操作がエラー メッセージ "ディスク マネージャーでの内部エラーです" で失敗することがあります。


## <a name="next-steps"></a>次の手順

- [Azure Stack 仮想マシンについての詳細](azure-stack-compute-overview.md)
