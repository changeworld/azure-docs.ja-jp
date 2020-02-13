---
title: 'データのエクスポート: モジュール リファレンス'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning でデータのエクスポート モジュールを使用して、パイプラインからの結果や中間データ、作業データを Azure Machine Learning の外部のクラウド ストレージに保存する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 11f5bd7f01e142273509ae59ddc19a2557464bde
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152314"
---
# <a name="export-data-module"></a>データのエクスポート モジュール

この記事では Azure Machine Learning デザイナー (プレビュー) 内のモジュールについて説明します。

パイプラインの結果や中間データ、作業データを Azure Machine Learning の外部のクラウド ストレージに保存するには、このモジュールを使用します。 

このモジュールは、次のクラウド データ サービスにデータをエクスポートする作業を支援します。

- Azure BLOB コンテナー
- Azure ファイル共有
- Azure Data Lake
- Azure Data Lake Gen2

データをエクスポートする前に、まずは Azure Machine Learning ワークスペースにデータストアを登録する必要があります。 詳細については、「[Azure ストレージ サービスのデータにアクセスする](../how-to-access-data.md)」を参照してください。

## <a name="how-to-configure-export-data"></a>データのエクスポートを構成する方法

1. デザイナーで、**データのエクスポート** モジュールをパイプラインに追加します。 このモジュールは、 **[Input and Output]\(入力と出力\)** カテゴリにあります。

1. エクスポートしたいデータがあるモジュールに **[Export Data]\(データのエクスポート\)** を接続します。

1. **[データのエクスポート]** を選択して **[プロパティ]** ウィンドウを開きます。

1. **[データストア]** では、ドロップダウン リストから既存のデータストアを選択します。 新しいデータストアを作成することもできます。 方法については、「[Azure ストレージ サービスのデータにアクセスする](../how-to-access-data.md)」を参照してください。

1. データを書き込むデータストア内のパスを定義します。 


1. **[ファイル形式]** で、データの保存形式を選択します。
 
1. パイプラインを実行します。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 
