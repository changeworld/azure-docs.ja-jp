---
title: SnpEff
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で SnpEff データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 3cc23edff82dce0c14a795e1b3b0a29975cb96e4
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038719"
---
# <a name="snpeff-genomic-variant-annotations-and-functional-effect-prediction-toolbox"></a>SnpEff: ゲノム バリアントのアノテーションと機能効果予測のツールボックス

[SnpEff](https://pcingola.github.io/SnpEff/) 遺伝的バリアントのアノテーションと機能効果予測のツールボックス。 遺伝子やタンパク質に対する遺伝的バリアントの影響 (アミノ酸の変化など) をアノテーションし、予測します。

データの詳細については、[ユーザー マニュアル](https://pcingola.github.io/SnpEff/se_introduction/)を参照してください。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>データ ソース

このデータセットは、 http://downloads.sourceforge.net/project/snpeff/databases/ のミラーです

## <a name="data-volumes-and-update-frequency"></a>データ量と更新の頻度

このデータセットには、約 2 TB のデータが含まれており、毎月更新されます。

## <a name="storage-location"></a>保存先

このデータセットは米国西部 2 および米国中西部 Azure リージョンに保存されています。 アフィニティのため、米国西部 2 または米国中西部にコンピューティング リソースを割り当てることをお勧めします。

## <a name="data-access"></a>データ アクセス

米国西部 2: https://datasetsnpeff.blob.core.windows.net/dataset

米国中西部: https://datasetsnpeff-secondary.blob.core.windows.net/dataset

[SAS Token](../storage/common/storage-sas-overview.md): sv=2019-10-10&st=2020-09-01T00%3A00%3A00Z&se=2050-09-01T00%3A00%3A00Z&si=prod&sr=c&sig=isafOa9tGnYBAvsXFUMDGMTbsG2z%2FShaihzp7JE5dHw%3D

## <a name="use-terms"></a>使用条件
データは制限なく使用できます。 詳細および引用の詳細については、「[Accessing and using data in ClinVar](https://pcingola.github.io/SnpEff/SnpEff_manual.html#intro)」を参照してください。

## <a name="contact"></a>Contact

このデータセットに関する質問またはフィードバックについては、[Pablo Cingolani](http://www.linkedin.com/in/pablocingolani) までご連絡ください。


## <a name="next-steps"></a>次の手順

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。