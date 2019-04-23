---
title: Azure Germany のストレージ サービス | Microsoft Docs
description: Azure Germany のストレージ サービスを比較します
services: germany
cloud: na
documentationcenter: na
author: gitralf
manager: rainerst
ms.assetid: na
ms.service: germany
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2017
ms.author: ralfwi
ms.openlocfilehash: 2a2a9242642c8bc661859e3c847467fd724e461a
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489786"
---
# <a name="azure-germany-storage-services"></a>Azure Germany のストレージ サービス
## <a name="storage"></a>Storage
Azure Storage とその使用方法の詳細については、[ストレージ グローバル ドキュメント](../storage/index.yml)を参照してください。

Azure Storage に格納されたデータは、高可用性を確保するためにレプリケートされます。 geo 冗長ストレージと読み取りアクセス geo 冗長ストレージの場合、Azure は*ペア リージョン*間でデータをレプリケートします。 Azure Germany の場合のこれらのペア リージョンを次に示します。

| プライマリ リージョン | セカンダリ (ペア) リージョン |
| --- | --- |
| ドイツ中部 | ドイツ北東部 |
| ドイツ北東部 | ドイツ中部 |

データのレプリケーションでは、ドイツ国境内にデータが維持されます。 プライマリ リージョンとセカンダリ リージョンが対になってデータセンター間の必要な距離を確保することにより、領域全体の停止または障害が発生した場合の可用性が確保されます。 geo 冗長高可用性ストレージの場合、ストレージ アカウントの作成時に、geo 冗長ストレージまたは読み取りアクセス geo 冗長ストレージのいずれかを選択します。  

Storage Service Encryption によって、Azure ストレージ アカウント内の保存データを保護します。 その機能を有効にすると、Azure によってストレージに永続化する前にデータが自動的に暗号化されます。 データは、256 ビット AES 暗号化によって暗号化されます。 Storage Service Encryption では、ブロック BLOB、追加 BLOB、ページ BLOB の暗号化をサポートしています。

### <a name="storage-service-availability-by-azure-germany-region"></a>Azure Germany リージョンごとのストレージ サービスの利用可能性

| Service | ドイツ中部 | ドイツ北東部 |
| --- | --- | --- |
| [BLOB ストレージ](../storage/common/storage-introduction.md#blob-storage) |一般公開 |一般公開 |
| [Azure Files](../storage/common/storage-introduction.md#azure-files) | 一般公開 | 一般公開 |
| [テーブル ストレージ](../storage/common/storage-introduction.md#table-storage) |一般公開  |一般公開 |
| [ストレージ](../storage/common/storage-introduction.md#queue-storage) |一般公開 | 一般公開 |
| [ホット/クール BLOB ストレージ](../storage/blobs/storage-blob-storage-tiers.md) |一般公開 |一般公開 |
| [Storage Service Encryption](../storage/common/storage-service-encryption.md) |一般公開 |一般公開 |
| Import/Export |NA |NA |
| StorSimple |NA |NA |

### <a name="variations"></a>バリエーション
Azure Germany でのストレージ アカウントの URL はグローバル Azure での URL と異なります。

| サービスの種類 | グローバル Azure | Azure Germany |
| --- | --- | --- |
| BLOB ストレージ | *.blob.core.windows.net | *.blob.core.cloudapi.de |
| Azure Files | *.file.core.windows.net | *.file.core.cloudapi.de | 
| ストレージ | *.queue.core.windows.net | *.queue.core.cloudapi.de |
| テーブル ストレージ | *.table.core.windows.net | *.table.core.cloudapi.de |

> [!NOTE]
> スクリプトやコードでは必ず、適切なエンドポイントを考慮する必要があります。 詳細については、「[Azure Storage の接続文字列を構成する](../storage/common/storage-configure-connection-string.md)」を参照してください。 
>
>

API の詳細については、[クラウド ストレージ アカウント コンストラクター](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.-ctor#Microsoft_WindowsAzure_Storage_CloudStorageAccount__ctor_Microsoft_WindowsAzure_Storage_Auth_StorageCredentials_System_String_System_String_System_Boolean_)に関するページを参照してください。

これらのオーバーロードに使用するエンドポイント サフィックスは、*core.cloudapi.de* です。

> [!NOTE]
> [ファイル共有のマウント](../storage/files/storage-dotnet-how-to-use-files.md)中に、エラー 53 ("ネットワーク パスが見つかりませんでした") が返された場合、ファイアウォールによって、送信ポートがブロックされている可能性がります。 ストレージ アカウントと同じ Azure サブスクリプション内の仮想マシンでファイル共有のマウントを試してみてください。
>
>


## <a name="next-steps"></a>次の手順
補足情報と更新情報については、[Azure Germany のブログ](https://blogs.msdn.microsoft.com/azuregermany/)を参照してください。
