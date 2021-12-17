---
title: Azure Spring Cloud のサービス プランとクォータ
description: Azure Spring Cloud のサービス クォータとサービス プランについて説明します
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 1bd1ed797ce737260289ab40ed6a16c4ce4581e7
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015588"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure Spring Cloud のクォータとサービス プラン

**この記事の適用対象:** ✔️ Java ✔️ C#

すべての Azure サービスには、リソースと機能に対する既定の制限とクォータが設定されています。   Azure Spring Cloud には、2 つの価格レベルがあります。Basic と Standard です。 この記事では、両方のレベルの制限について詳しく説明します。

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure Spring Cloud のサービス レベルと制限

| リソース | Scope | Basic | Standard
------- | ------- | -------
vCPU | アプリ インスタンスごと | 1 | 4
メモリ | アプリ インスタンスごと | 2 GB | 8 GB
Azure Spring Cloud サービス インスタンス | サブスクリプションあたりのリージョンごと | 10 | 10
アプリ インスタンスの合計数 | Azure Spring Cloud サービス インスタンスあたり | 25 | 500
カスタム ドメイン | Azure Spring Cloud サービス インスタンスあたり | 0 | 25
永続ボリューム | Azure Spring Cloud サービス インスタンスあたり | 1 GB/アプリ x 10 個のアプリ | 50 GB/アプリ x 10 個のアプリ
受信パブリック エンドポイント | Azure Spring Cloud サービス インスタンスあたり| 10 <sup>1</sup> | 10 <sup>1</sup>
送信パブリック IP | Azure Spring Cloud サービス インスタンスあたり| 1 <sup>2</sup> | 2 <sup>2</sup> <br> VNet を使用している場合は 1 <sup>2</sup>

<sup>1</sup> サポート リクエストを使用して、この制限をアプリあたり最大 1 に引き上げることができます。

<sup>2</sup> サポート リクエストを使用して、この制限を最大 10 に引き上げることができます。

> [!TIP]
> サービス インスタンスあたりのアプリ インスタンスの合計数について記載されている制限は、停止状態を含むすべての状態のアプリとデプロイに適用されます。 使用されていないアプリやデプロイは削除するようにしてください。

## <a name="next-steps"></a>次のステップ

一部の既定の制限は引き上げることができます。 設定を増やす必要がある場合は、[サポート リクエストを作成してください](../azure-portal/supportability/how-to-create-azure-support-request.md)。
