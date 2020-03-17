---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/27/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e29cdd56d1c43b3d0e8fc6ca233ac19d8b0004ff
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356383"
---
次の表では、Azure 汎用 v1 (GPv1)、v2 (GPv2)、BLOB、Premium BlockBlob、および Data Lake Gen2 対応のストレージ アカウントの既定の制限について説明します。 "*受信*" 制限は、ストレージ アカウントに送信されるすべてのデータを指します。 *送信*制限は、ストレージ アカウントから受信するすべてのデータを指します。

| リソース | 既定の制限 |
| --- | --- |
| サブスクリプションあたりの各リージョンのストレージ アカウント数 (Standard、Premium、Data Lake Gen2 アカウントすべてを含む)<sup>3</sup> | 250 |
| ストレージ アカウントの最大容量 | 5 PiB <sup>1</sup>|
| ストレージ アカウントあたりの BLOB コンテナー、BLOB、ファイル共有、テーブル、キュー、エンティティ、メッセージの最大数 | 制限なし |
| ストレージ アカウントあたりの最大要求レート<sup>1</sup> | 1 秒あたり 20,000 要求 |
| ストレージ アカウントあたりの最大イングレス<sup>1</sup> (米国、ヨーロッパ リージョン) | 25 Gbps |
| ストレージ アカウントあたりの最大イングレス<sup>1</sup> (米国とヨーロッパ以外のリージョン) | RA-GRS/GRS が有効な場合は 5 Gbps、LRS/ZRS<sup>2</sup> の場合は 10 Gbps |
| 汎用 v2 および BLOB ストレージ アカウントの最大送信速度 (すべてのリージョン) | 50 Gbps |
| 汎用 v1 ストレージ アカウントの最大送信速度 (米国リージョン) | RA-GRS/GRS が有効な場合は 20 Gbps、LRS/ZRS<sup>2</sup> の場合は 30 Gbps |
| 汎用 v1 ストレージ アカウントの最大送信速度 (米国以外のリージョン) | RA-GRS/GRS が有効な場合は 10 Gbps、LRS/ZRS<sup>2</sup> の場合は 15 Gbps |
| ストレージアカウントごとの仮想ネットワーク規則の最大数 | 200 |
| ストレージアカウントごとの IP アドレス規則の最大数 | 200 |

<sup>1</sup> Azure Storage 標準アカウントは、要求によるより高い容量制限とより高いイングレス制限をサポートします。 アカウント制限の引き上げを希望する場合は、[Azure サポートにお問い合わせください](https://azure.microsoft.com/support/faq/)。

<sup>2</sup> お使いのストレージ アカウントの読み取りアクセスで geo 冗長ストレージ (RA-GRS) または geo ゾーン冗長ストレージ (RA-GZRS) を有効にしている場合、第 2 の場所のエグレス ターゲットが第 1 の場所のそれと同じになります。 [Azure Storage のレプリケーション](https://docs.microsoft.com/azure/storage/common/storage-redundancy)には、次のオプションがあります。

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) は、Azure Blob Storage をベースに構築された、ビッグ データ分析専用の一連の機能です。 Azure Storage と Blob Storage の制限は、Data Lake Gen2 に適用されます。

> [!NOTE]
> Microsoft では、ほとんどのシナリオで汎用 v2 ストレージ アカウントを使用することをお勧めしています。 汎用 v1 または Azure BLOB ストレージ アカウントは汎用 v2 アカウントに簡単にアップグレードできます。その際にダウンタイムは発生せず、データをコピーする必要はありません。 詳細については、「[汎用 v2 ストレージ アカウントにアップグレードする](../articles/storage/common/storage-account-upgrade.md)」を参照してください。

アプリケーションで必要とされるスケーラビリティが、単一ストレージ アカウントあたりのスケーラビリティ ターゲットを超えている場合は、複数のストレージ アカウントを使用するようにアプリケーションを構築できます。 その後、それらのストレージ アカウント間でデータをパーティション分割できます。 ボリューム価格については、「 [Azure Storage 料金 ](https://azure.microsoft.com/pricing/details/storage/) 」を参照してください。

すべてのストレージ アカウントは、いつ作成されたかに関係なく、フラット ネットワーク トポロジ上で実行されます。 Azure Storage フラット ネットワークのアーキテクチャとスケーラビリティの詳細については、[Microsoft Azure Storage: 強力な一貫性を備えた高可用性クラウド ストレージ サービス](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)を参照してください。 マルチプロトコル アクセス用のフラット型名前空間に加え、[階層型名前空間を Data Lake Gen2 アカウントに対して有効にすることができます](../articles/storage/blobs/data-lake-storage-namespace.md)。 フラット型および階層型名前空間のストレージ アカウントはどちらも、この記事で説明されているのと同じスケーラビリティとパフォーマンスのターゲットをサポートします。
