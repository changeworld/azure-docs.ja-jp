---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/19/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 61990e785741799fcbcd4e6df965953bd9944f4d
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49476311"
---
## <a name="use-the-azure-portal-to-move-a-vm-to-a-different-subscription"></a>Azure portal を使用して、VM を他のサブスクリプションに移動する
Azure portal を使用して、VM とその関連リソースを他のサブスクリプションに移動できます。

1. [Azure Portal](https://portal.azure.com)を開きます。
2. **[参照]** > **[リソース グループ]** の順にクリックし、移動する VM を含むリソース グループを選択します。
3. リソース グループのページ上部で **[移動]** を選択し、**[別のサブスクリプションに移動する]** を選択します。 **[リソースの移動]** ページが開きます。
4. 移動するリソースを選択します。 ほとんどの場合、表示されているすべての関連リソースを移動する必要があります。
5. 移動する VM の **サブスクリプション** を選択します。
6. 既存の**リソース グループ**を選択するか、名前を入力して新しいリソース グループを作成します。
7. この操作が完了したら、新しいリソース ID が作成されること、および移動した後に VM でその新しい ID を使用する必要があることを理解したことを選択し、**[OK]** を選択します。

## <a name="use-the-azure-portal-to-move-a-vm-to-another-resource-group"></a>Azure portal を使用して、VM を別のリソース グループに移動する
Azure portal を使用して、VM とその関連リソースを別のリソース グループに移動できます。

1. [Azure Portal](https://portal.azure.com)を開きます。
2. **[参照]** > **[仮想マシン]** の順にクリックし、移動する VM を一覧から選択します。
3. VM のページで、リソース グループのラベルの隣にある **[変更]** を選択します。 **[リソースの移動]** ページが開きます。
4. 移動するリソースを選択します。 ほとんどの場合、表示されているすべての関連リソースを移動する必要があります。
5. 既存の**リソース グループ**を選択するか、名前を入力して新しいリソース グループを作成します。
6. この操作が完了したら、新しいリソース ID が作成されること、および移動した後に VM でその新しい ID を使用する必要があることを理解したことを選択し、**[OK]** を選択します。

