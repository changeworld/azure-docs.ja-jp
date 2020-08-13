---
title: 'ML Studio (classic): ノートブック (プレビュー) データをダウンロードする方法 - Azure'
description: ML Studio (クラシック) でノートブック (プレビュー) データをダウンロードする方法。
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.date: 04/15/2020
ms.openlocfilehash: 4c900700fa1588cd341171a0176c19acc51129a4
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87430960"
---
# <a name="download-notebookspreview-data"></a>ノートブック (プレビュー) データをダウンロードする

**適用対象:** ![はい](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![いいえ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)

Notebooks (プレビュー) 機能は、*2020 年 4 月 13 日*に削除されました。 Notebooks (プレビュー) のタブとユーザー ノートブック ファイルの削除は、現在、世界中の Azure リージョンにロールアウトされています。

この記事では、Notebooks (プレビュー) データをダウンロードする手順について詳しく説明します。

1. Azure Machine Learning Studio (クラシック) で、 **[Notebooks]** タブに移動します。
    ![[Notebooks] タブに移動する](./media/download-notebooks/notebooks-list.png)

1. ダウンロードするノートブックを選択します。 それは Jupyter で開かれます。
    ![ノートブックを選択する](./media/download-notebooks/select-notebook.png) 複数のノートブックの同時ダウンロードはサポートされていません。

1. **[ファイル]**  ->  **[形式を指定してダウンロード]** に移動し、フォーマット オプションを選択します。
    ![ノートブックのダウンロード オプション](./media/download-notebooks/download-options.PNG)

1. オプションを選択すると、ノートブック ファイルのダウンロードが開始されます。 表示される 500 または502 エラーは無視してください。これらはダウンロードには影響しません。
