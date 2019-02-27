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
ms.openlocfilehash: 0d771f03f9f71151ef25140148d4dd4daf3d46ec
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443378"
---
**送信データ転送**: [送信データ転送](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure データ センターから送信されるデータ) では、帯域幅の使用量に対して課金されます。

**トランザクション**:Standard マネージド ディスクで実行されるトランザクションの数に対して課金されます。 Standard HDD では、100,000 トランザクションあたり 0.0036 ドルが課金されます。 トランザクションには、ストレージに対する読み取り操作と書き込み操作の両方が含まれます。

Standard SSD では、256 KB の IO ユニット サイズが使用されます。 転送されるデータが 256 KB に満たない場合は、1 つの I/O ユニットとみなされます。 I/O のサイズが大きくなると、サイズが 256 KB の複数の I/O としてカウントされます。 たとえば、1,100 KB の I/O は 5 つの I/O ユニットとしてカウントされます。

Premium マネージド ディスクの場合、トランザクションのコストはかかりません。

Managed Disks の価格の詳細については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks)」をご覧ください。

### <a name="ultra-ssd-vm-reservation-fee"></a>Ultra SSD VM 予約料金

Azure VM には、Ultra SSD と互換性があるかどうかを示す機能があります。 Ultra ディスク互換 VM では、コンピューティング VM インスタンスとブロック ストレージ スケール ユニット間に専用の帯域幅キャパシティが割り当てられてパフォーマンスが最適化され、待機時間が減少します。 このキャパシティを VM に追加すると、Ultra ディスクがアタッチされていない VM 上で Ultra ディスク キャパシティを有効にした場合にのみ、予約料金がかかります。 Ultra ディスク互換の VM に Ultra ディスクがアタッチされている場合、この料金は適用されません。 この料金は、VM 上にプロビジョニングされている vCPU ごとにかかります。

Ultra ディスクの価格について詳しくは、[Azure ディスクの価格](https://azure.microsoft.com/pricing/details/managed-disks/)に関するページをご覧ください。