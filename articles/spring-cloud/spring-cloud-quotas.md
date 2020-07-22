---
title: Azure Spring Cloud のサービス プランとクォータ
description: Azure Spring Cloud のサービス クォータとサービス プランについて説明します
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: dc4426fc08efff44bc46ba37eb6fbc6fe31f356b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84735440"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure Spring Cloud のクォータとサービス プラン

すべての Azure サービスには、リソースと機能に対する既定の制限とクォータが設定されています。   Azure Spring Cloud には、2 つの価格レベルがあります。Basic と Standard です。 この記事では、両方のレベルの制限について詳しく説明します。

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure Spring Cloud のサービス レベルと制限

| リソース | Basic | Standard
------- | ------- | -------
vCPU | サービス インスタンスごとに 1 つ | サービス インスタンスごとに 4 つ
メモリ | サービス インスタンスごとに 2 GB | サービス インスタンスごとに 8 GB
サブスクリプション 1 件、1 リージョンあたりの Azure Spring Cloud サービス インスタンスの数 | 10 | 10
Azure Spring Cloud サービス インスタンスあたりのアプリ インスタンスの合計数 | 25 | 500
永続ボリューム | 1 GB/アプリ x 10 個のアプリ | 50 GB/アプリ x 10 個のアプリ


プレビュー期間中は、Azure Spring Cloud で提供されるサービス レベルは 1 つだけです。 制限に達すると、次のような 400 エラーを受け取ります: "Quota exceeds limit for subscription *your subscription* in region *region where your Azure Spring Cloud service is created* (クォータがサブスクリプション <サブスクリプション> のリージョン <Azure Spring Cloud サービスが作成されたリージョン> での制限を超えています)。

## <a name="next-steps"></a>次のステップ

一部の既定の制限は引き上げることができます。 設定を増やす必要がある場合は、[サポート リクエストを作成してください](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。
