---
title: チュートリアル:プライベート クラウドをスケーリングする
description: このチュートリアルでは、Azure portal を使用して Azure VMware Solution (AVS) プレビュー プライベート クラウドをスケーリングします。
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 70d8c1c555badd6102f4b2547492bdea54e55783
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82739727"
---
# <a name="tutorial-scale-an-azure-vmware-solution-avs-preview-private-cloud"></a>チュートリアル:Azure VMware Solution (AVS) プレビュー プライベート クラウドをスケーリングする

AVS プレビューのプライベート クラウド エクスペリエンスを最大限に活かし、予定されたワークロードの要件を反映するためには、クラスターとホストをスケーリングすることになります。 プレビュー段階の AVS では、オンプレミスの vCenter がサポートされないため、既に作成されているものを、Azure portal から使用する必要があります。

実際のアプリケーション ワークロードに応じて、プライベート クラウドにあるクラスターの数とホストの数をスケーリングすることができます。 特定のサービスに対するパフォーマンスと可用性の制限には、その都度 AVS プレビュー クラウド環境内で対処する必要があります。 プライベート クラウドにおけるクラスターとホストの制限については、[プライベート クラウドの概念に関する記事](concepts-private-clouds-clusters.md)を参照してください。

このチュートリアルでは、Azure portal を使用して次の作業を行います。

> [!div class="checklist"]
> * 既存のプライベート クラウドにクラスターを追加する
> * 既存のクラスターにホストを追加する

## <a name="prerequisites"></a>前提条件

このチュートリアルで行う作業には、プライベート クラウドが必要です。 まだプライベート クラウドの作成が済んでいない場合は、[プライベート クラウドの作成に関するチュートリアル](tutorial-create-private-cloud.md)を使用して、Azure にプライベート クラウドを作成し、VMware プライベート クラウドのネットワークを構成して、必要な仮想ネットワークをセットアップしてください。

## <a name="add-a-new-cluster"></a>新しいクラスターを追加する

既存のプライベート クラウドの概要ページの **[管理]** で、 **[Scale private cloud]\(プライベート クラウドのスケーリング\)** を選択します。 次に、 **[+ Add a cluster]\(+ クラスターの追加\)** を選択します。

:::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="[Add cluster]\(クラスターの追加\) を選択する" border="true":::

**[クラスターの追加]** ページで、スライダーを使用してホストの数を選択します。 **[保存]** を選択します。

:::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="新しいプライベート クラウド クラスターを構成する" border="true":::

新しいクラスターのデプロイが開始されます。

## <a name="scale-a-cluster"></a>クラスターのスケーリング 

既存のプライベート クラウドの概要ページで、 **[Scale private cloud]\(プライベート クラウドのスケーリング\)** を選択し、鉛筆アイコンを選択してクラスターを編集します。

:::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="[概要] で [Scale private cloud]\(プライベート クラウドのスケーリング\) を選択する" border="true":::

**[クラスターの編集]** ページで、スライダーを使用してホストの数を選択します。 **[保存]** を選択します。

:::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="新しいプライベート クラウド クラスターを構成する" border="true":::

クラスターのホストの追加が開始されます。

## <a name="next-steps"></a>次のステップ

もう 1 つ AVS プライベート クラウドが必要な場合は、同じネットワーク前提条件および同じクラスターとホストの制限に従って、[別のプライベート クラウドを作成](tutorial-create-private-cloud.md)してください。

<!-- LINKS - external-->

<!-- LINKS - internal -->
