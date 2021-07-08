---
title: GATK リソース バンドル
titleSuffix: Azure Open Datasets
description: GATK リソース バンドル データセットを Azure Open Datasets で使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: a895654027caa9b56a3a21d217ac9d105b1d6130
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038847"
---
# <a name="gatk-resource-bundle"></a>GATK リソース バンドル

[GATK リソース バンドル](https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle)は、GATK でヒトの再シーケンシング データを操作するための標準的ファイルのコレクションです。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>データ ソース

このデータセットは https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle のデータ ストアのミラーです。

## <a name="data-volumes-and-update-frequency"></a>データ量と更新の頻度

1. datasetgatkbestpractices :542 GB
1. datasetgatklegacybundles :61 GB
1. datasetgatktestdata :2 TB
1. datasetpublicbroadref :477 GB
1. datasetbroadpublic :3 TB

データセットは毎月第 1 週に更新されます。

## <a name="storage-location"></a>保存先

このデータセットは米国西部 2 および米国中西部 Azure リージョンに保存されています。 アフィニティのため、米国西部 2 または米国中西部にコンピューティング リソースを割り当てることをお勧めします。

## <a name="data-access"></a>データ アクセス

1. datasetgatkbestpractices

    米国西部 2: https://datasetgatkbestpractices.blob.core.windows.net/dataset
    
    米国中西部: https://datasetgatkbestpractices-secondary.blob.core.windows.net/dataset
    
    [SAS Token](/azure/storage/common/storage-sas-overview): ?sv=2020-04-08&si=prod&sr=c&sig=6SaDfKtXAIfdpO%2BkvNA%2FsTNmNij%2Byh%2F%2F%2Bf98WAUqs7I%3D

2. datasetgatklegacybundles

    米国西部 2: https://datasetgatklegacybundles.blob.core.windows.net/dataset
    
    米国中西部: https://datasetgatklegacybundles-secondary.blob.core.windows.net/dataset
    
    [SAS Token](/azure/storage/common/storage-sas-overview): ?sv=2020-04-08&si=prod&sr=c&sig=xBfxOPBqHKUCszzwbNCBYF0k9osTQjKnZbEjXCW7gU0%3D

3. datasetgatktestdata

    米国西部 2: https://datasetgatktestdata.blob.core.windows.net/dataset
    
    米国中西部: https://datasetgatktestdata-secondary.blob.core.windows.net/dataset
    
    [SAS Token](/azure/storage/common/storage-sas-overview): ?sv=2020-04-08&si=prod&sr=c&sig=fzLts1Q2vKjuvR7g50vE4HteEHBxTcJbNvf%2FZCeDMO4%3D

4. datasetpublicbroadref
    
    米国西部 2: https://datasetpublicbroadref.blob.core.windows.net/dataset
    
    米国中西部: https://datasetpublicbroadref-secondary.blob.core.windows.net/dataset
    
    [SAS Token](/azure/storage/common/storage-sas-overview): ?sv=2020-04-08&si=prod&sr=c&sig=DQxmjB4D1lAfOW9AxIWbXwZx6ksbwjlNkixw597JnvQ%3D

5. datasetbroadpublic

    米国西部 2: https://datasetpublicbroadpublic.blob.core.windows.net/dataset
    
    米国中西部: https://datasetpublicbroadpublic-secondary.blob.core.windows.net/dataset
    
    [SAS Token](/azure/storage/common/storage-sas-overview): ?sv=2020-04-08&si=prod&sr=c&sig=u%2Bg2Ab7WKZEGiAkwlj6nKiEeZ5wdoJb10Az7uUwis%2Fg%3D

## <a name="use-terms"></a>使用条件

[GATK リソース バンドルの公式サイト](https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle)にアクセスしてください。

## <a name="contact"></a>Contact

[GATK リソース バンドルの公式サイト](https://gatk.broadinstitute.org/hc/articles/360035890811-Resource-bundle)にアクセスしてください。

## <a name="next-steps"></a>次の手順

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。