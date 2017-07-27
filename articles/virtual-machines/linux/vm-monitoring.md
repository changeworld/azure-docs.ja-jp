---
title: "Azure VM の監視を有効または無効にする"
description: "Azure VM の監視を有効または無効にする方法を説明します"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: kmouss
manager: timlt
editor: 
ms.assetid: 6ce366d2-bd4c-4fef-a8f5-a3ae2374abcc
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/08/2016
ms.author: kmouss
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 9b2fe579113d6ca6bfd27d82eb9d4657657d44ff
ms.contentlocale: ja-jp
ms.lasthandoff: 05/25/2017

---
# <a name="enable-or-disable-azure-vm-monitoring"></a>Azure VM の監視を有効または無効にする

このセクションでは、Azure で動作する仮想マシンの監視を有効または無効にする方法について説明します。 ポータルまたは Mac、Linux、Windows (Azure CLI) の Azure コマンドライン インターフェイスを使用して、監視を有効または無効にできます。

> [!IMPORTANT]
> このドキュメントでは、非推奨の Linux Diagnostic Extension バージョン 2.3 について説明します。 バージョン 2.3 は 2018 年 6 月 30 日までサポートされる予定です。
>
> 代わりに Linux Diagnostic Extension バージョン 3.0 をご利用いただけます。 詳細については、[こちらのドキュメント](./diagnostic-extension.md)をご覧ください。

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Azure Portal で監視を有効または無効にする

Azure VM の監視を有効にできます。インスタンスに関するデータが 1 分間隔で提供されます  (ストレージの変更が適用されます)。 VM の詳細な診断データを、ポータル グラフまたは API で利用できます。 Azure Portal では、既定でホスト ベースの監視が有効になっており、限定されたメトリックのセットを監視します。 VM が実行中でも停止状態でも、VM 内からのメトリックの監視を有効にできます。

* Azure Portal ([https://portal.azure.com](https://portal.azure.com)) を開きます。
* 左側のナビゲーションで、[仮想マシン] をクリックします。
* 仮想マシンのリストで、実行中または停止中のインスタンスを選択します。 [仮想マシン] ブレードが開きます。
* [すべての設定] をクリックします。
* [診断] をクリックします。
* ステータスをオンまたはオフに変更します。 このブレードでは、仮想マシンに対して有効にする監視の詳細レベルを選択することもできます。

![Azure Portal で監視を有効または無効にします。][1]

## <a name="enable--disable-monitoring-with-azure-cli"></a>Azure CLI で監視を有効/無効にする

Azure VM の監視を有効にするには:

* ファイルを作成します (ファイル名は PrivateConfig.json などにします)。

```json
{
        "storageAccountName":"the storage account to receive data",
        "storageAccountKey":"the key of the account"
}
```

* Azure CLI を使用して拡張機能を有効にします。

```azurecli
azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.3 --private-config-path PrivateConfig.json
```

詳細については、「[Using Linux Diagnostic Extension to Monitor Linux VM’s performance and diagnostic data (Linux Diagnostic Extension を使用して Linux VM のパフォーマンスと診断データを監視する)](classic/diagnostic-extension-v2.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」をご覧ください。

<!--Image references-->
[1]: ./media/vm-monitoring/portal-enable-disable.png

