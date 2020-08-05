---
title: 'ML Studio (classic): ノートブック (プレビュー) データをダウンロードする方法 - Azure'
description: ML Studio (クラシック) でノートブック (プレビュー) データをダウンロードする方法。
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.date: 04/15/2020
ms.openlocfilehash: cc12204c5eabf857fd1143e71df9b2b505d88a23
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318659"
---
# <a name="download-notebookspreview-data"></a>ノートブック (プレビュー) データをダウンロードする

**適用対象:** ![いいえ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-azure-ml.md) ![はい](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic) 

Notebooks (プレビュー) 機能は、*2020 年 4 月 13 日*に削除されました。 Notebooks (プレビュー) のタブとユーザー ノートブック ファイルの削除は、現在、世界中の Azure リージョンにロールアウトされています。

この記事では、Notebooks (プレビュー) データをダウンロードする手順について詳しく説明します。

1. Azure Machine Learning Studio (クラシック) で、 **[Notebooks]** タブに移動します。
    ![[Notebooks] タブに移動する](./media/download-notebooks/notebooks-list.png)

1. ダウンロードするノートブックを選択します。 それは Jupyter で開かれます。
    ![ノートブックを選択する](./media/download-notebooks/select-notebook.png) 複数のノートブックの同時ダウンロードはサポートされていません。

1. **[ファイル]**  ->  **[形式を指定してダウンロード]** に移動し、フォーマット オプションを選択します。
    ![ノートブックのダウンロード オプション](./media/download-notebooks/download-options.PNG)

1. オプションを選択すると、ノートブック ファイルのダウンロードが開始されます。 表示される 500 または502 エラーは無視してください。これらはダウンロードには影響しません。
