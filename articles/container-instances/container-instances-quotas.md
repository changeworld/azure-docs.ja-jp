---
title: Azure Container Instances のクォータとリージョンの可用性
description: Azure Container Instances サービスのクォータ、制限、リージョンでの利用可能性。
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 02/15/2019
ms.author: danlep
ms.openlocfilehash: c676989b4b882f2b1887a1b6a5091b60027f61d0
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328409"
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Azure Container Instances のクォータとリージョンの可用性

すべての Azure サービスには、リソースと機能に対する特定の既定の制限とクォータが含まれます。 以下のセクションでは、いくつかの Azure Container Instances リソースに対する既定のリソース制限と、Azure リージョンでサービスを利用できるかどうかについて詳しく説明します。

## <a name="service-quotas-and-limits"></a>サービスのクォータと制限

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="feature-availability"></a>使用可能な機能

Azure Container Instances では、同じ API で、Windows と Linux の両方のコンテナーをスケジュールできます。 ただし、次の機能は現在、Linux コンテナー グループでのみ使用できます。 Windows でのサポートは計画されています。

* コンテナー グループあたりの複数のコンテナー
* ボリューム マウント (Azure Files、emptyDir、GitRepo、シークレット)
* 仮想ネットワーク (プレビュー)
* GPU リソース (プレビュー)

## <a name="region-availability"></a>利用可能なリージョン

Azure Container Instances は、各コンテナー グループに対して指定された CPU とメモリ制限を備えた以下のリージョンで利用できます。 値は公開時現在のものです。 最新情報については、[一覧表示機能](/rest/api/container-instances/listcapabilities/listcapabilities) API を使用します。 

Azure Container Instances と[仮想ネットワーク](container-instances-vnet.md) (プレビュー) または [GPU リソース](container-instances-gpu.md) (プレビュー) を使用する場合、可用性とリソースの制限は異なる場合があります。

| Location | OS | CPU | メモリ (GB) |
| -------- | -- | :---: | :-----------: |
| カナダ中部、米国中部、米国東部 2、米国中南部 | Linux | 4 | 16 |
| 米国東部、北ヨーロッパ、西ヨーロッパ、米国西部、米国西部 2 | Linux | 4 | 14 |
| 東日本 | Linux | 2 | 8 |
| オーストラリア東部、東南アジア | Linux | 2 | 7 |
| インド中部、東アジア、米国中北部、インド南部 | Linux | 2 | 3.5 |
| 米国東部、西ヨーロッパ、米国西部 | Windows | 4 | 14 |
| オーストラリア東部、カナダ中部、インド中部、米国中部、東アジア、米国東部 2、東日本、米国中北部、北ヨーロッパ、米国中南部、インド南部、東南アジア、米国西部 2 | Windows | 2 | 3.5 |

これらのリソース制限内で作成されたコンテナー インスタンスは、デプロイ リージョン内での利用の対象になります。 リージョンでの負荷が高い場合、インスタンスのデプロイ時に失敗することがあります。 このようなデプロイの失敗を減らすために、より低い CPU とメモリの設定でインスタンスをデプロイしてみるか、デプロイを後で試してみてください。

[aka.ms/aci/feedback](https://aka.ms/aci/feedback).で、リージョンの追加の必要性または CPU/メモリ制限の増加についてチームにお知らせください。

コンテナー インスタンスのデプロイに関するトラブルシューティングの詳細については、「[Azure Container Instances でのデプロイに関する問題をトラブルシューティングする](container-instances-troubleshooting.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

特定の既定の制限とクォータを増やすことができます。 このような増加に対応する 1 つまたは複数のリソースの増加を要求する場合は、[Azure サポート リクエスト][azure-support]を送信してください (**[問題の種類]** で "クォータ" を選択してください)。

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
