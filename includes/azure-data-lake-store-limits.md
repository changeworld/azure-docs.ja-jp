---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 7098f23e9b5b6f56fbbe761335afc65375aea680
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96509479"
---
**Azure Data Lake Storage Gen2** は、専用サービスまたはストレージ アカウントの種類ではありません。 これは、ビッグ データ分析専用の機能の最新リリースです。  これらの機能は、汎用 v2 または BlockBlobStorage ストレージ アカウントで利用でき、アカウントの **階層型名前空間** 機能を有効にすることで取得できます。 スケール ターゲットについては、これらの記事を参照してください。 

- [BLOB ストレージのスケール ターゲット](../articles/storage/blobs/scalability-targets.md#scale-targets-for-blob-storage)。
- [Standard ストレージ アカウントのスケール ターゲット](../articles/storage/common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#scale-targets-for-standard-storage-accounts)。

**Azure Data Lake Storage Gen1** は専用サービスです。 これは、ビッグ データの分析ワークロードに対応するエンタープライズ規模のハイパースケール リポジトリです。 Data Lake Storage Gen1 を使用すると、運用分析や調査分析を目的として任意のサイズ、種類、および取り込み速度のデータを 1 か所でキャプチャすることができます。 Data Lake Storage Gen1 アカウントに格納できるデータ量に制限はありません。

| **リソース** | **制限** | **コメント** |
| --- | --- | --- |
| サブスクリプションあたり、リージョンあたりの Data Lake Storage Gen1 の最大数 |10 | この制限の引き上げを要求する場合は、サポートにお問い合わせください。 |
| ファイルまたはフォルダーあたりのアクセス ACL の最大数 |32 | これはハード リミットです。 少ないエントリでアクセスを管理するには、グループを使用します。 |
| ファイルまたはフォルダーあたりの既定 ACL の最大数 |32 | これはハード リミットです。 少ないエントリでアクセスを管理するには、グループを使用します。 |