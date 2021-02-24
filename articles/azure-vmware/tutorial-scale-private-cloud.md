---
title: チュートリアル - プライベート クラウドをスケーリングする
description: このチュートリアルでは、Azure portal を使用して、Azure VMware Solution のプライベート クラウドをスケーリングします。
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: d49d973cc6d97280dc0c7ea6681f2602b871e1ba
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791241"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>チュートリアル:Azure VMware Solution のプライベート クラウドをスケーリングする

Azure VMware Solution のプライベート クラウド エクスペリエンスを最大限に活かすには、予定されたワークロードの要件を反映するようにクラスターとホストをスケーリングします。 実際のアプリケーション ワークロードに応じて、プライベート クラウドにあるクラスターとホストをスケーリングできます。 特定のサービスのパフォーマンスと可用性の制限については、ケース バイ ケースで対処する必要があります。 クラスターとホストの制限については、[プライベート クラウドの概念](concepts-private-clouds-clusters.md)に関する記事を参照してください。

このチュートリアルでは、次を行うために Azure portal を使用します。

> [!div class="checklist"]
> * 既存のプライベート クラウドにクラスターを追加する
> * 既存のクラスターにホストを追加する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するためのプライベート クラウド。 プライベート クラウドを作成していない場合は、[プライベート クラウドの作成に関するチュートリアル](tutorial-create-private-cloud.md)を使用して作成します。 必要な仮想ネットワークを設定するために、Azure で VMware プライベート クラウド用のネットワークを構成します。

## <a name="add-a-new-cluster"></a>新しいクラスターを追加する

1. 既存のプライベート クラウドの概要ページの **[管理]** で、 **[Scale private cloud]\(プライベート クラウドのスケーリング\)** を選択します。 次に、 **[+ Add a cluster]\(+ クラスターの追加\)** を選択します。

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="[Add cluster]\(クラスターの追加\) を選択する" border="true":::

1. **[クラスターの追加]** ページで、スライダーを使用してホストの数を選択します。 **[保存]** を選択します。

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="[Add cluster]\(クラスターの追加\) を選択する" border="true":::

   新しいクラスターのデプロイが開始されます。

## <a name="scale-a-cluster"></a>クラスターのスケーリング 

1. 既存のプライベート クラウドの概要ページで、 **[Scale private cloud]\(プライベート クラウドのスケーリング\)** を選択し、鉛筆アイコンを選択してクラスターを編集します。

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="[Add cluster]\(クラスターの追加\) を選択する" border="true":::

1. **[クラスターの編集]** ページで、スライダーを使用してホストの数を選択します。 **[保存]** を選択します。

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="[Add cluster]\(クラスターの追加\) を選択する" border="true":::

   クラスターのホストの追加が開始されます。

## <a name="next-steps"></a>次のステップ

もう 1 つ Azure VMware Solution のプライベート クラウドが必要な場合は、同じネットワーク前提条件と同じクラスターおよびホストの制限に従って、[別のプライベート クラウドを作成](tutorial-create-private-cloud.md)してください。

<!-- LINKS - external-->

<!-- LINKS - internal -->
