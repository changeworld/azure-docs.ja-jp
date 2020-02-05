---
title: 'データのエクスポート: モジュール リファレンス'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning でデータのエクスポート モジュールを使用して、パイプラインからの結果や中間データ、作業データを Azure Machine Learning の外部のクラウド ストレージに保存する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: c83bfc4a3ec8d1163669f28dfde64c63e7c6ff89
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775070"
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
