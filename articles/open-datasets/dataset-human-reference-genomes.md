---
title: Human Reference Genomes
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で Human Reference Genomes データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 1e1b1fa785d4ab91329216983c5b694cc484860a
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297726"
---
# <a name="human-reference-genomes"></a>Human Reference Genomes

このデータセットには、[Genome Reference Consortium](https://www.ncbi.nlm.nih.gov/grc) によってアセンブルされた 2 つのヒトゲノム リファレンス (Hg19 と Hg38) が含まれています。

Hg19 (GRCh37) データの詳細については、[NCBI の GRCh37 レポート](https://www.ncbi.nlm.nih.gov/assembly/GCF_000001405.13/)をご覧ください。

Hg38 データの詳細については、[NCBI の GRCh38 レポート](http://www.ncbi.nlm.nih.gov/assembly/GCF_000001405.26/)をご覧ください。

データのその他の詳細情報については、[NCBI RefSeq](https://www.ncbi.nlm.nih.gov/refseq/) サイトをご覧ください。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>データ ソース

このデータセットのソースは次の 2 つの FTP の場所です。

ftp://ftp.ncbi.nih.gov/genomes/refseq/vertebrate_mammalian/Homo_sapiens/all_assembly_versions/GCF_000001405.25_GRCh37.p13/

ftp://ftp.ncbi.nih.gov/genomes/refseq/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/

BLOB 名には、"vertebrate_mammalian" で始まる URI セグメントのプレフィックスが付けられます。

## <a name="data-volumes-and-update-frequency"></a>データ量と更新の頻度

このデータセットには、約 10 GB のデータが含まれており、毎日更新されます。

## <a name="storage-location"></a>保存先

このデータセットは米国西部 2 および米国中西部 Azure リージョンに保存されています。 アフィニティのため、米国西部 2 または米国中西部にコンピューティング リソースを割り当てることをお勧めします。

## <a name="data-access"></a>データ アクセス

米国西部 2: 'https://datasetreferencegenomes.blob.core.windows.net/dataset '

米国中西部: 'https://datasetreferencegenomes-secondary.blob.core.windows.net/dataset '

[SAS トークン](../storage/common/storage-sas-overview.md): sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=JtQoPFqiC24GiEB7v9zHLi4RrA2Kd1r%2F3iFt2l9%2FlV8%3D

## <a name="use-terms"></a>使用条件

データは制限なく使用できます。 詳細および引用の詳細については、[NCBI Reference Sequence Database サイト](https://www.ncbi.nlm.nih.gov/refseq/)を参照してください。

## <a name="contact"></a>Contact

このデータセットに関する質問またはフィードバックについては、[Genome Reference Consortium](https://www.ncbi.nlm.nih.gov/grc/contact-us) までご連絡ください。

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-reference-genomes -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-reference-genomes)** 。

## <a name="getting-the-reference-genomes-from-azure-open-datasets"></a>Azure Open Datasets から Reference Genomes を取得する

いくつかの公開ゲノミクス データが Azure Open Dataset として[こちら](https://azure.microsoft.com/services/open-datasets/catalog/)にアップロードされています。 このオープン データセットにリンクされている Blob service を作成します。 `Reference Genomes` データセット用のデータを Azure Open Datasets から呼び出す手順の例を以下に示します。

ユーザーは、このノートブックを使用して次のパスを呼び出してダウンロードできます。'https://datasetreferencegenomes.blob.core.windows.net/dataset/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_assembly_structure/genomic_regions_definitions.txt '

**重要な注意事項:** Azure ML SDK を使用してデータを表示するには、ユーザーは Azure CLI を使用して自分の Azure アカウントにログインする必要があります。 一方、データをダウンロードするためのアクションは必要ありません。

[Azure CLI のインストール](/cli/azure/install-azure-cli)を実行します。

### <a name="calling-the-data-from--reference-genome-datasets"></a>'Reference Genome Datasets' からデータを呼び出す

```python
import azureml.core
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

```python
from azureml.core import  Dataset
reference_dataset = Dataset.File.from_files('https://datasetreferencegenomes.blob.core.windows.net/dataset')
mount = reference_dataset.mount()
```

```python
import os

REF_DIR = '/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_assembly_structure'
path = mount.mount_point + REF_DIR

with mount:
    print(os.listdir(path))
```

```python
import pandas as pd

# create mount context
mount.start()

# specify path to genomic_regions_definitions.txt file
REF_DIR = 'vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_assembly_structure'
metadata_filename = '{}/{}/{}'.format(mount.mount_point, REF_DIR, 'genomic_regions_definitions.txt')

# read genomic_regions_definitions.txt file
metadata = pd.read_table(metadata_filename)
metadata
```

### <a name="download-the-specific-file"></a>特定のファイルをダウンロードする

```python
import os
import uuid
import sys
from azure.storage.blob import BlockBlobService, PublicAccess

blob_service_client = BlockBlobService(account_name='datasetreferencegenomes',sas_token='sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=JtQoPFqiC24GiEB7v9zHLi4RrA2Kd1r%2F3iFt2l9%2FlV8%3D')     
blob_service_client.get_blob_to_path('dataset/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.39_GRCh38.p13/GCF_000001405.39_GRCh38.p13_assembly_structure', 'genomic_regions_definitions.txt', './genomic_regions_definitions.txt')
```

<!-- nbend -->

---

## <a name="next-steps"></a>次の手順

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。