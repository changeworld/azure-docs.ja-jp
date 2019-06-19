---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 268467796e67caf2d20fedb44d83fd455a09b83e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181162"
---
**送信データ転送**: [送信データ転送](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure データ センターから送信されるデータ) では、帯域幅の使用量に対して課金されます。

**トランザクション**:Standard マネージド ディスクで実行されるトランザクションの数に対して課金されます。 標準 SSD では、スループットが 256 KiB 以下の I/O 操作は、それぞれ単一の I/O 操作とみなされます。 スループットが 256 KiB を超える I/O 操作は、サイズが 256 KiB の複数の I/O とみなされます。 標準 HDD では、各 IO 操作は、I/O サイズに関係なく、単一のトランザクションとみなされます。

トランザクション コストを含め、Managed Disks の価格の詳細については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks)」をご覧ください。

### <a name="ultra-ssd-vm-reservation-fee"></a>Ultra SSD VM 予約料金

Azure VM には、Ultra SSD と互換性があるかどうかを示す機能があります。 Ultra ディスク互換 VM では、コンピューティング VM インスタンスとブロック ストレージ スケール ユニット間に専用の帯域幅キャパシティが割り当てられてパフォーマンスが最適化され、待機時間が減少します。 このキャパシティを VM に追加すると、Ultra ディスクがアタッチされていない VM 上で Ultra ディスク キャパシティを有効にした場合にのみ、予約料金がかかります。 Ultra ディスク互換の VM に Ultra ディスクがアタッチされている場合、この料金は適用されません。 この料金は、VM 上にプロビジョニングされている vCPU ごとにかかります。

Ultra ディスクの価格について詳しくは、[Azure ディスクの価格](https://azure.microsoft.com/pricing/details/managed-disks/)に関するページをご覧ください。