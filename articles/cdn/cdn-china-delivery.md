---
title: Azure CDN を使用した中国へのコンテンツ配信 | Microsoft Docs
description: Azure Content Delivery Network (CDN) を使用して、中国のユーザーにコンテンツを配信する方法について説明します。
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 59788f301bb501103babd55a2ac37102932f4dcf
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259691"
---
# <a name="china-content-delivery-with-azure-cdn"></a>Azure CDN を使用した中国へのコンテンツ配信

Azure Content Delivery Network (CDN) グローバルは、中国に近いポイント オブジェクト プレゼンス (POP) の場所を使用して、または中国から送信された要求に最適なパフォーマンスを提供する任意の POP を使用して、中国のユーザーにコンテンツを提供できます。 ただし、顧客にとって中国が重要な市場で、高速なパフォーマンスを必要としている場合は、代わりに Azure CDN China の使用を検討してください。

Azure CDN China は、多くのローカル プロバイダーと連携することで、中国国内の POP からコンテンツを配信するという点が、Azure CDN グローバルと異なります。 中国のコンプライアンスおよび規制により、Azure CDN China を使用するために個別のサブスクリプションを登録する必要があります。また、Web サイトには ICP ライセンスがある必要があります。 コンテンツ配信を有効にして管理するためのポータルおよび API エクスペリエンスは、Azure CDN グローバルと Azure CDN China とで同じです。

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Azure CDN グローバルと Azure CDN China の比較

Azure CDN グローバルと Azure CDN China には、次の機能があります。

- Azure CDN グローバル:

     - ポータル: https://portal.azure.com  

     - 中国国外でのコンテンツ配信の実行

     - 4 つの価格レベル: Microsoft 標準、Verizon 標準、Verizon プレミアム、Akamai 標準

     - [ドキュメント](https://docs.microsoft.com/en-us/azure/cdn/)

- Azure CDN China:

     - ポータル: https://portal.azure.cn

     - 中国国内でのコンテンツ配信の実行

     - 2 つの価格レベル: 標準とプレミアム

     - [ドキュメント](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>次の手順

Azure CDN China に関する詳細については、以下を参照してください。

- [Content Delivery Network の機能](https://www.azure.cn/en-us/home/features/cdn/)

- [Azure Content Delivery Network の概要](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Azure Content Delivery Network の使用](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [中国での Azure サービスの利用可能性](https://docs.microsoft.com/en-us/azure/china/china-get-started-service-availability)



