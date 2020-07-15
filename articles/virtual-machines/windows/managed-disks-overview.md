---
title: Azure Disk Storage の概要
description: Azure VM を使用するときにストレージ アカウントを管理する Azure マネージド ディスクの概要
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 13237011e160d0190475eadb3f9d20f0d731df60
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146617"
---
# <a name="introduction-to-azure-managed-disks"></a>Azure マネージド ディスクの概要

Azure マネージド ディスクは、Azure によって管理されて Azure Virtual Machines で使用されるブロックレベルの記憶域ボリュームです。 マネージド ディスクは、オンプレミス サーバーの物理ディスクと似ていますが、仮想化されています。 マネージド ディスクを使用した場合、ユーザーがすべきことは、サイズと種類を指定してディスクをプロビジョニングするだけです。 ディスクのプロビジョニング後、残りの作業は Azure によって行われます。

使用できるディスクの種類は、Ultra ディスク、Premium ソリッド ステート ドライブ (SSD)、Standard SSD、Standard ハード ディスク ドライブ (HDD) です。 各ディスクの種類については、「[IaaS VM 用のディスクの種類の選択](disks-types.md)」を参照してください。

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [IaaS VM 用のディスクの種類の選択](disks-types.md)
