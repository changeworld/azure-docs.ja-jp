---
title: 1000 ゲノム
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で 1000 Genomes データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 52f948526b1575f43d722c856251eed83b2e66ef
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982782"
---
# <a name="1000-genomes"></a>1000 ゲノム

1000 Genomes Project は 2008 年から 2015 年にかけて実施され、ヒトの多様性と遺伝子型データの最大の公開カタログが作成されました。 最終的なデータ セットには、26 の母集団からの 2,504 人のデータと、特定された 8,400 万の変異が含まれています。 詳細については、1000 Genomes Project の Web サイトと以下の出版物をご覧ください。

パイロット分析: 母集団スケール シーケンシングからのヒトの遺伝的変異マップ Nature 467, 1061-1073 (2010 年 10 月 28 日)

フェーズ 1 の分析: 1,092 のヒト ゲノムからの遺伝的変異の統合マップ Nature 491, 56-65 (2012 年 11 月 01 日)

フェーズ 3 の分析: ヒトの遺伝的変異のグローバル リファレンス Nature 526, 68-74 (2015 年 10 月 01 日)、および 2,504 のヒト ゲノムにおける構造多型の統合マップ Nature 526, 75-81 (2015 年 10 月 01 日)

データ形式の詳細については、「 http://www.internationalgenome.org/formats 」をご覧ください

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="data-source"></a>データ ソース

このデータセットは ftp://ftp.1000genomes.ebi.ac.uk/vol1/ftp/ のミラーです

## <a name="data-volumes-and-update-frequency"></a>データ量と更新の頻度

このデータセットには、約 815 TB のデータが含まれており、毎日更新されます。

## <a name="storage-location"></a>保存先

このデータセットは米国西部 2 および米国中西部 Azure リージョンに保存されています。 アフィニティのため、米国西部 2 または米国中西部にコンピューティング リソースを割り当てることをお勧めします。

## <a name="data-access"></a>データ アクセス

米国西部 2: 'https://dataset1000genomes.blob.core.windows.net/dataset '

米国中部: 'https://dataset1000genomes-secondary.blob.core.windows.net/dataset '

[SAS Token](../storage/common/storage-sas-overview.md): sv=2019-10-10&si=prod&sr=c&sig=9nzcxaQn0NprMPlSh4RhFQHcXedLQIcFgbERiooHEqM%3D

## <a name="use-terms"></a>使用条件

最終的に出版された後、1000 Genomes Project のデータは、データセットの提供元が提供する条件の下で、誰もが制限なく利用できるようになりました ([http://www.internationalgenome.org/data](http://www.internationalgenome.org/data))。 データの使用については、1000 ゲノム プロジェクトの [FAQ]() に掲載されている詳細を引用してください。

## <a name="contact"></a>Contact

https://www.internationalgenome.org/contact

## <a name="next-steps"></a>次の手順

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。
