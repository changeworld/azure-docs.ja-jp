---
title: Azure portal を使用してフレキシブル スケール セットに仮想マシンを作成する
description: Azure portal のフレキシブル オーケストレーション モードで仮想マシン スケール セットを作成する方法について説明します。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 9804b8cd773eec6df8a8b7a5b06e61ee110987b3
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130163556"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-azure-portal"></a>Azure portal を使用してフレキシブル スケール セットに仮想マシンを作成する

**適用対象:** :heavy_check_mark: フレキシブル スケール セット

この記事では、Azure portal を使用して、フレキシブル オーケストレーション モードで仮想マシン スケール セットを作成する方法について説明します。 フレキシブル スケール セットの詳細については、[仮想マシン スケール セットのフレキシブル オーケストレーション モード](flexible-virtual-machine-scale-sets.md)に関するページを参照してください。 


> [!CAUTION]
> オーケストレーション モードは、スケール セットを作成するときに定義され、後で変更または更新することはできません。


## <a name="get-started-with-flexible-orchestration-mode"></a>フレキシブル オーケストレーション モードの開始

### <a name="create-a-virtual-machine-scale-set-in-flexible-orchestration-mode-through-the-azure-portal"></a>Azure portal を通じてフレキシブル オーケストレーション モードで仮想マシン スケール セットを作成します。

1. Azure portal (https://portal.azure.com ) にログインします。
1. 検索バーで、 **[仮想マシン スケール セット]** を検索して選択します。
1. **[仮想マシン スケール セット]** ページで **[作成]** を選択します。
1. **[仮想マシン スケール セットの作成]** ページで、 **[オーケストレーション]** セクションを表示します。
1. **[オーケストレーション モード]** で **[Flexible]\(フレキシブル\)** オプションを選択します。
1. **[障害ドメイン数]** を設定します。
1. スケール セットの作成を完了します。 スケール セットを作成する方法の詳細については、[Azure portal でのスケール セットの作成](../virtual-machine-scale-sets/quick-create-portal.md#create-virtual-machine-scale-set)に関するページを参照してください。


### <a name="optional-add-a-virtual-machine-to-the-scale-set-in-flexible-orchestration-mode"></a>(省略可能) フレキシブル オーケストレーション モードのスケール セットに仮想マシンを追加します。

1. 検索バーで **[仮想マシン]** を検索して選択します。
1. **[仮想マシン]** ページで **[追加]** を選択します。
1. **[基本]** タブで **[インスタンスの詳細]** セクションを表示します。
1. **[可用性オプション]** でスケール セットを選択して、フレキシブル オーケストレーション モードのスケール セットに VM を追加します。 同じリージョン、ゾーン、リソース グループ内のスケール セットに仮想マシンを追加できます。
1. **[ネットワーク]** タブにアクセスし、送信接続を明示的に定義します。

    > [!IMPORTANT]
    > フレキシブル オーケストレーションを使用した仮想マシン スケール セットには、明示的に定義された送信接続が必要です。 詳細については、[明示的な送信ネットワーク接続](flexible-virtual-machine-scale-sets-migration-resources.md#explicit-network-outbound-connectivity-required)に関するページを参照してください。

1. 仮想マシンの作成を完了します。


## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Azure CLI でフレキシブル スケールを作成する方法について説明します。](flexible-virtual-machine-scale-sets-cli.md)