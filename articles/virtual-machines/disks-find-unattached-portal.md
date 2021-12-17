---
title: 接続されていない Azure ディスクを特定する - Azure portal
description: Azure portal を使用して、接続されていない Azure マネージド ディスクとアンマネージド ディスク (VHD/ページ BLOB) を見つけて削除する方法。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0855516610c9712d5f8db597d1de6e53f01ac546
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301307"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks---azure-portal"></a>接続されていない Azure マネージド ディスクとアンマネージド ディスクを見つけて削除する - Azure portal

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

Azure で仮想マシン (VM) を削除するとき、既定では、その VM に接続されているディスクはいずれも削除されません。 これは、意図せず VM を削除したことによるデータ損失を防ぐのに役立ちます。 VM が削除された後、接続されていないディスクに対する料金の支払いが続きます。 この記事では、接続されていないディスクを Azure portal で見つけて削除し、不要なコストを削減する方法を示します。 削除は永続的です。ディスクを削除すると、データを回復できなくなります。

## <a name="managed-disks-find-and-delete-unattached-disks"></a>マネージド ディスク:接続されていないディスクの検索と削除

以下では、接続されていないマネージド ディスクがあり、そのディスク上のデータがもう不要ない場合に、Azure portal でそれらを探す方法を説明します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. **[ディスク]** を探して選択します。

    **[ディスク]** ブレードに、すべてのディスクの一覧が表示されます。

1. 削除するディスクを選択すると、ディスクのブレードが開きます。
1. ディスクのブレードで、ディスクの状態が非接続になっていることを確認し、 **[削除]** を選択します。

    :::image type="content" source="media/disks-find-unattached-portal/delete-managed-disk-unattached.png" alt-text="個々のマネージド ディスク ブレードのスクリーンショット。ディスクが接続されていない場合、このブレードにディスクの状態が非接続と表示されます。データを保持する必要がなくなった場合は、このディスクを削除できます":::

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>アンマネージド ディスク:接続されていないディスクの検索と削除

非管理対象ディスクは、[Azure Storage アカウント](../storage/common/storage-account-overview.md)で[ページ BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) として格納された VHD ファイルです。

VM に接続されていないアンマネージド ディスクがあり、そのディスク上のデータが不要であり、ディスクを削除したい場合は、次に説明するプロセスで、Azure portal からそれを行うことができます。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. **[ディスク (クラシック)]** を探して選択します。

    すべてのアンマネージド ディスクの一覧が表示されます。 **[接続先]** 列が " **-** " になっているディスクは、接続されていないディスクです。

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-attached-to.png" alt-text="アンマネージド ディスク ブレードのスクリーンショット。このブレードで [接続先] 列が - になっているディスクは接続されていません。":::

1. 接続されていない削除するディスクを選択すると、ディスクのブレードが表示されます。

1. ディスクのブレードでは、 **[接続先]** がまだ " **-** " になっていることで、ディスクが接続されていないことを確認できます。

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-select-blade.png" alt-text="個々のアンマネージド ディスク ブレードのスクリーンショット。接続されていない場合、[接続先] の値が - になります。このディスクのデータが必要ない場合は、ディスクを削除することができます。":::

1. **[削除]** を選択します。

    :::image type="content" source="media/disks-find-unattached-portal/delete-unmanaged-disk-unattached.png" alt-text="[削除] が強調して示されている、個々のアンマネージド ディスク ブレードのスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

接続されていないストレージ アカウントを自動的に検索して削除する方法については、[CLI](linux/find-unattached-disks.md) または [PowerShell](windows/find-unattached-disks.md) の記事を参照してください。

詳細については、「[ストレージ アカウントを削除する](../storage/common/storage-account-create.md#delete-a-storage-account)」および [PowerShell を使用した孤立したディスクの特定](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell)に関するページを参照してください。
