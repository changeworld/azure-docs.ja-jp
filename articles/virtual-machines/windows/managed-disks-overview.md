---
title: Azure Disk Storage の概要
description: Azure VM を使用するときにストレージ アカウントを管理する Azure マネージド ディスクの概要
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4b3a66ce00582a3ef05e77f65acdc46fbde8ce72
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82148080"
---
# <a name="introduction-to-azure-managed-disks"></a>Azure マネージド ディスクの概要

Azure マネージド ディスクは、Azure によって管理されて Azure Virtual Machines で使用されるブロックレベルの記憶域ボリュームです。 マネージド ディスクは、オンプレミス サーバーの物理ディスクと似ていますが、仮想化されています。 マネージド ディスクを使用した場合、ユーザーがすべきことは、サイズと種類を指定してディスクをプロビジョニングするだけです。 ディスクのプロビジョニング後、残りの作業は Azure によって行われます。

使用できるディスクの種類は、Ultra ディスク、Premium ソリッド ステート ドライブ (SSD)、Standard SSD、Standard ハード ディスク ドライブ (HDD) です。 各ディスクの種類については、「[IaaS VM 用のディスクの種類の選択](disks-types.md)」を参照してください。

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [IaaS VM 用のディスクの種類の選択](disks-types.md)
