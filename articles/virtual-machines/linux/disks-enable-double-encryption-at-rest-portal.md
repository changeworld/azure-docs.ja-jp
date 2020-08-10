---
title: 保存時の二重暗号化を有効にする - Azure portal - マネージド ディスク
description: Azure portal を使用して、マネージド ディスク データの保存時の二重暗号化を有効にします。
author: roygara
ms.date: 07/23/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 7fefaa3b13b73db119da696738af66e84f8bdd63
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2020
ms.locfileid: "87135969"
---
# <a name="azure-portal---enable-double-encryption-at-rest-on-your-managed-disks"></a>Azure portal - マネージド ディスクで保存時の二重暗号化を有効にする

Azure Disk Storage は、マネージド ディスクに対する保存時の二重暗号化をサポートしています。 保存時の二重暗号化とその他のマネージド ディスクの暗号化の概念については、ディスクの暗号化の記事の[保存時の暗号化](disk-encryption.md#double-encryption-at-rest)に関するセクションを参照してください。

[!INCLUDE [virtual-machines-double-encryption-at-rest-portal](../../../includes/virtual-machines-disks-double-encryption-at-rest-portal.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Resource Manager テンプレートのサンプル](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [サーバー側の暗号化でカスタマー マネージド キーを有効にする - 例](disks-enable-customer-managed-keys-cli.md#examples)