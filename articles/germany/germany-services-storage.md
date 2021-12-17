---
title: Azure Germany のストレージ サービス | Microsoft Docs
description: このトピックでは、Azure Germany のストレージ サービスを比較します その他の関連情報も記載されています。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 840ac16914196a238bcf8fdaaa518799dcb104e9
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275033"
---
# <a name="azure-germany-storage-services"></a>Azure Germany のストレージ サービス

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="storage"></a>記憶域
Azure Storage とその使用方法の詳細については、[ストレージ グローバル ドキュメント](../storage/index.yml)を参照してください。

Azure Storage に格納されたデータは、高可用性を確保するためにレプリケートされます。 geo 冗長ストレージと読み取りアクセス geo 冗長ストレージの場合、Azure は *ペア リージョン* 間でデータをレプリケートします。 Azure Germany の場合のこれらのペア リージョンを次に示します。

| プライマリ リージョン | セカンダリ (ペア) リージョン |
| --- | --- |
| ドイツ中部 | ドイツ北東部 |
| ドイツ北東部 | ドイツ中部 |

データのレプリケーションでは、ドイツ国境内にデータが維持されます。 プライマリ リージョンとセカンダリ リージョンが対になってデータセンター間の必要な距離を確保することにより、領域全体の停止または障害が発生した場合の可用性が確保されます。 geo 冗長高可用性ストレージの場合、ストレージ アカウントの作成時に、geo 冗長ストレージまたは読み取りアクセス geo 冗長ストレージのいずれかを選択します。  

Storage Service Encryption によって、Azure ストレージ アカウント内の保存データを保護します。 その機能を有効にすると、Azure によってストレージに永続化する前にデータが自動的に暗号化されます。 データは、256 ビット AES 暗号化によって暗号化されます。 Storage Service Encryption では、ブロック BLOB、追加 BLOB、ページ BLOB の暗号化をサポートしています。

### <a name="storage-service-availability-by-azure-germany-region"></a>Azure Germany リージョンごとのストレージ サービスの利用可能性

| サービス | ドイツ中部 | ドイツ北東部 |
| --- | --- | --- |
| [Blob Storage](../storage/common/storage-introduction.md#blob-storage) |GA |GA |
| [Azure Files](../storage/common/storage-introduction.md#azure-files) | GA | GA |
| [Table Storage](../storage/common/storage-introduction.md#table-storage) |GA  |GA |
| [Queue Storage](../storage/common/storage-introduction.md#queue-storage) |GA | GA |
| [ホット/クール BLOB ストレージ](../storage/blobs/access-tiers-overview.md) |GA |GA |
| [Storage Service Encryption](../storage/common/storage-service-encryption.md) |GA |GA |
| インポート/エクスポート |NA |NA |
| StorSimple |NA |NA |

### <a name="variations"></a>バリエーション
Azure Germany でのストレージ アカウントの URL はグローバル Azure での URL と異なります。

| サービスの種類 | グローバル Azure | Azure Germany |
| --- | --- | --- |
| Blob Storage | *.blob.core.windows.net | *.blob.core.cloudapi.de |
| Azure Files | *.file.core.windows.net | *.file.core.cloudapi.de | 
| ストレージ | *.queue.core.windows.net | *.queue.core.cloudapi.de |
| テーブル ストレージ | *.table.core.windows.net | *.table.core.cloudapi.de |

> [!NOTE]
> スクリプトやコードでは必ず、適切なエンドポイントを考慮する必要があります。 詳細については、「[Azure Storage の接続文字列を構成する](../storage/common/storage-configure-connection-string.md)」を参照してください。 
>
>

API の詳細については、[クラウド ストレージ アカウント コンストラクター](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.-ctor)に関するページを参照してください。

これらのオーバーロードに使用するエンドポイント サフィックスは、*core.cloudapi.de* です。

> [!NOTE]
> [ファイル共有のマウント](../storage/files/storage-dotnet-how-to-use-files.md)中に、エラー 53 ("ネットワーク パスが見つかりませんでした") が返された場合、ファイアウォールによって、送信ポートがブロックされている可能性がります。 ストレージ アカウントと同じ Azure サブスクリプション内の仮想マシンでファイル共有のマウントを試してみてください。
>
>


## <a name="next-steps"></a>次のステップ
補足情報と更新情報については、[Azure Germany のブログ](/archive/blogs/azuregermany/)を参照してください。