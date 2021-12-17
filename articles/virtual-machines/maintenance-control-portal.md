---
title: Azure portal を使用した Azure 仮想マシンのメンテナンス コントロール
description: メンテナンス コントロールと Azure portal を使用して、ご利用の Azure VM にメンテナンスを適用するタイミングを制御する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 6ac4be6b1c0654ea46bbbfe587a72839c3f2896b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216633"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>メンテナンス コントロールと Azure portal を使用して更新を制御する

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

メンテナンス コントロールを使用すると、分離された VM や Azure 専用ホストに更新プログラムを適用するタイミングをユーザーが決定できます。 このトピックでは、メンテナンス コントロール用の Azure portal オプションについて説明します。 メンテナンス コントロールを使用する利点、その制限、およびその他の管理オプションの詳細については、[メンテナンス コントロールを使用したプラットフォーム更新プログラムの管理](maintenance-control.md)に関する記事を参照してください。

## <a name="create-a-maintenance-configuration"></a>メンテナンス構成を作成する

1. Azure portal にサインインします。

1. **[メンテナンス構成]** を探します。
    
    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-search-bar.png" alt-text="[メンテナンス構成] を開く方法を示すスクリーンショット":::

1. **[追加]** をクリックします。

    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-2.png" alt-text="メンテナンス構成を追加する方法を示すスクリーンショット":::

1. [基本] タブで、サブスクリプションとリソース グループを選択し、構成の名前を指定します。リージョンを選択し、スコープとして *[ホスト]* を選択します。 **[次へ]** をクリックします。
    
    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics-tab.png" alt-text="[メンテナンス構成] の基本を示すスクリーンショット":::

1. [スケジュール] タブで、Azure でリソースに更新プログラムを適用するスケジュールされた期間を宣言します。 開始日、メンテナンス期間、繰り返しを設定します。 日程計画された期間を作成すると、更新プログラムを手動で適用する必要がなくなります。 **[次へ]** をクリックします。 

    > [!IMPORTANT]
    > メンテナンス **期間** は、"*2 時間*" 以上である必要があります。 メンテナンスの **繰り返し** は、少なくとも 35 日に 1 回繰り返すように設定する必要があります。 

    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-schedule-tab.png" alt-text="メンテナンス構成のスケジュールを示すスクリーンショット":::

1. [割り当て] タブで、今すぐリソースを割り当てるか、この手順をスキップし、メンテナンス構成の展開後にリソースを割り当てます。 **[次へ]** をクリックします。

1. タグと値を追加します。 **[次へ]** をクリックします。
    
    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags-tab.png" alt-text="メンテナンス構成にタグを追加する方法を示すスクリーンショット":::

1. 概要を確認します。 **Create** をクリックしてください。

1. デプロイが完了したら、 **[リソースに移動]** をクリックします。


## <a name="assign-the-configuration"></a>構成を割り当てる

メンテナンス構成の詳細ページで、[割り当て] をクリックし、 **[リソースの割り当て]** をクリックします。 

![リソースを割り当てる方法を示すスクリーンショット](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

メンテナンス構成を割り当てるリソースを選択し、 **[OK]** をクリックします。 **[種類]** 列には、リソースが分離された VM か Azure 専用ホストかが示されます。 構成を割り当てるには、VM が実行されている必要があります。 停止している VM に構成を割り当てようとすると、エラーが発生します。 

<!---Shantanu to add details about the error case--->

![リソースを選択する方法を示すスクリーンショット](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>構成を確認する

構成が正しく適用されたことを確認するか、 **[メンテナンス構成]** を使用して現在割り当てられているメンテナンス構成を確認します。 **[種類]** 列には、分離された VM と Azure 専用ホストのどちらに構成が割り当てられているかが示されます。 

![メンテナンス構成を確認する方法を示すスクリーンショット](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

また、プロパティ ページで特定の仮想マシンの構成を確認することもできます。 **[メンテナンス]** をクリックすると、その仮想マシンに割り当てられている構成を確認できます。

![ホストの [メンテナンス] を確認する方法を示すスクリーンショット](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>保留中の更新プログラムを確認する

また、メンテナンス構成の更新プログラムが保留中かどうかを確認する方法も 2 つあります。 **[メンテナンス構成]** の構成の詳細で、 **[割り当て]** をクリックし、 **[メンテナンスの状態]** を確認します。

![保留中の更新プログラムを確認する方法を示すスクリーンショット](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

**[仮想マシン]** または専用ホストのプロパティを使用して特定のホストを確認することもできます。 

![[メンテナンスの状態] が強調表示されているスクリーンショット。](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>更新プログラムの適用

必要に応じて、保留中の更新プログラムを適用できます。 VM または Azure Dedicated Host の詳細で、 **[メンテナンス]** をクリックし、 **[今すぐメンテナンスを適用する]** をクリックします。 更新呼び出しが適用されるまで、最大 2 時間かかる場合があります。

![保留中の更新プログラムの適用方法を示すスクリーンショット](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>更新プログラムの適用状態を確認する 

構成の更新プログラムの進行状況は、 **[メンテナンス構成]** または **[仮想マシン]** を使用して確認できます。 VM の詳細で、 **[メンテナンス]** をクリックします。 次の例では、 **[メンテナンスの状態]** に更新プログラムが "**保留中**" であると表示されています。

![保留中の更新プログラムの状態を確認する方法を示すスクリーンショット](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>メンテナンス構成を削除する

構成を削除するには、構成の詳細を開き、 **[削除]** をクリックします。

![構成を削除する方法を示すスクリーンショット。](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>次のステップ

詳細については、[メンテナンスと更新プログラム](maintenance-and-updates.md)に関するページを参照してください。
