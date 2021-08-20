---
title: チュートリアル - プライベート クラウドでクラスターを拡大または縮小する
description: このチュートリアルでは、Azure portal を使用して、Azure VMware Solution のプライベート クラウドをスケーリングします。
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 79368483c7edd3ebf6811eeca1853b4bcb4ecec9
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113433996"
---
# <a name="tutorial-expand-or-shrink-clusters-in-a-private-cloud"></a>チュートリアル: プライベート クラウドでクラスターを拡大または縮小する

Azure VMware Solution のプライベート クラウド エクスペリエンスを最大限に活かすには、予定されたワークロードの要件を反映するようにクラスターとホストをスケーリングします。 実際のアプリケーション ワークロードに応じて、プライベート クラウドにあるクラスターとホストをスケーリングできます。 特定のサービスのパフォーマンスと可用性の制限については、ケース バイ ケースで対処する必要があります。 クラスターとホストの制限については、[プライベート クラウドの概念](concepts-private-clouds-clusters.md)に関する記事を参照してください。

このチュートリアルでは、次を行うために Azure portal を使用します。

> [!div class="checklist"]
> * 既存のプライベート クラウドにクラスターを追加する
> * 既存のクラスターにホストを追加する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、既存のプライベート クラウドが必要です。 プライベート クラウドを作成していない場合は、[プライベート クラウドの作成に関するチュートリアル](tutorial-create-private-cloud.md)を使用して作成します。 

## <a name="add-a-new-cluster"></a>新しいクラスターを追加する

1. 既存のプライベート クラウドの概要ページの **[管理]** で、 **[Scale private cloud]\(プライベート クラウドのスケーリング\)** を選択します。 次に、 **[+ Add a cluster]\(+ クラスターの追加\)** を選択します。

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Azure VMware Solution プライベート クラウドにクラスターを追加する方法を示すスクリーンショット。" border="true":::

1. **[クラスターの追加]** ページで、スライダーを使用してホストの数を選択します。 **[保存]** を選択します。

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="新しいクラスターの構成方法を示すスクリーンショット。" border="true":::

   新しいクラスターのデプロイが開始されます。

## <a name="scale-a-cluster"></a>クラスターのスケーリング 

1. 既存のプライベート クラウドの概要ページで、 **[Scale private cloud]\(プライベート クラウドのスケーリング\)** を選択し、鉛筆アイコンを選択してクラスターを編集します。

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="既存のクラスターを編集する場所を示すスクリーンショット。" border="true":::

1. **[クラスターの編集]** ページで、スライダーを使用してホストの数を選択します。 **[保存]** を選択します。

   クラスターへのホストの追加が開始されます。

## <a name="next-steps"></a>次のステップ

もう 1 つ Azure VMware Solution のプライベート クラウドが必要な場合は、同じネットワーク前提条件と同じクラスターおよびホストの制限に従って、[別のプライベート クラウドを作成](tutorial-create-private-cloud.md)してください。

<!-- LINKS - external-->

<!-- LINKS - internal -->
