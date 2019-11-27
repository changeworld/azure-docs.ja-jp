---
title: 着信呼び出しのクライアント認証の構成 - Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid on IoT Edge によって公開される API プロトコルを構成します。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: b5456130e89bf77e2c2ba41880323e38f6b27f4c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991840"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>着信呼び出しのクライアント認証の構成

このガイドでは、Event Grid モジュールで可能なクライアント認証構成の例を示します。 Event Grid モジュールでは、次の 2 種類のクライアント認証がサポートされています。

* Shared Access Signature (SAS) キーベース
* 証明書ベース

可能なすべての構成については、「[セキュリティと認証](security-authentication.md)」のガイドを参照してください。

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>証明書ベースのクライアント認証を有効にし、自己署名証明書は拒否する

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>証明書ベースのクライアント認証を有効にし、自己署名証明書を許可する

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>プロパティ **inbound:clientAuth:clientCert:allowUnknownCA** は、一般的に自己署名証明書が使用される、テスト環境でのみ **true** に設定してください。 運用環境のワークロードの場合は、このプロパティを **false** に設定し、証明書機関 (CA) から証明書を取得することをお勧めします。

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>証明書ベースと SAS キー ベースのクライアント認証を有効にする

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=true",
    "inbound:clientAuth:sasKeys:key1=<some-secret1-here>",
    "inbound:clientAuth:sasKeys:key2=<some-secret2-here>",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>SAS キーベースのクライアント認証では、IoT Edge ネットワークの外部で API ポートがアクセス可能であることを前提として、非 IoT エッジ モジュールによる管理と実行時操作が許可されます。
