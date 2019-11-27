---
title: Webhook サブスクライバー認証の構成 - Azure Event Grid IoT Edge | Microsoft Docs
description: Webhook サブスクライバー認証の構成
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 97ed1e2ad84d895e9da0d96cd070e14acb46385d
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991832"
---
# <a name="configure-webhook-subscriber-authentication"></a>Webhook サブスクライバー認証の構成

このガイドでは、Event Grid モジュールで可能な Webhook サブスクライバー構成の例を示します。 既定では、Webhook サブスクライバーに対して HTTPS エンドポイントのみが受け入れられます。 サブスクライバーが自己署名証明書を提示した場合は、Event Grid モジュールによって拒否されます。

## <a name="allow-only-https-subscriber"></a>HTTPS サブスクライバーのみを許可する

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>自己署名証明書を持つ HTTPS サブスクライバーを許可する

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>プロパティ `outbound:webhook:allowUnknownCA` は、一般的に自己署名証明書が使用される、テスト環境でのみ `true` に設定してください。 運用環境のワークロードでは、**false** に設定することをお勧めします。

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>証明書の検証をスキップして HTTPS サブスクライバーを許可する

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=true",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>プロパティ `outbound:webhook:skipServerCertValidation` は、認証される必要がある証明書を提示しないような、テスト環境でのみ `true` に設定してください。 運用環境のワークロードでは、**false** に設定することをお勧めします

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>自己署名証明書を使用して HTTP と HTTPS の両方を許可する

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=false",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>プロパティ `outbound:webhook:httpsOnly` は、HTTP サブスクライバーを先に起動することが望ましいような、テスト環境でのみ `false` に設定してください。 運用環境のワークロードでは、**true** に設定することをお勧めします
