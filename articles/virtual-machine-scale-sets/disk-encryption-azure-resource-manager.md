---
title: Azure Resource Manager テンプレートで仮想マシン スケール セットを作成して暗号化する
description: このクイックスタートでは、Azure Resource Manager テンプレートを使用して仮想マシン スケール セットを作成し、暗号化する方法について説明します。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 058ba9a3b2593ec6f635485771d5bbe140beb510
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698990"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Azure Resource Manager を使用して仮想マシン スケール セットを暗号化する

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: ユニフォーム スケール セット

Linux 仮想マシン スケール セットの暗号化と暗号化解除は、Azure Resource Manager テンプレートを使用して実行できます。

## <a name="deploying-templates"></a>テンプレートをデプロイする

まず、シナリオに合ったテンプレートを選択します。

- [実行中の Linux 仮想マシン スケール セットでディスク暗号化を有効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-vmss-linux)

- [実行中の Windows 仮想マシン スケール セットでディスク暗号化を有効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-vmss-windows)

  - [ジャンプボックスで Linux VM の仮想マシン スケール セットをデプロイし、Linux 仮想マシン スケール セットで暗号化を有効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-vmss-linux-jumpbox)

  - [ジャンプボックスで Windows VM の仮想マシン スケール セットをデプロイし、Windows 仮想マシン スケール セットで暗号化を有効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-vmss-windows-jumpbox)

- [実行中の Linux 仮想マシン スケール セットでディスク暗号化を無効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-vmss-linux)

- [実行中の Windows 仮想マシン スケール セットでディスク暗号化を無効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-vmss-windows)

その後、次の手順に従います。

1. **[Azure へのデプロイ]** をクリックします。
2. 必須のフィールドに入力し、使用条件に同意します。
3. **[購入]** をクリックしてテンプレートをデプロイします。

## <a name="next-steps"></a>次のステップ

- [仮想マシン スケール セットの Azure Disk Encryption](disk-encryption-overview.md)
- [Azure CLI を使用して仮想マシン スケール セットを暗号化する](disk-encryption-cli.md)
- [Azure PowerShell を使用して仮想マシン スケール セットを暗号化する](disk-encryption-powershell.md)
- [Azure Disk Encryption のキー コンテナーを作成して構成する](disk-encryption-key-vault.md)
- [Azure Disk Encryption を仮想マシン スケール セットの拡張機能のシーケンス処理と共に使用する](disk-encryption-extension-sequencing.md)
