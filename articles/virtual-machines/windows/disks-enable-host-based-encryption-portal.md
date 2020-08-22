---
title: ホストでの暗号化を使用してエンドツーエンドの暗号化を有効にする - Azure portal - マネージド ディスク
description: ホストでの暗号化を使用して、Azure マネージド ディスク (Azure portal) でエンドツーエンドの暗号化を有効にします。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/23/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e969fe34e7e7723218586f24807ad70944ba5716
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171354"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-portal"></a>ホストでの暗号化を使用してエンドツーエンドの暗号化を有効にする - Azure portal

ホストでの暗号化を有効にすると、VM ホスト上の格納データは、保存時に暗号化され、暗号化された状態でストレージ サービスに送られます。 ホストでの暗号化とその他のマネージド ディスクの暗号化の概要については、「[ホストでの暗号化 - VM データのエンドツーエンドの暗号化](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)」を参照してください。

[!INCLUDE [virtual-machines-disks-encryption-at-host-portal](../../../includes/virtual-machines-disks-encryption-at-host-portal.md)]

## <a name="next-steps"></a>次のステップ

[Azure Resource Manager テンプレートのサンプル](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)