---
title: Blob Storage の概要 - Azure のオブジェクト ストレージ | Microsoft Docs
description: Azure Blob Storage は、テキスト データやバイナリ データなどの大量の非構造化オブジェクト データを格納するよう設計されています。 アプリケーションは、PowerShell または Azure CLI、Azure Storage クライアント ライブラリ経由のコード、あるいは REST で Blob Storage のオブジェクトにアクセスできます。
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: overview
ms.date: 03/27/2018
ms.author: tamram
ms.openlocfilehash: 0fff0032ec2452413bcd1df3175634b14a64208f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-blob-storage"></a>Blob Storage の概要

Azure Blob Storage は、データ オブジェクト用の Microsoft のクラウド ストレージ ソリューションです。 Blob Storage は、テキスト データやバイナリ データなどの大量の非構造化オブジェクト データを格納できます。 Blob Storage のデータには、世界中のどこからでも HTTP または HTTPS 経由でアクセスできます。 BLOB ストレージを使用すると、データを一般に公開することも、アプリケーション データを公開せずに格納することもできます。

BLOB ストレージの一般的な用途には、次のようなものがあります。

* 画像またはドキュメントをブラウザーに直接配信する
* 分散アクセス用にファイルを格納する
* ビデオおよびオーディオをストリーミング配信する
* バックアップと復元、ディザスター リカバリー、アーカイブのために格納する
* 内部設置型サービスまたは Azure ホステッド サービスで分析する データを格納する
* Azure Virtual Machines で使用する VHD を格納する

## <a name="blob-service-concepts"></a>Blob service の概念

Blob service には、次のコンポーネントが含まれます。

![BLOB アーキテクチャ](./media/storage-blobs-introduction/blob1.png)

* **ストレージ アカウント** : Azure Storage にアクセスする場合には必ず、ストレージ アカウントを使用します。 このストレージ アカウントは **汎用ストレージ アカウント (v1 または v2)** または **BLOB ストレージ アカウント**のいずれでもかまいません。 詳細については、「[Azure ストレージ アカウントについて](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。

* **コンテナー** : コンテナーは、BLOB のセットをグループ化します。 すべての BLOB はコンテナーに格納されている必要があります。 1 つのアカウントに格納できるコンテナーの数は無制限です。 また、1 つのコンテナーに保存できる BLOB の数も無制限です。 コンテナー名は小文字で入力する必要があります。

* **BLOB** : 任意の種類およびサイズのファイルです。 Azure Storage は、ブロック BLOB、[ページ BLOB](storage-blob-pageblob-overview.md)、および追加 BLOB の 3 種類の BLOB を提供します。
  
    *ブロック BLOB* は、ドキュメントやメディア ファイルなどのテキストまたはバイナリ ファイルを格納するのに最適です。 *追加 BLOB* はブロック BLOB と同様にブロックで構成されますが、追加操作用に最適化されているので、ログ記録シナリオで役立ちます。 1 つのブロック BLOB は、100 MB までのブロックを最大 50,000 個まで含めることができます。合計サイズは 4.75 TB を少し上回ります (100 MB x 50,000)。 1 つの追加 BLOB は、4 MB までのブロックを最大 50,000 個まで含めることができます。合計サイズは 195 GB を少し上回ります (4 MB x 50,000)。
  
    *ページ BLOB* は最大 8 TB のサイズにすることができます。読み取り/書き込み操作を頻繁に実行する場合はこの BLOB が効率的です。 Azure の仮想マシンでは、ページ BLOB を OS とデータ ディスクとして使用します。
  
    コンテナーと BLOB の名前付け規則については、「 [コンテナー、BLOB、およびメタデータの名前付けおよび参照](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)」を参照してください。

## <a name="next-steps"></a>次の手順

* [ストレージ アカウントの作成](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [.NET を使用して Blob Storage を使用する](storage-dotnet-how-to-use-blobs.md)
* [.NET を使用した Azure Storage サンプル](../common/storage-samples-dotnet.md)
* [Java を使用した Azure Storage サンプル](../common/storage-samples-java.md)
