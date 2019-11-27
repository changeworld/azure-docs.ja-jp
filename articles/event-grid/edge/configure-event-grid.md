---
title: 構成 - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid on IoT Edge の構成。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a9bac9a84e1ba034b011691ae82a1cb67eb71af0
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991816"
---
# <a name="event-grid-configuration"></a>Event Grid の構成

Event Grid には、環境ごとに変更できる多くの構成が用意されています。 次のセクションは、利用可能なすべてのオプションとその既定値のリファレンスです。

## <a name="tls-configuration"></a>TLS の構成

クライアント認証全般については、「[セキュリティと認証](security-authentication.md)」を参照してください。 使用例については、[こちらの記事](configure-api-protocol.md)を参照してください。

| プロパティ名 | 説明 |
| ---------------- | ------------ |
|`inbound:serverAuth:tlsPolicy`| Event Grid モジュールの TLS ポリシー。 既定値は HTTPS のみです。
|`inbound:serverAuth:serverCert:source`| Event Grid モジュールによって TLS 構成のために使用されるサーバー証明書のソース。 既定値は IoT Edge です。

## <a name="incoming-client-authentication"></a>着信クライアント認証

クライアント認証全般については、「[セキュリティと認証](security-authentication.md)」を参照してください。 例については、[こちらの記事](configure-client-auth.md)を参照してください。

| プロパティ名 | 説明 |
| ---------------- | ------------ |
|`inbound:clientAuth:clientCert:enabled`| 証明書ベースのクライアント認証をオンまたはオフにします。 既定値は true です。
|`inbound:clientAuth:clientCert:source`| クライアント証明書を検証するためのソース。 既定値は IoT Edge です。
|`inbound:clientAuth:clientCert:allowUnknownCA`| 自己署名されたクライアント証明書を許可するポリシー。 既定値は true です。
|`inbound:clientAuth:sasKeys:enabled`| SAS キー ベースのクライアント認証をオンまたはオフにします。 既定値は off です。
|`inbound:clientAuth:sasKeys:key1`| 着信要求を検証するための値の 1 つ。
|`inbound:clientAuth:sasKeys:key2`| 着信要求を検証するための、オプションの 2 番目の値。

## <a name="outgoing-client-authentication"></a>発信クライアント認証
クライアント認証全般については、「[セキュリティと認証](security-authentication.md)」を参照してください。 例については、[こちらの記事](configure-identity-auth.md)を参照してください。

| プロパティ名 | 説明 |
| ---------------- | ------------ |
|`outbound:clientAuth:clientCert:enabled`| 発信要求の ID 証明書の添付をオンまたはオフにします。 既定値は true です。
|`outbound:clientAuth:clientCert:source`| Event Grid モジュールの発信証明書を取得するためのソース。 既定値は IoT Edge です。

## <a name="webhook-event-handlers"></a>Webhook イベント ハンドラー

クライアント認証全般については、「[セキュリティと認証](security-authentication.md)」を参照してください。 例については、[こちらの記事](configure-webhook-subscriber-auth.md)を参照してください。

| プロパティ名 | 説明 |
| ---------------- | ------------ |
|`outbound:webhook:httpsOnly`| HTTPS サブスクライバーのみが許可されるかどうかを制御するポリシー。 既定値は true です (HTTPS のみ)。
|`outbound:webhook:skipServerCertValidation`| サブスクライバーの証明書を検証するかどうかを制御するフラグ。 既定値は true です。
|`outbound:webhook:allowUnknownCA`| サブスクライバーが自己署名証明書を提示できるかどうかを制御するポリシー。 既定値は true です。 

## <a name="delivery-and-retry"></a>配信と再試行

この機能全般については、「[配信と再試行](delivery-retry.md)」を参照してください。

| プロパティ名 | 説明 |
| ---------------- | ------------ |
| `broker:defaultMaxDeliveryAttempts` | イベントを配信する試行の最大数。 既定値は 30 です。
| `broker:defaultEventTimeToLiveInSeconds` | イベントが配信されなかった場合に削除されるまでの Time-to-live (TTL) (秒単位)。 既定値は **7,200** 秒です

## <a name="output-batching"></a>出力のバッチ処理

この機能全般については、[配信と出力のバッチ処理](delivery-output-batching.md)に関するページを参照してください。

| プロパティ名 | 説明 |
| ---------------- | ------------ |
| `api:deliveryPolicyLimits:maxBatchSizeInBytes` | `ApproxBatchSizeInBytes` ノブに許可される最大値。 既定値は `1_058_576` です。
| `api:deliveryPolicyLimits:maxEventsPerBatch` | `MaxEventsPerBatch` ノブに許可される最大値。 既定値は `50` です。
| `broker:defaultMaxBatchSizeInBytes` | `MaxEventsPerBatch` のみが指定されている場合の配信要求の最大サイズ。 既定値は `1_058_576` です。
| `broker:defaultMaxEventsPerBatch` | `MaxBatchSizeInBytes` のみが指定されている場合にバッチに追加するイベントの最大数。 既定値は `10` です。
