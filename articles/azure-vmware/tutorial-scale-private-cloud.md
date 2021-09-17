---
title: チュートリアル - プライベート クラウドでクラスターをスケーリングする
description: このチュートリアルでは、Azure portal を使用して、Azure VMware Solution のプライベート クラウドをスケーリングします。
ms.topic: tutorial
ms.date: 08/03/2021
ms.openlocfilehash: 47bc7d12a025104df48d5a55af4abf342759696e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638782"
---
# <a name="tutorial-scale-clusters-in-a-private-cloud"></a>チュートリアル: プライベート クラウドでクラスターをスケーリングする

Azure VMware Solution のプライベート クラウド エクスペリエンスを最大限に活かすには、予定されたワークロードの要件を反映するようにクラスターとホストをスケーリングします。 実際のアプリケーション ワークロードに応じて、プライベート クラウドにあるクラスターとホストをスケーリングできます。  特定のサービスのパフォーマンスと可用性の制限については、ケース バイ ケースで対処する必要があります。 

[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

このチュートリアルでは、次を行うために Azure portal を使用します。

> [!div class="checklist"]
> * 既存のプライベート クラウドにクラスターを追加する
> * 既存のクラスターにホストを追加する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、既存のプライベート クラウドが必要です。 プライベート クラウドを作成していない場合は、[プライベート クラウドの作成に関するチュートリアル](tutorial-create-private-cloud.md)に従って作成します。 

## <a name="add-a-new-cluster"></a>新しいクラスターを追加する

1. Azure VMware Solution プライベート クラウドで、 **[管理]** の下にある **[クラスター]**  >  **[クラスターの追加]** を選択します。

   :::image type="content" source="media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Azure VMware Solution プライベート クラウドにクラスターを追加する方法を示すスクリーンショット。" border="true":::

1. スライダーを使用してホストの数を選択し、 **[保存]** を選択します。

   :::image type="content" source="media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="新しいクラスターの構成方法を示すスクリーンショット。" border="true":::

   新しいクラスターのデプロイが開始されます。

## <a name="scale-a-cluster"></a>クラスターのスケーリング 

1. Azure VMware Solution プライベート クラウドで、 **[管理]** の下にある **[クラスター]** を選択します。

1. スケーリングするクラスターを選択し、 **[More]\(詳細\)** (...) を選択して **[編集]** を選択します。

   :::image type="content" source="media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="既存のクラスターを編集する場所を示すスクリーンショット。" border="true":::

1. スライダーを使用してホストの数を選択し、 **[保存]** を選択します。

   クラスターへのホストの追加が開始されます。

## <a name="next-steps"></a>次のステップ

もう 1 つ Azure VMware Solution のプライベート クラウドが必要な場合は、同じネットワーク前提条件と同じクラスターおよびホストの制限に従って、[別のプライベート クラウドを作成](tutorial-create-private-cloud.md)してください。

<!-- LINKS - external-->

<!-- LINKS - internal -->
