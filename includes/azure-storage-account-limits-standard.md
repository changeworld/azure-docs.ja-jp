---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/13/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6cd883289513091ff1a57a130b12e25e012c1160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334600"
---
次の表では、Azure 汎用 v1、v2、BLOB ストレージ、ブロック Blob ストレージ、および Data Lake Gen2 対応のストレージ アカウントの既定の制限について説明します。 "*受信*" 制限は、ストレージ アカウントに送信されるすべてのデータを指します。 *送信*制限は、ストレージ アカウントから受信するすべてのデータを指します。

| リソース | 制限 |
| --- | --- |
| Standard、Premium、Data Lake Gen2 対応のストレージ アカウントを含むサブスクリプションあたりの各リージョンのストレージ アカウント数。<sup>3</sup> | 250 |
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

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) は、Azure Blob Storage をベースに構築された、ビッグ データ分析専用の一連の機能です。 Azure Storage と BLOB ストレージの制限は、Data Lake Storage Gen2 に適用されます。

> [!NOTE]
> Microsoft では、ほとんどのシナリオで汎用 v2 ストレージ アカウントを使用することをお勧めしています。 汎用 v1 または Azure BLOB ストレージ アカウントは汎用 v2 アカウントに簡単にアップグレードできます。その際にダウンタイムは発生せず、データをコピーする必要はありません。 詳細については、「[汎用 v2 ストレージ アカウントにアップグレードする](../articles/storage/common/storage-account-upgrade.md)」を参照してください。

アプリケーションで必要とされるスケーラビリティが、単一ストレージ アカウントあたりのスケーラビリティ ターゲットを超えている場合は、複数のストレージ アカウントを使用するようにアプリケーションを構築できます。 その後、それらのストレージ アカウント間でデータをパーティション分割できます。 ボリューム価格については、「 [Azure Storage 料金 ](https://azure.microsoft.com/pricing/details/storage/) 」を参照してください。

すべてのストレージ アカウントは、いつ作成されたかに関係なく、フラット ネットワーク トポロジ上で実行されます。 Azure Storage フラット ネットワークのアーキテクチャとスケーラビリティの詳細については、[Microsoft Azure Storage: 強力な一貫性を備えた高可用性クラウド ストレージ サービス](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets)を参照してください。 
