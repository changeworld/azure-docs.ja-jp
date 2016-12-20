---
title: Azure Government Storage | Microsoft Docs
description: "Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 83df022b-d791-4efb-9fdf-8afe47a885d5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/13/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 004860b319b7b32955415ac2d6c7308c32037848
ms.openlocfilehash: 36312ce195e931d174e00d54cab4cb071a85b618


---
# <a name="azure-government-storage"></a>Azure Government ストレージ
## <a name="azure-storage"></a>Azure Storage (Azure Storage)
このサービスとその使用方法について詳しくは、[Azure Storage のパブリック ドキュメント](../storage/index.md)をご覧ください。

### <a name="variations"></a>バリエーション
Azure Government では、ストレージ アカウントの URL が異なります。

| サービスの種類 | Azure Public | Azure Government |
| --- | --- | --- |
| BLOB ストレージ |*.blob.core.windows.net |*.blob.core.usgovcloudapi.net |
| キュー ストレージ |*.queue.core.windows.net |*.queue.core.usgovcloudapi.net |
| Table Storage |*.table.core.windows.net |*.table.core.usgovcloudapi.net |

> [!NOTE]
> スクリプトやコードでは必ず、適切なエンドポイントを考慮する必要があります。  「[Azure Storage の接続文字列を構成する](../storage/storage-configure-connection-string.md)」を参照してください。 
>
>

API の詳細については、<a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">クラウド ストレージ アカウント コンストラクター</a>に関するページを参照してください。

これらのオーバーロードに使用するエンドポイント サフィックスは、core.usgovcloudapi.net です。

### <a name="considerations"></a>考慮事項
以下の情報は、Azure Storage に関する Azure Government の機能領域について記述したものです。

| 許可される規制対象データ | 許可されない規制対象データ |
| --- | --- |
| Azure Storage 製品内で入力、保存、処理されるデータには、輸出規制対象データが含まれていてもかまいません。 静的な認証コード (Azure のプラットフォーム コンポーネントにアクセスするためのパスワード、スマートカードの PIN など)。 Azure のプラットフォーム コンポーネントを管理する際に使用する証明書の秘密キー。 その他、Azure サービスに格納される各種セキュリティ情報/シークレット (証明書、暗号化キー、マスター キー、ストレージ キーなど)。 |Azure Storage のメタデータに、輸出規制対象データを含めることは許可されません。 このメタデータには、ストレージ製品を作成したり管理したりする際に入力するあらゆる構成データが含まれます。  リソース グループ、デプロイ名、リソース名、リソース タグの各フィールドには規制対象データを入力しないでください。 |

## <a name="premium-storage"></a>Premium Storage
このサービスとその使用方法について詳しくは、「[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage/storage-premium-storage.md)」をご覧ください。

### <a name="variations"></a>バリエーション
Premium Storage は、米国政府バージニア州で一般提供中となっています。 これには、DS シリーズの仮想マシンが含まれます。

### <a name="considerations"></a>考慮事項
ストレージ データに関して上に挙げた考慮事項は、Premium ストレージ アカウントにも適用されます。

## <a name="next-steps"></a>次のステップ
補足情報と最新情報については、<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>を講読してください。



<!--HONumber=Nov16_HO3-->


