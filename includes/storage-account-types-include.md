---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6e8c0e1c7fef884844b8aaae9dc4c7e3eaa220a2
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274549"
---
Azure Storage では、数種類のストレージ アカウントが提供されています。 各種類は異なる機能をサポートし、独自の価格モデルがあります。 アプリケーションに最適な種類を判断するために、ストレージ アカウントを作成する前にこれらの違いを検討してください。 ストレージ アカウントには、次の種類があります。

- **汎用 v2 アカウント**:BLOB、ファイル、キュー、およびテーブル用の基本的なストレージ アカウントの種類。 Azure Storage を使用するほとんどのシナリオにお勧めします。
- **汎用 v1 アカウント**:BLOB、ファイル、キュー、およびテーブル用の従来のアカウントの種類。 可能な場合は汎用 v2 アカウントを代わりに使用してください。
- **BlockBlobStorage アカウント**:ブロック BLOB と追加 BLOB の Premium パフォーマンス特性を持つストレージ アカウント。 トランザクション レートが高く、比較的小さなオブジェクトが使用されるシナリオ、またはストレージ待ち時間が一貫して短いことが要求されるシナリオに推奨されます。
- **FileStorage アカウント**:Premium パフォーマンスの特徴を持つファイル専用ストレージ アカウント。 エンタープライズまたはハイ パフォーマンス スケール アプリケーションにお勧めします。
- **BlobStorage アカウント**:従来の BLOB 専用ストレージ アカウント。 可能な場合は汎用 v2 アカウントを代わりに使用してください。

次の表は、ストレージ アカウントの種類とその機能について説明しています。

| ストレージ アカウントの種類 | サポートされているサービス                       | サポートされているパフォーマンス レベル      | サポートされているアクセス層         | レプリケーション オプション               | デプロイメント モデル<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | 暗号化<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| 汎用 v2   | BLOB、ファイル、キュー、テーブル、ディスク、および Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard、Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | ホット、クール、アーカイブ<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS、GRS、RA-GRS、ZRS、GZRS (プレビュー)、RA-GZRS (プレビュー)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | リソース マネージャー             | Encrypted              |
| 汎用 v1   | BLOB、ファイル、キュー、テーブル、およびディスク       | Standard、Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | 該当なし                            | LRS、GRS、RA-GRS                  | Resource Manager、クラシック    | Encrypted              |
| BlockBlobStorage   | BLOB (ブロック BLOB と追加 BLOB のみ) | Premium                       | 該当なし                            | LRS、ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | リソース マネージャー             | Encrypted              |
| FileStorage   | ファイルのみ | Premium                       | 該当なし                            | LRS、ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | リソース マネージャー             | Encrypted              |
| BlobStorage         | BLOB (ブロック BLOB と追加 BLOB のみ) | Standard                      | ホット、クール、アーカイブ<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS、GRS、RA-GRS                  | リソース マネージャー             | Encrypted              |

<div id="deployment-model"><sup>1</sup> Azure Resource Manager デプロイ モデルが推奨されます。 一部の場所では、クラシック デプロイ モデルを使用するストレージ アカウントを引き続き作成でき、既存のクラシック アカウントもサポートされます。 詳細については、「<a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager とクラシック デプロイ:デプロイ モデルとリソースの状態について</a>」を参照してください。</div>

<div id="encryption"><sup>2</sup> すべてのストレージ アカウントは、保存データ用の Storage Service Encryption (SSE) を使用して暗号化されます。 詳細については、「<a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">保存データ向け Azure Storage Service Encryption</a>」をご覧ください。</div>

<div id="archive"><sup>3</sup> アーカイブ 層は、個々の BLOB のレベルでのみ利用でき、ストレージ アカウント レベルでは利用できません。 ブロック BLOB と追加 BLOB のみをアーカイブできます。 詳細については、「<a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure BLOB ストレージ: ホット ストレージ層、クール ストレージ層、アーカイブ ストレージ層</a>」をご覧ください。</div>

<div id="zone-redundant-storage"><sup>4</sup> ゾーン冗長ストレージ (ZRS) および geo ゾーン冗長ストレージ (GZRS/RA-GZRS) (プレビュー) は、特定のリージョンの Standard 汎用 V2、BlockBlobStorage、および FileStorage アカウントでのみ使用できます。 ZRS に関する詳細については、「<a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">ゾーン冗長ストレージ (ZRS): 高可用 Azure Storage アプリケーション</a>」をご覧ください。 GZRS/RA-GZRS の詳細については、<a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-gzrs">高可用性と最大限の持続性のための geo ゾーン冗長ストレージ (プレビュー)</a> に関する記事を参照してください。 レプリケーション オプションの詳細については、「<a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure Storage のレプリケーション</a>」を参照してください。</div>

<div id="premium-performance">汎用 v2 および汎用 V1 アカウント用の <sup>5</sup> Premium パフォーマンスは、ディスクとページ BLOB でのみ使用できます。 ブロックまたは BLOB の追加用の Premium パフォーマンスは、BlockBlobStorage アカウントでのみ使用できます。 ファイル用の Premium パフォーマンスは、FileStorage アカウントでのみ使用できます。</div>

<div id="data-lake-gen2"><sup>6</sup>Azure Data Lake Storage Gen2 は、Azure Blob ストレージを基礎に構築されたビッグ データ分析専用の機能セットです。 Data Lake Storage Gen2 は、階層型名前空間が有効になった汎用の V2 ストレージ アカウントでのみサポートされています。 Data Lake Storage Gen2 の詳細については、「<a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2 の概要</a>」を参照してください。</div>
