---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/25/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a777151216cb70b696da088b8a0d34779ebc39
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47396129"
---
次の表は、Azure Storage の既定の制限について説明しています。 *受信*制限は、ストレージ アカウントに送信されるすべてのデータ (要求) を指します。 *送信*制限は、ストレージ アカウントから受信するすべてのデータ (応答) を指します。

| リソース | 既定の制限 |
| --- | --- |
| サブスクリプションあたりの各リージョンのストレージ アカウント数 (Standard アカウントと Premium アカウント両方を含む) | 200 |
| ストレージ アカウントの最大容量<sup>1</sup> | 500 TiB |
| ストレージ アカウントあたりの BLOB コンテナー、BLOB、ファイル共有、テーブル、キュー、エンティティ、メッセージの最大数 | 制限なし |
| ストレージ アカウントあたりの最大要求レート<sup>1</sup> | 1 秒あたり 20,000 要求 |
| ストレージ アカウントあたりの最大受信速度<sup>1</sup> (米国リージョン) | RA-GRS/GRS が有効な場合は 10 Gbps、LRS/ZRS<sup>2</sup> の場合は 20 Gbps |
| ストレージ アカウントあたりの最大送信速度<sup>1</sup> (米国リージョン) | RA-GRS/GRS が有効な場合は 20 Gbps、LRS/ZRS の場合は 30 Gbps |
| ストレージ アカウントあたりの最大受信速度<sup>1</sup> (米国以外のリージョン) | RA-GRS/GRS が有効な場合は 5 Gbps、LRS/ZRS<sup>2</sup> の場合は 10 Gbps |
| ストレージ アカウントあたりの最大送信速度<sup>1</sup> (米国以外のリージョン) | RA-GRS/GRS が有効な場合は 10 Gbps、LRS/ZRS の場合は 15 Gbps |

<sup>1</sup> Azure ストレージ アカウントは、要求ごとの容量、要求レート、入力速度、および出力速度の上限をサポートしています。 制限の引き上げの詳細については、「[Announcing larger, higher scale storage accounts](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)」(より大規模なストレージ アカウントに関するお知らせ) を参照してください。 アカウント制限の引き上げを希望する場合は、[Azure サポートにお問い合わせください](https://azure.microsoft.com/support/faq/)。

<sup>2</sup>[Azure Storage のレプリケーション](https://docs.microsoft.com/azure/storage/common/storage-redundancy)には次のオプションがあります。
* **RA-GRS**: 読み取りアクセス地理冗長ストレージ。 RA-GRS が有効な場合、2 次拠点への送信ターゲットは、1 次拠点と同じになります。
* **GRS**: 地理冗長ストレージ。 
* **ZRS**: ゾーン冗長ストレージ。
* **LRS**: ローカル冗長ストレージ。 

