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
ms.openlocfilehash: 4bad339ca704f14f57c120db5e731d0dbfeb8d59
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793443"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack のマネージド ディスク:相違点と考慮事項
この記事は、Azure Stack のマネージド ディスクと Azure のマネージド ディスクの既知の相違点をまとめたものです。 Azure Stack と Azure の違いの概要については、「[主な考慮事項](azure-stack-considerations.md)」をご覧ください。

マネージド ディスクを使用すると、VM ディスクに関連付けられている[ストレージ アカウント](/azure/azure-stack/azure-stack-manage-storage-accounts)を管理できるため、IaaS VM のディスク管理が簡素化されます。

> [!Note]  
> Azure Stack のマネージド ディスクは 1808 から実行できます。
  

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
|移行      |VM を再作成せずに既存の非管理対象の Azure Resource Manager VM から移行するためのツールを提供します  |まだサポートされていません |

> [!Note]  
> Azure Stack のマネージド ディスクの IOP とスループットは、プロビジョニングされた数値に代わりの上限数です。これは Azure Stack で実行されているハードウェアとワークロードによって影響を受ける可能性があります。


## <a name="metrics"></a>メトリック
ストレージ メトリックにも相違点があります。
- Azure Stack では、ストレージ メトリックのトランザクション データは、内部と外部のネットワーク帯域幅を区別しません。
- ストレージ メトリックの Azure Stack トランザクション データには、マウントされたディスクへの仮想マシンのアクセスは含まれません。


## <a name="api-versions"></a>API のバージョン
Azure Stack のマネージド ディスクは次の API バージョンをサポートしています。
- 2017-03-30

## <a name="known-issues"></a>既知の問題
更新プログラム 1809 の適用後、Managed Disks を使用した VM をデプロイするときに、次の問題が発生する可能性があります。

   - 1808 更新の前にサブスクリプションが作成された場合、Managed Disks を使用した VM をデプロイすると、内部エラー メッセージが出て失敗することがあります。 このエラーを解決するには、サブスクリプションごとに次の手順に従ってください。
      1. テナント ポータルで、**[サブスクリプション]** に移動して、サブスクリプションを検索します。 **[リソース プロバイダー]** をクリックし、**[Microsoft.Compute]** をクリックした後、**[再登録]** をクリックします。
      2. 同じサブスクリプションで、**[アクセス制御 (IAM)]** に移動し、**[Azure Stack – マネージド ディスク]** がリストに含まれていることを確認します。
   - マルチテナント環境を構成した場合、ゲスト ディレクトリに関連付けられているサブスクリプションで VM をデプロイすると、内部エラー メッセージが出て失敗することがあります。 このエラーを解決するには、[この記事](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)にある手順に従って、各ゲスト ディレクトリを構成します。


## <a name="next-steps"></a>次の手順
[Azure Stack 仮想マシンについての詳細](azure-stack-compute-overview.md)
