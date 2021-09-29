---
title: Azure の予約の使用量に関する詳細をダウンロードするときに発生した問題のトラブルシューティング
description: この記事では、Azure portal で Reserved Instance 使用量の詳細をダウンロードできない問題について、その理由とトラブルシューティングの方法を説明します。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 09/15/2021
ms.openlocfilehash: 6b899f634c5fbaa1acd0493da2d7b97b54588157
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656932"
---
# <a name="troubleshoot-azure-reservation-download-usage-details"></a>Azure の予約の使用量に関する詳細をダウンロードするときに発生した問題のトラブルシューティング

この記事では、Azure portal で Reserved Instance 使用量の詳細をダウンロードできない問題について、その理由とトラブルシューティングの方法を説明します。

## <a name="symptoms"></a>現象

1. [Azure portal](https://portal.azure.com/) にサインインし、 **[予約]** に移動します。
1. 予約を選択します。
1. 予約の概要ページに、過去 7 日間の使用量が既定のビューで表示されます。 **[Download as CSV]\(CSV 形式でダウンロード\)** を選択すると、予約の使用量の詳細をダウンロードできます。
1. 時間の範囲を変更すると、 **[Download as CSV]\(CSV 形式でダウンロード\)** オプションが利用不可になります。
    :::image type="content" source="./media/troubleshoot-download-usage/download-csv-unavailable.png" alt-text="[Download as CSV]\(CSV 形式でダウンロード\) が利用不可になっている画面の例" lightbox="./media/troubleshoot-download-usage/download-csv-unavailable.png" :::

## <a name="cause"></a>原因

Azure では、7 日を超える期間については、技術的な制限によりデータのダウンロードがサポートされません。 多数の予約を利用するユーザーの場合、期間が長いと大量のデータが生成されます。 API から返されるデータによって、Azure リソースに負荷がかかります。

## <a name="solution"></a>解決策

Microsoft は、より長期間のデータをダウンロードしたいと考えるお客様のニーズを把握しております。 しかし現時点では、現状を超える期間について、予約の利用状況データをダウンロードする方法がありません。

## <a name="next-steps"></a>次のステップ

- 予約の詳細については、「[Azure の予約とは](save-compute-costs-reservations.md)」を参照してください。