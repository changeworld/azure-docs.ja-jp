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
ms.date: 09/05/2018
ms.author: sethm
ms.reviewer: jiahan
ms.openlocfilehash: 2870bf8911c48ac4cbb442278f172b37a474152b
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078055"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack のマネージド ディスク: 相違点と考慮事項
この記事は、Azure Stack のマネージド ディスクと Azure のマネージド ディスクの既知の相違点をまとめたものです。 Azure Stack と Azure の違いの概要については、「[主な考慮事項](azure-stack-considerations.md)」をご覧ください。

マネージド ディスクを使用すると、VM ディスクに関連付けられている[ストレージ アカウント](/azure/azure-stack/azure-stack-manage-storage-accounts)を管理できるため、IaaS VM のディスク管理が簡素化されます。
  

## <a name="cheat-sheet-managed-disk-differences"></a>チート シート: マネージド ディスクの相違点

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
|ディスクの最大サイズ  |4 TB       |1 TB (テラバイト)       |
|ディスクのパフォーマンス分析 |サポートされる合計メトリックおよびディスクあたりのメトリック |まだサポートされていません |
|移行      |VM を再作成せずに既存の非管理対象の Azure Resource Manager VM から移行するためのツールを提供します  |まだサポートされていません |


## <a name="metrics"></a>メトリック
ストレージ メトリックにも相違点があります。
- Azure Stack では、ストレージ メトリックのトランザクション データは、内部と外部のネットワーク帯域幅を区別しません。
- ストレージ メトリックの Azure Stack トランザクション データには、マウントされたディスクへの仮想マシンのアクセスは含まれません。


## <a name="api-versions"></a>API のバージョン
Azure Stack のマネージド ディスクは次の API バージョンをサポートしています。
- 2017-03-30


## <a name="next-steps"></a>次の手順
[Azure Stack 仮想マシンについての詳細](azure-stack-compute-overview.md)
