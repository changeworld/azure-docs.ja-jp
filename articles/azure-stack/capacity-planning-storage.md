---
title: Azure Stack のためのストレージ容量の計画 | Microsoft Docs
description: Azure Stack デプロイのためのストレージ容量の計画について説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.openlocfilehash: 9ea46860817d60c2ffbde68c0fc5ae6f6ca14877
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2018
ms.locfileid: "46369761"
---
# <a name="azure-stack-storage-capacity-planning"></a>Azure Stack ストレージ容量の計画
次のセクションでは、ソリューションのストレージ ニーズに合わせて計画する場合に役立つ Azure Stack ストレージ容量の計画に関する情報を提供します。

## <a name="uses-and-organization-of-storage-capacity"></a>ストレージ容量の使用と構成
Azure Stack のハイパーコンバージド構成では、物理ストレージ デバイスの共有を可能にします。 使用可能なストレージの 3 つの主な部分は、テナント仮想マシンのインフラストラクチャの一時ストレージ、および Azure Consistent Storage (ACS) サービスの BLOB、テーブル、キューをバックアップするストレージの間にあります。

## <a name="spaces-direct-cache-and-capacity-tiers"></a>スペース ダイレクトのキャッシュ層と容量層
オペレーティング システム、ローカルのログ記録、ダンプ、およびその他の一時インフラストラクチャ ストレージのニーズに合わせて使用されるストレージ容量があります。 このローカル ストレージ容量は、記憶域スペース ダイレクト構成の管理下にあるストレージ デバイスから分離されます (デバイスと容量は別個)。 その他のストレージ デバイスは、スケール ユニット内のサーバー数に関係なく、ストレージ容量の単一のプールに配置されます。 これらのデバイスには、キャッシュおよび容量という 2 つの種類があります。  キャッシュ デバイスは、その名のとおり、キャッシュです。 スペース ダイレクトでは、キャッシュの書き戻しと読み取りのためにこれらのデバイスを消費します。 これらのキャッシュ デバイスの容量は、使用されますが、フォーマット済み仮想ディスクのフォーマット済み "可視" 容量にはコミットされません。 容量デバイスはこの目的に使用され、記憶域スペースで管理されるデータの "ホームの場所" が提供されます。

すべてのストレージ容量は、Azure Stack インフラストラクチャによって直接割り当てられ、管理されます。 オペレーターは構成や割り当てについて選択する必要があります。また、容量拡張に関する選択に対応する必要があります。 これらの設計上の決定は、ソリューション要件に合わせて行われており、初期インストール/デプロイ時または容量拡張時に自動化されます。 回復性、リビルドのための予約容量、およびその他の詳細は、設計の一部として考慮されています。 

オペレーターは、オール フラッシュ構成またはハイブリッド ストレージ構成を選択することができます。

![Azure のストレージ容量の計画](media/azure-stack-capacity-planning/storage.png)

オール フラッシュ構成では、キャッシュは NVMe となり、容量に対して SATA SSD または NVMe を選択することができます。 ハイブリッド構成では、キャッシュは NVMe と SATA SSD から選ぶことができますが、容量は HDD となります。

記憶域スペース ダイレクトと Azure Stack ストレージ構成の簡単な概要を以下に示します。
- スケール ユニットごとに記憶域スペース プールが 1 つ (ストレージ デバイスはすべて単一プール内で構成されます)
- 仮想ディスクは最適なパフォーマンスと回復性のために 3 つのコピー ミラーとして作成されます。
- 各仮想ディスクは ReFS ファイル システムとしてフォーマットされます。
- 仮想ディスクの容量が計算され、割り当てられます。このとき、プール内の 1 つの容量デバイスのデータ容量が未割り当てのままとなります。 つまり、サーバーごとに 1 つの容量ドライブがあることになります。
- 各 ReFS ファイル システムでは、保存データの暗号化のために BitLocker が有効になります。 

自動的に作成される仮想ディスクとその容量は次のとおりです。




|Name|容量の計算|説明|
|-----|-----|-----|
|ローカル/ブート デバイス|最小 340 GB<sup>1</sup>|オペレーティング システム イメージと "ローカル" インフラストラクチャ VM に対する個々のサーバー ストレージ|
|インフラストラクチャ|3.5 TB|すべての Azure Stack インフラストラクチャの使用量|
|VmTemp|以下を参照<sup>2</sup>|テナント仮想マシンには一時ディスクが接続されており、そのデータはこれらの仮想ディスクに格納されます。|
|ACS|下記参照 <sup>3</sup>|BLOB、テーブル、キューを提供するための Azure Consistent Storage 容量|

<sup>1</sup> Azure Stack ソリューション パートナーの必要な最小ストレージ容量。

<sup>2</sup> テナント仮想マシンの一時ディスクで使用される仮想ディスク サイズは、サーバーの物理メモリの比率として計算されます。 Azure IaaS VM サイズに関する以下の表に示すとおり、一時ディスクは、仮想マシンに割り当てられた物理メモリの比率となります。 Azure Stack の "一時ディスク" ストレージについては、ほとんどのユース ケースが取り込まれるように割り当てられますが、すべての一時ディスク ストレージのニーズを満たせない場合があります。 比率を選択すると、一時ディスク容量のみに対するソリューションのストレージ容量の大部分が消費されないときに、一時ストレージを利用できるようになります。 スケール ユニット内のサーバーごとに、一時ストレージ ディスクが 1 つ作成されます。 一時ストレージの容量は、スケール ユニットのストレージ プールで利用可能なストレージ容量全体の 10% を超えます。 計算は次の例のようになります。

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> ACS で使用するために作成される仮想ディスクについては、残りの容量を単に除算します。 前述のとおり、すべての仮想ディスクは 3 方向ミラーであり、サーバーごとに 1 つの容量ドライブ相当の容量が未割り当てとなります。 上に列挙されているさまざまな仮想ディスクが最初に割り当てられてから、残りの容量が ACS 仮想ディスクで使用されます。

## <a name="next-steps"></a>次の手順
[Azure Stack の容量計画スプレッドシートについて学習する](capacity-planning-spreadsheet.md)
