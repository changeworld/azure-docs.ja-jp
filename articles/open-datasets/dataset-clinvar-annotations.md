---
title: ClinVar Annotations
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で ClinVar Annotations データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: e5bad3f24d5692bbebb668e29b5ea229294b46f9
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982746"
---
# <a name="clinvar-annotations"></a>ClinVar Annotations

[ClinVar](https://www.ncbi.nlm.nih.gov/clinvar/) は人間の差異と表現型の間の関係についてのレポートと、それを裏付ける証拠を集めた、自由にアクセスできる公開アーカイブです。 人間の差異と観察された健康状態との間で断定されている関係、およびその解釈の履歴に対するアクセスやコミュニケーションを支援します。 また、ゲノミクスのワークフローやアプリケーションに組み込むことができる、より幅広い臨床的解釈へのアクセスを提供します。

データの詳細については、「[Data Dictionary](https://www.ncbi.nlm.nih.gov/projects/clinvar/ClinVarDataDictionary.pdf)」と「[FAQ](https://www.ncbi.nlm.nih.gov/clinvar/docs/faq/)」を参照してください。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>データ ソース

このデータセットは、ftp://ftp.ncbi.nlm.nih.gov/pub/clinvar/xml/ をミラーリングしたものです

## <a name="data-volumes-and-update-frequency"></a>データ量と更新の頻度

このデータセットには、約 56 GB のデータが含まれており、毎日更新されます。

## <a name="storage-location"></a>保存先

このデータセットは米国西部 2 および米国中西部 Azure リージョンに保存されています。 アフィニティのため、米国西部 2 または米国中西部にコンピューティング リソースを割り当てることをお勧めします。

## <a name="data-access"></a>データ アクセス

米国西部 2: "https://datasetclinvar.blob.core.windows.net/dataset"

米国中西部: "https://datasetclinvar-secondary.blob.core.windows.net/dataset"

[SAS トークン](../storage/common/storage-sas-overview.md): sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=qFPPwPba1RmBvaffkzkLuzabYU5dZstSTgMwxuLNME8%3D

## <a name="use-terms"></a>使用条件
データは制限なく使用できます。 詳細および引用の詳細については、「[Accessing and using data in ClinVar](https://www.ncbi.nlm.nih.gov/clinvar/docs/maintenance_use/)」を参照してください。

## <a name="contact"></a>Contact

このデータセットに関する質問またはフィードバックについては、clinvar@ncbi.nlm.nih.gov にお問い合わせください。

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-clinvar -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=genomics-clinvar)** 。

## <a name="getting-the-clinvar-data-from-azure-open-dataset"></a>Azure Open Dataset から ClinVar データを取得する

いくつかの公開ゲノミクス データが Azure Open Dataset として[こちら](https://azure.microsoft.com/services/open-datasets/catalog/)にアップロードされています。 このオープン データセットにリンクされている Blob service を作成します。 `ClinVar` データセット用のデータを Azure Open Datasets から呼び出す手順の例を以下に示します。

ユーザーは、このノートブックを使用して次のパスを呼び出してダウンロードできます: "https://datasetclinvar.blob.core.windows.net/dataset/ClinVarFullRelease_00-latest.xml.gz.md5"

> [!NOTE]
> Azure ML SDK を使用してデータを表示するには、ユーザーは Azure CLI を使用して自分の Azure アカウントにログインする必要があります。 一方、データをダウンロードするためのアクションは必要ありません。

Azure CLI のインストールの詳細については、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

### <a name="calling-the-data-from--clinvar-data-set"></a>'ClinVar Data Set' からのデータの呼び出し

```python
import azureml.core
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

```python
from azureml.core import  Dataset
reference_dataset = Dataset.File.from_files('https://datasetclinvar.blob.core.windows.net/dataset')
mount = reference_dataset.mount()
```

```python
import os

REF_DIR = '/dataset'
path = mount.mount_point + REF_DIR

with mount:
    print(os.listdir(path))
```

```python
import pandas as pd

# create mount context
mount.start()

# specify path to README file
REF_DIR = '/dataset'
metadata_filename = '{}/{}/{}'.format(mount.mount_point, REF_DIR, '_README')

# read README file
metadata = pd.read_table(metadata_filename)
metadata
```

### <a name="download-the-specific-file"></a>特定のファイルをダウンロードする

```python
import os
import uuid
import sys
from azure.storage.blob import BlockBlobService, PublicAccess

blob_service_client = BlockBlobService(account_name='datasetclinvar', sas_token='sv=2019-02-02&se=2050-01-01T08%3A00%3A00Z&si=prod&sr=c&sig=qFPPwPba1RmBvaffkzkLuzabYU5dZstSTgMwxuLNME8%3D')     
blob_service_client.get_blob_to_path('dataset', 'ClinVarFullRelease_00-latest.xml.gz.md5', './ClinVarFullRelease_00-latest.xml.gz.md5')
```

<!-- nbend -->

---

## <a name="next-steps"></a>次の手順

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。
