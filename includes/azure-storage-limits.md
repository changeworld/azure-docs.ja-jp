---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e4fa42b6c32c3eb383eea4489ea109c0d496bdb9
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2019
ms.locfileid: "54392729"
---
次の表は、Azure Storage の既定の制限について説明しています。 *受信*制限は、ストレージ アカウントに送信されるすべてのデータ (要求) を指します。 *送信*制限は、ストレージ アカウントから受信するすべてのデータ (応答) を指します。

| リソース | 既定の制限 |
| --- | --- |
| サブスクリプションあたりの各リージョンのストレージ アカウント数 (Standard アカウントと Premium アカウント両方を含む) | 250 |
| ストレージ アカウントの最大容量 | 米国とヨーロッパでは 2 PB、英国を含む他のすべてのリージョンでは 500 TB |
| ストレージ アカウントあたりの BLOB コンテナー、BLOB、ファイル共有、テーブル、キュー、エンティティ、メッセージの最大数 | 制限なし |
| ストレージ アカウントあたりの最大要求レート<sup>1</sup> | 1 秒あたり 20,000 要求 |
| ストレージ アカウントあたりの最大受信速度<sup>1</sup> (米国リージョン) | RA-GRS/GRS が有効な場合は 10 Gbps、LRS/ZRS<sup>2</sup> の場合は 20 Gbps |
| ストレージ アカウントあたりの最大受信速度<sup>1</sup> (米国以外のリージョン) | RA-GRS/GRS が有効な場合は 5 Gbps、LRS/ZRS<sup>2</sup> の場合は 10 Gbps |
| 汎用 v2 および BLOB ストレージ アカウントの最大エグレス (すべてのリージョン) | 50 Gbps |
| 汎用 v1 ストレージ アカウントの最大エグレス (米国リージョン) | RA-GRS/GRS が有効な場合は 20 Gbps、LRS/ZRS の場合は 30 Gbps <sup>2</sup> |
| 汎用 v1 ストレージ アカウントの最大送信速度 (米国以外のリージョン) | RA-GRS/GRS が有効な場合は 10 Gbps、LRS/ZRS の場合は 15 Gbps <sup>2</sup> |

<sup>1</sup> Azure Standard ストレージ アカウントでは、要求によりさらに高いイングレス制限がサポートされます。 イングレスのアカウント制限の引き上げを要求する場合は、[Azure サポートにお問い合わせください](https://azure.microsoft.com/support/faq/)。

<sup>2</sup> [Azure Storage のレプリケーション](https://docs.microsoft.com/azure/storage/common/storage-redundancy)には次のオプションがあります。
* **RA-GRS**: 読み取りアクセス geo 冗長ストレージ。 RA-GRS が有効な場合、2 次拠点への送信ターゲットは、1 次拠点と同じになります。
* **GRS**: geo 冗長ストレージ。 
* **ZRS**: ゾーン冗長ストレージ。
* **LRS**: ローカル冗長ストレージ。 

> [!NOTE]
> Microsoft では、ほとんどのシナリオで汎用 v2 ストレージ アカウントを使用することをお勧めしています。 汎用 v1 または BLOB ストレージ アカウントは汎用 v2 アカウントに簡単にアップグレードできます。そのとき、ダウンタイムは発生せず、データをコピーする必要はありません。
>
> Azure Storage アカウントの詳細については、[Storage アカウントの概要](../articles/storage/common/storage-account-overview.md)に関するページを参照してください。 

アプリケーションで必要とされるスケーラビリティが、単一ストレージ アカウントあたりのスケーラビリティ ターゲットを超えている場合は、複数のストレージ アカウントを使用するようにアプリケーションを構築できます。 その後、それらのストレージ アカウント間でデータをパーティション分割できます。 ボリューム価格については、「 [Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/) 」をご覧ください。

すべてのストレージ アカウントはフラット ネットワーク トポロジで実行され、ストレージ アカウントがいつ作成されたかにかかわらず、この記事で概要を説明するスケーラビリティとパフォーマンスのターゲットがサポートされます。 Azure Storage フラット ネットワークのアーキテクチャとスケーラビリティの詳細については、[Microsoft Azure Storage: 強力な一貫性を備えた高使用可能なクラウド ストレージ サービス](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)を参照してください。

