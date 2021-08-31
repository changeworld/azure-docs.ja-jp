---
title: Azure Stack Edge Pro GPU デバイスで Edge リソース グループを管理する
description: Azure portal を介して、Azure Stack Edge Pro GPU、Azure Stack Edge Pro R、Azure Stack Edge Mini R デバイスの Edge リソース グループを管理する方法について説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: c77fb42579af8d1e4bad7e55746ad36d16c983b3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779637"
---
# <a name="manage-edge-resource-groups-on-azure-stack-edge-pro-gpu-devices"></a>Azure Stack Edge Pro GPU デバイスで Edge リソース グループを管理する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Edge リソース グループには、仮想マシンの作成時やデプロイ時に、ローカルの Azure Resource Manager を介してデバイス上に作成されるリソースが含まれます。 これらのローカル リソースには、仮想マシン、VM イメージ、ディスク、ネットワーク インターフェイス、Edge ストレージ アカウントなどの他のリソースの種類が含まれます。

この記事では、Azure Stack Edge Pro GPU デバイスの Edge リソース グループを表示および削除する方法について説明します。

## <a name="view-edge-resource-groups"></a>Edge リソース グループを表示する

次の手順で、現在のサブスクリプションの Edge リソース グループを表示します。

1. デバイスの **[仮想マシン]** に移動し、 **[リソース]** ペインに移動します。 **[Edge のリソース グループ]** を選択します。

    ![Azure Stack Edge デバイスの仮想マシンの [リソース] ビューのスクリーンショット。 [Edge のリソース グループ] タブが表示され、強調表示されています。](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-01.png)

    > [!NOTE]
    > デバイスに Azure Resource Manager 環境を設定した後、Azure Powershell で [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-6.1.0&preserve-view=true) を使用して、同じ一覧を取得することもできます。 詳細については、[Azure Resource Manager への接続](azure-stack-edge-gpu-connect-resource-manager.md)に関するページを参照してください。


## <a name="delete-an-edge-resource-group"></a>Edge リソース グループを削除する

次の手順で、使用しなくなった Edge リソース グループを削除します。

> [!NOTE]
> - リソース グループを削除するには、空にする必要があります。 
> - ASERG リソース グループは削除できません。 このリソース グループには、デバイスでコンピューティングを有効にしたときに自動的に作成される ASEVNET 仮想ネットワークが格納されています。

1. デバイスの **[仮想マシン]** に移動し、 **[リソース]** ペインに移動します。 **[Edge のリソース グループ]** を選択します。

    ![Azure Stack Edge デバイスの仮想マシンの [リソース] ビューを示すスクリーンショット。 [Edge のリソース グループ] タブが表示され、強調表示されています。](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-01.png)

1. 削除するリソース グループを選択します。 リソース グループの右端にある、削除アイコン (ごみ箱) を選択します。

   削除アイコンは、リソース グループにリソースが含まれていない場合にのみ表示されます。

    ![Azure Stack Edge デバイス上の VM の [リソース] ビュー、[Edge のリソース グループ] タブのスクリーンショット。 リソース グループの横にあるごみ箱アイコンは、削除できることを示しています。 アイコンが強調表示されています。](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-02.png)

1. Edge リソース グループの削除を確認するように求めるメッセージが表示されます。 この操作を元に戻すことはできません。 **[はい]** を選択します。

    ![VM の [リソース] ビューの [Edge のリソース グループ] タブのスクリーンショット。 強調表示されているごみ箱アイコンは、リソース グループを削除できることを示しています。](./media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-03.png)

    削除が完了すると、リソース グループは一覧から削除されます。

## <a name="next-steps"></a>次の手順

- Azure Stack Edge Pro GPU デバイスを管理する方法については、[ローカル Web UI を使用して Azure Stack Edge Pro GPU を管理する](azure-stack-edge-manage-access-power-connectivity-mode.md)方法に関する記事を参照してください。

- [デバイス上で Azure Resource Manager 環境を設定します](azure-stack-edge-gpu-connect-resource-manager.md)。