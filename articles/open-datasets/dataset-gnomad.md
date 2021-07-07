---
title: ゲノム集計データベース (gnomAD)
titleSuffix: Azure Open Datasets
description: Azure Open Datasets でゲノム集計データベース (gnomAD) データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 52fc504135e77ef9e32cd1b83f1186d7ddb18fcd
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038834"
---
# <a name="genome-aggregation-database-gnomad"></a>ゲノム集計データベース (gnomAD)

[ゲノム集計データベース (gnomAD)](https://gnomad.broadinstitute.org/) は国際的な研究者連合によって開発されたリソースであり、さまざまな大規模シーケンス プロジェクトから得られたエクソームとゲノム シーケンス データの両方を集約および調和させ、その要約データをより幅広い科学コミュニティで利用できるようにすることを目的としています。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>データ ソース

このデータセットは Broad Institute との共同研究として提供されており、完全な gnomAD データ カタログは https://gnomad.broadinstitute.org/downloads で見ることができます

## <a name="data-volumes-and-update-frequency"></a>データ量と更新の頻度

このデータセットには約 30 TB のデータが含まれており、gnomAD のリリースごとに更新されます。

## <a name="storage-location"></a>保存先

このデータセットをホストしているストレージ アカウントは、米国東部 Azure リージョンにあります。 アフィニティのために、米国東部でコンピューティング リソースを割り当てることをお勧めします。

## <a name="data-access"></a>データ アクセス

ストレージ アカウント: https://azureopendatastorage.blob.core.windows.net/gnomad

データは制限なく公開されており、一括操作には azcopy ツールをお勧めします。 たとえば、gnomAD のリリース 3.0 で VCF を表示するには、次のように行います。

```powershell
$ azcopy ls https://azureopendatastorage.blob.core.windows.net/gnomad/release/3.0/vcf/genomes
```

すべての VCF を再帰的にダウンロードするには、次のように行います。

```powershell
$ azcopy cp --recursive=true https://azureopendatastorage.blob.core.windows.net/gnomad/release/3.0/vcf/genomes .
```

また、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) は、gnomAD リリースのファイル一覧を閲覧するのに便利なツールです。

## <a name="use-terms"></a>使用条件

データは制限なく使用できます。 詳細情報と引用の詳細については、[gnomAD の概要ページ](https://gnomad.broadinstitute.org/about)を参照してください。

## <a name="contact"></a>Contact

このデータセットに関する質問またはフィードバックについては、[gnomAD チーム](https://gnomad.broadinstitute.org/contact)までご連絡ください。

## <a name="next-steps"></a>次の手順

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。