---
title: Azure Data Factory でラングリング データ フローの使用を開始する
description: ラングリング データ フローを使用して Azure Data Factory データを準備する方法に関するチュートリアル
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e9a4f7266522fc17c33e2086af6cb7367bbed39e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650682"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>ラングリング データ フローを使用してデータを準備する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

> [!NOTE]
> ラングリング データ フローは現在パブリック プレビューで提供されています

## <a name="create-a-wrangling-data-flow"></a>ラングリング データ フローを作成する

Azure Data Factory でラングリング データ フローを作成するには、2 つの方法があります。 1 つ目の方法は、プラス記号のアイコンをクリックし、Factory リソースのウィンドウで **[データ フロー]** を選択することです。

![ファクトリ リソース ペインでの [データ フロー] を示すスクリーンショット。](media/wrangling-data-flow/tutorial7.png)

もう 1 つの方法は、パイプライン キャンバスのアクティビティ ウィンドウで行います。 **[移動と変換]** アコーディオンを開き、 **[データ フロー]** アクティビティをキャンバスにドラッグします。

どちらの方法でも、開いたサイド ウィンドウで **[Create new data flow]\(新しいデータ フローの作成\)** を選択し、 **[Wrangling data flow]\(ラングリング データ フロー\)** を選択します。 [OK] をクリックします。

![[Wrangling data flow]\(ラングリング データ フロー\) オプションが強調表示されているスクリーンショット。](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>ラングリング データ フローを作成する

ラングリング データ フロー用の**ソース データセット** を追加します。 既存のデータセットを選択するか、新しいデータセットを作成することができます。 シンク データセットを選択することもできます。 1 つまたは複数のソース データセットを選択できますが、現時点では 1 つのシンクのみが許可されます。 シンク データセットの選択は省略可能ですが、少なくとも 1 つのソース データセットが必要です。

> [!NOTE]
> 限定プレビューでは、ADLS Gen 2 区切りテキストのみがサポートされています。 

![ラングリング](media/wrangling-data-flow/tutorial4.png)

**[作成]** をクリックして、Power Query Online マッシュアップ エディターを開きます。

![Power Query Online マッシュアップ エディターを開く [作成] ボタンを示すスクリーンショット。](media/wrangling-data-flow/tutorial5.png)

コーディング不要のデータ準備を使用して、ラングリング データ フローを作成します。 使用できる関数の一覧については、[変換関数](wrangling-data-flow-functions.md)に関するページを参照してください。

![ラングリング データ フローを作成するためのプロセスを示すスクリーンショット。](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>ラングリング データ フローを実行して監視する

ラングリング データ フローのパイプライン デバッグ実行を実行するには、パイプライン キャンバスで **[デバッグ]** をクリックします。 データ フローを発行すると、 **[Trigger Now]\(今すぐトリガー\)** によって、最後に発行されたパイプラインのオンデマンド実行が実行されます。 ラングリング データ フローは、既存のすべての Azure Data Factory トリガーを使用してスケジュールできます。

![ラングリング データ フローを追加する方法を示すスクリーンショット。](media/wrangling-data-flow/tutorial3.png)

**[監視]** タブに移動し、トリガーされたラングリング データ フロー アクティビティの実行の出力を視覚化します。

![トリガーされたラングリング データ フロー アクティビティの実行の出力を示すスクリーンショット。](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>次のステップ

[マッピング データ フローの作成](tutorial-data-flow.md)方法について確認します。