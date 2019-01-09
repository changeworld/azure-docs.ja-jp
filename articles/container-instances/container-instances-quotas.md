---
title: Azure Container Instances のクォータとリージョンの可用性
description: Azure Container Instances サービスの既定のクォータとリージョンの可用性。
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 12/17/2018
ms.author: danlep
ms.openlocfilehash: 86c169c84e905362e8fdb069e8ceadcb5e19fe67
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53602394"
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Azure Container Instances のクォータとリージョンの可用性

すべての Azure サービスには、リソースと機能に対する特定の既定の制限とクォータが含まれます。 次のセクションでは、いくつかの Azure Container Instances (ACI) リソースに対する既定のリソースの制限と、Azure リージョンでの ACI サービスの可用性について詳しく説明します。

## <a name="service-quotas-and-limits"></a>サービスのクォータと制限

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="region-availability"></a>利用可能なリージョン

Azure Container Instances は、指定された CPU とメモリ制限を備えた以下のリージョンで利用できます。 値は公開時現在のものです。 最新情報については、[一覧表示機能](/rest/api/container-instances/listcapabilities/listcapabilities) API を使用します。

| 場所 | OS | CPU | メモリ (GB) |
| -------- | -- | :---: | :-----------: |
| カナダ中部 |  Linux | 4 | 16 |
| 米国東部、北ヨーロッパ、西ヨーロッパ、米国西部、米国西部 2 |  Linux | 4 | 14 |
| 東日本 |  Linux | 2 | 8 |
| オーストラリア東部、米国東部 2、東南アジア |  Linux | 2 | 7 |
| インド中部、東アジア、米国中北部、米国中南部、インド南部 |  Linux | 2 | 3.5 |
| 米国東部、西ヨーロッパ、米国西部 |  Windows | 4 | 14 |
| オーストラリア東部、カナダ中部、インド中部、東アジア、米国東部 2、東日本、米国中北部、北ヨーロッパ、米国中南部、インド南部、東南アジア、米国西部 2 |  Windows | 2 | 3.5 |

これらのリソース制限内で作成されたコンテナー インスタンスは、デプロイ リージョン内での利用の対象になります。 リージョンでの負荷が高い場合、インスタンスのデプロイ時に失敗することがあります。 このようなデプロイの失敗を減らすために、より低い CPU とメモリの設定でインスタンスをデプロイしてみるか、デプロイを後で試してみてください。

[aka.ms/aci/feedback](https://aka.ms/aci/feedback).で、リージョンの追加の必要性または CPU/メモリ制限の増加についてチームにお知らせください。

コンテナー インスタンスのデプロイに関するトラブルシューティングの詳細については、「[Azure Container Instances でのデプロイに関する問題をトラブルシューティングする](container-instances-troubleshooting.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

特定の既定の制限とクォータを増やすことができます。 このような増加に対応する 1 つまたは複数のリソースの増加を要求する場合は、[Azure サポート リクエスト][azure-support]を送信してください (**[問題の種類]** で "クォータ" を選択してください)。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
