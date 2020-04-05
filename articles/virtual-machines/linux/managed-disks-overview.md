---
title: Disk Storage の概要
description: VM を使用するときにストレージ アカウントを管理する Azure マネージド ディスクの概要
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 12/02/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: edcde9c5192791e88da57d158bab3e79b171bfc8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "78944760"
---
# <a name="introduction-to-azure-managed-disks"></a>Azure マネージド ディスクの概要

Azure マネージド ディスクは、Azure によって管理されて Azure Virtual Machines で使用されるブロックレベルの記憶域ボリュームです。 マネージド ディスクは、オンプレミス サーバーの物理ディスクと似ていますが、仮想化されています。 マネージド ディスクを使用した場合、ユーザーがすべきことは、サイズと種類を指定してディスクをプロビジョニングするだけです。 ディスクのプロビジョニング後、残りの作業は Azure によって行われます。

使用できるディスクの種類は、Ultra ディスク、Premium ソリッド ステート ドライブ (SSD)、Standard SSD、Standard ハード ディスク ドライブ (HDD) です。 各ディスクの種類については、「[IaaS VM 用のディスクの種類の選択](disks-types.md)」を参照してください。

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [IaaS VM 用のディスクの種類の選択](disks-types.md)