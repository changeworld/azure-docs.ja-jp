---
title: OpenCravat
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で OpenCravat データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 2047b5f5071f6166a7f30e7a04b79fa711c95624
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982129"
---
# <a name="opencravat-open-custom-ranked-analysis-of-variants-toolkit"></a>OpenCravat: 変異のオープン カスタム ランク付け分析ツールキット

OpenCRAVAT は、変異の影響、アノテーション、スコアリングを含むゲノム変異解釈を実行する python パッケージです。 OpenCRAVAT にはモジュール型のアーキテクチャが採用され、さまざまな解析モジュールやアノテーション リソースが備わっており、特定の研究のニーズに応じて選択し、インストールして実行することができます。

データの詳細については、[OpenCravat](https://opencravat.org/) を参照してください。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>データ ソース

このデータセットは https://store.opencravat.org および https://run.opencravat.org のストアのミラーです。

## <a name="data-volumes-and-update-frequency"></a>データ量と更新の頻度

このデータセットには 500 GB のデータが含まれており、毎日更新されます。

## <a name="storage-location"></a>保存先

このデータセットは米国西部 2 および米国中西部 Azure リージョンに保存されています。 アフィニティのため、米国西部 2 または米国中西部にコンピューティング リソースを割り当てることをお勧めします。

## <a name="data-access"></a>データ アクセス

米国西部 2: 'https://datasetopencravat.blob.core.windows.net/dataset '

米国中西部: 'https://datasetopencravat-secondary.blob.core.windows.net/dataset '

[SAS Token](../storage/common/storage-sas-overview.md): sv=2020-04-08&st=2021-03-11T23%3A50%3A01Z&se=2025-07-26T22%3A50%3A00Z&sr=c&sp=rl&sig=J9J9wnJOXsmEy7TFMq9wjcxjXDE%2B7KhGpCUL4elsC14%3D

## <a name="use-terms"></a>使用条件

OpenCRAVAT は、GPLv3 ライセンスで利用できます。 ほとんどのデータ ソースは、非営利目的では無料です。 商用使用する場合は、各データ ソースの機関の連絡先にご相談ください。

## <a name="contact"></a>Contact

rkarchi1@jhmi.edu

## <a name="next-steps"></a>次の手順

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。
