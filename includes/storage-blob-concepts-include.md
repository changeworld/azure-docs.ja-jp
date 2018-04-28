---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 203f5a766c4c8a8f1e577f6be1e18d0f9ac95403
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
Azure Blob Storage は、Microsoft のクラウド用オブジェクト ストレージ ソリューションです。 BLOB ストレージは、テキスト データやバイナリ データなどの大量の非構造化データを格納するために最適化されています。

BLOB ストレージは、次の目的に最適です。

* 画像またはドキュメントをブラウザーに直接配信する。
* 分散アクセス用にファイルを格納する。
* ビデオおよびオーディオをストリーミング配信する。
* ログ ファイルに書き込む。
* バックアップと復元、ディザスター リカバリー、アーカイブのためのデータを格納する。
* オンプレミス サービスまたは Azure ホステッド サービスで分析するデータを格納する。

BLOB ストレージ内のオブジェクトには、世界中のどこからでも HTTP または HTTPS 経由でアクセスできます。 ユーザーまたはクライアント アプリケーションは、URL、[Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage)、[Azure CLI](https://docs.microsoft.com/cli/azure/storage)、または Azure Storage クライアント ライブラリを介して BLOB にアクセスできます。 ストレージ クライアント ライブラリは、[.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client)、[Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)、[Node.js](http://azure.github.io/azure-storage-node)、[Python](https://azure-storage.readthedocs.io/en/latest/index.html)、[PHP](http://azure.github.io/azure-storage-php/)、[Ruby](http://azure.github.io/azure-storage-ruby) など、さまざまな言語で利用できます。

## <a name="blob-service-concepts"></a>Blob service の概念

BLOB ストレージでは、ストレージ アカウント、アカウント内のコンテナー、およびコンテナー内の BLOB の 3 つのリソースが公開されます。 次の図に、これらのリソースの関係を示します。

![BLOB (オブジェクト) ストレージのアーキテクチャ図](./media/storage-blob-concepts-include/blob1.png)

### <a name="storage-account"></a>ストレージ アカウント

Azure Storage 内のデータ オブジェクトへのアクセスはすべて、ストレージ アカウント経由で行います。 詳細については、「[Azure ストレージ アカウントについて](../articles/storage/common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。

### <a name="container"></a>コンテナー

ファイル システムのフォルダーと同様に、コンテナーを使用して BLOB のセットを整理できます。 BLOB はすべてコンテナー内に格納されます。 ストレージ アカウントに含めることができるコンテナーの数には制限がなく、1 つのコンテナーに格納できる BLOB の数にも制限はありません。 コンテナー名は小文字で入力する必要があります。

### <a name="blob"></a>BLOB
 
Azure Storage が提供する BLOB には、ブロック BLOB、追加 BLOB、および[ページ BLOB](../articles/storage/blobs/storage-blob-pageblob-overview.md) (VHD ファイルに使用します) の 3 種類があります。

* ブロック BLOB には、約 4.7 TB までのテキストおよびバイナリ データを格納できます。 ブロック BLOB は、個別に管理できるデータ ブロックで構成されます。
* 追加 BLOB は、ブロック BLOB と同様にブロックで構成されますが、追加操作に最適化されています。 追加 BLOB は、仮想マシンのデータのログ記録などのシナリオに最適です。
* ページ BLOB には、最大 8 TB のランダム アクセス ファイルを格納できます。 ページ BLOB には、VM に使用される VHD ファイルが格納されます。

BLOB はすべてコンテナー内に格納されます。 コンテナーは、ファイル システムのフォルダーに似ています。 さらに BLOB を仮想ディレクトリに整理して、ファイル システムと同様に走査することができます。 

ネットワーク上の制限があるために、有線接続経由で Blob Storage にデータをアップロードまたはダウンロードできない場合は、一連のハード ドライブを Microsoft に送付し、データ センターから直接データをインポートまたはエクスポートできます。 詳しくは、「[Microsoft Azure Import/Export サービスを使用した BLOB ストレージへのデータの転送](../articles/storage/common/storage-import-export-service.md)」をご覧ください。
  
コンテナーと BLOB の名前付け規則については、「 [コンテナー、BLOB、およびメタデータの名前付けおよび参照](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)」を参照してください。
