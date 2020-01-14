---
title: Azure HPC キャッシュを作成する
description: Azure HPC Cache インスタンスを作成する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: rohogue
ms.openlocfilehash: 793a80e7019e72c1cb3087da02d5642639cb8d5e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647158"
---
# <a name="create-an-azure-hpc-cache"></a>Azure HPC キャッシュを作成する

Azure portal を使用してキャッシュを作成します。

![Azure portal に表示されるキャッシュの概要と下部の作成ボタンのスクリーンショット](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>基本的な詳細を定義する

![Azure portal のプロジェクト詳細ページのスクリーンショット](media/hpc-cache-create-basics.png)

**[プロジェクトの詳細]** で、キャッシュのホストとなるサブスクリプションとリソース グループを選択します。 サブスクリプションが[アクセス](hpc-cache-prereqs.md#azure-subscription)一覧にあることを確認します。

**[サービスの詳細]** で、キャッシュの名前と、これらのその他の属性を設定します。

* 場所 - [サポートされているリージョン](hpc-cache-overview.md#region-availability)のいずれかを選択します。
* 仮想ネットワーク - 既存のものを選択するか、新しい仮想ネットワークを作成することができます。
* サブネット - このAzure HPC キャッシュ インスタンス専用の少なくとも 64 個の IP アドレス (/24) を含むサブネットを選択または作成します。

## <a name="set-cache-capacity"></a>キャッシュ容量を設定する
<!-- referenced from GUI - update aka.ms link if you change this header text -->

**[キャッシュ]** ページで、キャッシュの容量を設定する必要があります。 ご自分のキャッシュで保持できるデータの量とクライアント要求の処理の迅速さは、ここで設定する値によって決まります。

また、容量はキャッシュのコストにも影響します。

これらの 2 つの値を設定して容量を選択してください。

* キャッシュの最大データ転送速度 (スループット) (GB/秒)
* キャッシュ データ用に割り当てるストレージの容量 (TB)

選択可能ないずれかのスループット値とキャッシュ ストレージ サイズを選択してください。

実際のデータ転送速度は、ワークロードとネットワーク速度、ストレージ ターゲットの種類によって異なることに注意してください。 選択した値により、キャッシュ システム全体の最大スループットが設定されますが、その一部はオーバーヘッド タスクに使用されます。 たとえば、キャッシュに既に格納されていないファイルがクライアントによって要求された場合、またはファイルが古いとマークされている場合、キャッシュでは、スループットの一部を使用して、バックエンド ストレージからファイルがフェッチされます。

Azure HPC Cache では、キャッシュ ヒット率を最大限に高めるために、どのファイルをキャッシュして事前に読み込むかが管理されます。 キャッシュの内容は絶えず評価され、アクセスされる頻度が低くなったファイルは長期ストレージに移されます。 メタデータ用の追加領域やその他のオーバーヘッドと共にアクティブな作業ファイル一式を無理なく保持できるキャッシュ ストレージ サイズを選んでください。

![キャッシュ サイズ設定ページのスクリーンショット](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>リソース タグを追加する (省略可)

**[タグ]** ページでは、お使いの Azure HPC キャッシュ インスタンスに[リソース タグ](https://go.microsoft.com/fwlink/?linkid=873112)を追加できます。

## <a name="finish-creating-the-cache"></a>キャッシュの作成を完了する

新しいキャッシュを構成したら、 **[確認と作成]** タブをクリックします。選択内容がポータルによって検証されるほか、自分で選択内容を確認することができます。 すべて正しければ **[作成]** をクリックしてください。

キャッシュの作成には 10 分程度かかります。 進行状況は、Azure portal の通知パネルで追跡できます。

![ポータルのキャッシュ作成画面 ("デプロイが進行中です" ページと "通知" ページ) のスクリーンショット](media/hpc-cache-deploy-status.png)

作成が完了すると、新しい Azure HPC Cache インスタンスへのリンクと共に通知が表示され、ご利用のサブスクリプションの **[リソース]** リストにキャッシュが表示されます。
<!-- double check on notification -->

![Azure portal における Azure HPC Cache インスタンスのスクリーンショット](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>次のステップ

キャッシュが **[リソース]** リストに表示されたら、ストレージ ターゲットを定義して、キャッシュがデータ ソースにアクセスできるようにします。

* [ストレージ ターゲットを追加する](hpc-cache-add-storage.md)
