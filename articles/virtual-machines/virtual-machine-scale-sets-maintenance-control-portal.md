---
title: Azure portal を使用した、Azure 仮想マシン スケール セットの OS イメージ アップグレードのメンテナンス コントロール
description: メンテナンス コントロールと Azure portal を使用して、OS イメージのアップグレードを Azure 仮想マシン スケール セットに自動展開するタイミングを管理する方法を説明します。
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6537728feb2145520ee49457b00d9944d5967c9f
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073142"
---
# <a name="maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-azure-portal"></a>Azure portal を使用した、Azure 仮想マシン スケール セットの OS イメージ アップグレードのメンテナンス コントロール

メンテナンス コントロールを使用すると、ゲスト OS イメージの自動アップグレードを仮想マシン スケール セットに適用するタイミングを決定できます。 このトピックでは、メンテナンス コントロール用の Azure portal オプションについて説明します。 メンテナンス コントロールの使用方法の詳細については、「[Azure 仮想マシン スケール セットのメンテナンス コントロール](virtual-machine-scale-sets-maintenance-control.md)」を参照してください。


## <a name="create-a-maintenance-configuration"></a>メンテナンス構成を作成する

1. Azure portal にサインインします。

1. **[メンテナンス構成]** を探します。
    
    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-search-bar.png" alt-text="[メンテナンス構成] を開く方法を示すスクリーンショット":::

1. **[追加]** を選択します。

    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-add.png" alt-text="メンテナンス構成を追加する方法を示すスクリーンショット":::

1. [基本] タブで、サブスクリプションとリソース グループを選び、構成の名称を入力し、リージョンを選び、スコープに *[OS イメージのアップグレード]* を選びます。 **[次へ]** を選択します。
    
    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-basics-tab.png" alt-text="[メンテナンス構成] の基本を示すスクリーンショット":::

1. Azure でリソースを更新するスケジュールを、[スケジュール] タブで宣言します。 開始日、メンテナンス時間、頻度を設定します。 スケジュールを決めたら、それ以降手動で更新する必要はなくなります。 **[次へ]** を選択します。 

    > [!IMPORTANT]
    > メンテナンス スケジュールの **期間** は *5 時間* 以上に設定する必要があります。 メンテナンスの **頻度** は、少なくとも 1 日 1 回以上に設定する必要があります。 

    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-schedule-tab.png" alt-text="メンテナンス構成のスケジュールを示すスクリーンショット":::

1. [割り当て] タブでは、すぐにリソースを割り当てるか、これを一時的にスキップして、メンテナンス構成をデプロイした後で改めてリソースを割り当てます。 **[次へ]** を選択します。

1. タグと値を追加します。 **[次へ]** を選択します。
    
    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-tags-tab.png" alt-text="メンテナンス構成にタグを追加する方法を示すスクリーンショット":::

1. 概要を確認します。 **[作成]** を選択します。

1. デプロイが完了したら、**[リソースに移動]** を選択します。


## <a name="assign-the-configuration"></a>構成を割り当てる

メンテナンス構成の詳細ページで、 **[割り当て]** 、 **[リソースの割り当て]** を順に選びます。 

![リソースを割り当てる方法を示すスクリーンショット](media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-add-assignment.png)

メンテナンス構成を割り当てるリソースを選び、 **[Ok]** をクリックします。 **[種類]** 列には、リソースが分離された VM か Azure 専用ホストかが示されます。 構成を割り当てるには、VM が実行されている必要があります。 停止している VM に構成を割り当てようとすると、エラーが発生します。 


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure で実行している仮想マシンのメンテナンスと更新について知る](maintenance-and-updates.md)
