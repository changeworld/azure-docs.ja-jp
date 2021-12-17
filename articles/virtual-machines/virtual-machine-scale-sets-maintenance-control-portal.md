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
ms.openlocfilehash: 656d72663045b4604b9a94cbc686150925caca83
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2021
ms.locfileid: "112202042"
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

1. [スケジュール] タブで、Azure でリソースに更新プログラムを適用するスケジュールされた期間を宣言します。 開始日、メンテナンス時間、頻度を設定します。 スケジュールを決めたら、それ以降手動で更新する必要はなくなります。 **[次へ]** を選択します。 

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

メンテナンス構成を割り当てる仮想マシン スケール セットのリソースを選択し、 **[OK]** をクリックします。  


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure で実行されている仮想マシンのメンテナンスと更新について確認する](maintenance-and-updates.md)
