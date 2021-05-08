---
title: Azure Storage サービスに予約割引が適用されるしくみについて | Microsoft Docs
description: Azure Blob Storage、Azure Files、Azure Data Lake Storage Gen2 の各リソースに予約容量割引が適用されるしくみについて説明します。
author: tamram
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: tamram
ms.openlocfilehash: f18097f0da658b3a485b79f5279e9ecc54a41ea0
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024032"
---
# <a name="understand-how-reservation-discounts-are-applied-to-azure-storage-services"></a>Azure Storage サービスに予約割引が適用されるしくみについて 
Azure Storage サービスでは、容量を予約することでストレージ コストを節約することができます。 Azure Blob Storage、Azure Files、Azure Data Lake Storage Gen 2 では、予約インスタンスがサポートされます。 予約容量を購入すると、予約の条件に一致するストレージ リソースに予約割引が自動的に適用されます。 予約割引は、ストレージ容量にのみ適用されます。 帯域幅と要求レートは従量課金制で課金されます。

Azure Blob Storage と Azure Data Lake Storage Gen 2 の予約容量の詳細については、「[予約容量を使用して BLOB ストレージのコストを最適化する](../../storage/blobs/storage-blob-reserved-capacity.md)」を参照してください。 Azure Files の予約容量の詳細については、「[予約容量を使用して Azure Files のコストを最適化する](../../storage/files/files-reserve-capacity.md)」を参照してください。

Azure Blob Storage の容量予約の価格については、「[ブロック BLOB の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)」と「[Azure Data Lake Storage Gen 2 の価格](https://azure.microsoft.com/pricing/details/storage/data-lake/)」を参照してください。 Azure Files ストレージ予約の価格の詳細については、「[Azure Files の料金](https://azure.microsoft.com/pricing/details/storage/files)」を参照してください。

## <a name="how-the-reservation-discount-is-applied"></a>予約割引の適用方法
予約容量割引は、サポートされているストレージ リソースに 1 時間単位で適用されます。

予約容量割引は、"使用しないと失われる" 割引です。 特定の 1 時間に予約の条件を満たすブロック BLOB、Azure ファイル共有、または Azure Data Lake Storage Gen2 リソースがない場合、その時間に対する予約量は失われます。 未使用の予約済み時間を繰り越すことはできません。

リソースを削除すると、指定されたスコープ内の別の一致するリソースに予約割引が自動的に適用されます。 指定したスコープ内に一致するリソースが見つからない場合、予約済み時間は失われます。

## <a name="discount-examples"></a>割引の例
以下の例は、デプロイに応じて、予約容量割引がどのように適用されるかを示しています。

米国西部 2 リージョンで 1 年間 100 TiB の予約容量を購入したとします。 この予約は、ローカル冗長ストレージ (LRS) の Blob Storage のホット アクセス層に適用されます。

このサンプル予約のコストは 18,540 ドルであると仮定します。 次の 12 か月間について、全額を支払うか、月あたり 1,545 ドルの固定月額割賦払いを行うかを選択できます。

以下の例では、毎月の予約支払いプランに登録したと仮定します。 以下のシナリオでは、予約容量の使用量が過少または過多だった場合にどうなるかを説明します。

### <a name="underusing-your-capacity"></a>容量の使用量が過少だった場合
予約期間内の特定の 1 時間に、100 TiB の予約容量のうち 80 TiB のみを使用したとします。 残りの 20 TiB は、その時間には適用されず、引き継がれません。

### <a name="overusing-your-capacity"></a>容量の使用量が過多だった場合
予約期間内の特定の 1 時間に、101 TiB のストレージ容量を使用したとします。 100 TiB のデータについては予約割引が適用され、残りの 1 TiB についてはその時間の従量課金制料金で課金されます。 次の 1 時間の使用量が 100 TiB に変わった場合は、すべての使用量が予約の対象になります。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ
ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ
- [予約容量を使用して BLOB ストレージのコストを最適化する](../../storage/blobs/storage-blob-reserved-capacity.md)
- [予約容量を使用して Azure Files のコストを最適化する](../../storage/files/files-reserve-capacity.md)
- [Azure の予約とは](save-compute-costs-reservations.md)
