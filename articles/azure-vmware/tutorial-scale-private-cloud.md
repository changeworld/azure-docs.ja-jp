---
title: チュートリアル - プライベート クラウドをスケーリングする
description: このチュートリアルでは、Azure portal を使用して、Azure VMware Solution のプライベート クラウドをスケーリングします。
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: e1226eb98607a34869bda1f998c7cecea2e50919
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91254416"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>チュートリアル:Azure VMware Solution のプライベート クラウドをスケーリングする

Azure VMware Solution のプライベート クラウド エクスペリエンスを最大限に活かすには、予定されたワークロードの要件を反映するようにクラスターとホストをスケーリングします。 実際のアプリケーション ワークロードに応じて、プライベート クラウドにあるクラスターの数とホストの数をスケーリングすることができます。 特定のサービスに対するパフォーマンスと可用性の制限に対しては、そのつど Azure VMware Solution のプライベート クラウド環境内で対処する必要があります。 クラスターとホストの制限については、[プライベート クラウドの概念](concepts-private-clouds-clusters.md)に関する記事を参照してください。

このチュートリアルでは、次を行うために Azure portal を使用します。

> [!div class="checklist"]
> * 既存のプライベート クラウドにクラスターを追加する
> * 既存のクラスターにホストを追加する

## <a name="prerequisites"></a>前提条件

このチュートリアルで行う作業には、プライベート クラウドが必要です。 まだプライベート クラウドの作成が済んでいない場合は、[プライベート クラウドの作成に関するチュートリアル](tutorial-create-private-cloud.md)を参照して作成し、Azure で VMware のプライベート クラウドのネットワークを構成して、必要な仮想ネットワークをセットアップしてください。

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

もう 1 つ Azure VMware Solution のプライベート クラウドが必要な場合は、同じネットワーク前提条件および同じクラスターとホストの制限に従って、[別のプライベート クラウドを作成](tutorial-create-private-cloud.md)してください。

<!-- LINKS - external-->

<!-- LINKS - internal -->
