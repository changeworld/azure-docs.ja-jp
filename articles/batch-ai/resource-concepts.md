---
title: Batch AI リソース について - Azure |Microsoft Docs
description: Microsoft Azure での Batch AI サービス内のワークスペース、クラスター、ファイル サーバー、実験、およびジョブの概要です。
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/15/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 4454448809411c6390bc1f65c33b4abe76094ded
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407747"
---
# <a name="overview-of-resources-in-batch-ai"></a>Batch AI でのリソースの概要

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

最初に Batch AI サービスの使用を開始する際は、使用可能な Batch AI リソースを理解しましょう。 その他の Azure サービスと同様に、1 つまたは複数の Azure *リソース グループ*内で Batch AI リソースを作成します。 1 つまたは複数の Batch AI の*ワークスペース*をリソース グループに作成します。 各ワークスペースには、Batch AI の*クラスター*、*ファイル サーバー*、および*実験*が混在しています。 Batch AI の実験には、*ジョブ*のグループを含みます。

次の図で、Batch AI のリソースの階層の例を示します。 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

次のセクションでは、Batch AI リソースの詳細に進みます。

## <a name="workspace"></a>ワークスペース

Batch AI のワークスペースは、残る全ての Batch AI リソースの最上位のコレクションです。 ワークスペースは、異なるグループまたはプロジェクトに属している作業の分離を手助けします。 たとえば、開発ワークスペースとテスト ワークスペースを作成します。

## <a name="cluster"></a>クラスター

Batch AI でのクラスターには、ジョブを実行するためのコンピューティング リソースが含まれています。 クラスター内のすべてのノードには、同じ VM のサイズと OS イメージがあります。 Batch AI には、さまざまなニーズに合わせてカスタマイズされたクラスターを作成するためのさまざまなオプションが用意されています。 通常、プロジェクトを完了するために必要な処理能力の各カテゴリに異なるクラスターを設定します。 Batch AI クラスターを需要と予算に基づいてスケール アップ/ダウンします。 詳細は、[Batch AI クラスターの使用](clusters.md)を参照してください。

## <a name="file-server"></a>ファイル サーバー

任意で、データ、トレーニング スクリプトを格納し、ログを出力するためのファイル サーバーを Batch AI 内に作成します。 Batch AI ファイル サーバーとは、クラスター ノードに自動的にマウントされる管理された単一ノード NFS で、簡単かつ一元的にアクセスできるジョブの保存場所を提供します。 ほとんどの場合、ワークスペースで必要なファイル サーバーは 1 つだけであり、トレーニング ジョブのデータを異なるディレクトリに分離することができます。 NFS がワークロードに適していない場合、Batch AI では、[Azure Storage](use-azure-storage.md)または Gluster や Lustre ファイル システムなどのカスタム ソリューションを含むその他のストレージ オプションがサポートされています。

## <a name="experiment"></a>実験

実験では、エリを実行し、まとめて管理する関連するジョブをグループ化します。 各ワークスペースは、個々 の実験が 1 つの特定の問題を解決しようとする、複数の実験があります。

## <a name="job"></a>ジョブ

ジョブは、たとえばディープ ラーニング モデルをトレーニングするために実行する必要のある1 つのタスクやスクリプトです。 各ジョブは、ワークスペース内の 1 つのクラスターで特定のスクリプトを実行します。 (スクリプトは、Batch AI ファイル サーバーまたは他のストレージ ソリューションに格納される可能性があります。)各 Batch AI ジョブには、それぞれに関連付けられているフレームワーク型があります:TensorFlow、Horovod、CNTK、Caffe、Caffe2、pyTorch、Chainer、カスタム MPI、またはカスタム。 各フレームワークについて、Batch AI サービスは、必要なインフラストラクチャを設定し、ジョブ プロセスを管理します。 各実験には、異なるパラメーターに多少の変更がある以外は類似している複数のジョブがあります。

## <a name="next-steps"></a>次の手順

* 最初の[Batch AI トレーニング ジョブ](quickstart-tensorflow-training-cli.md)を実行します。

* さまざまなフレームワークのサンプル [トレーニング レシピ](https://github.com/Azure/BatchAI/tree/master/recipes)を確認する。