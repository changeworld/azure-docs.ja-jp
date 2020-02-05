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
ms.openlocfilehash: 3363db4557dd19e8d72747ccd62bb535abb7b1e2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841793"
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
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>証明書ベースのクライアント認証を有効にし、自己署名証明書を許可する

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>プロパティ **inbound__clientAuth__clientCert__allowUnknownCA** は、一般的に自己署名証明書が使用される、テスト環境でのみ **true** に設定してください。 運用環境のワークロードの場合は、このプロパティを **false** に設定し、証明書機関 (CA) から証明書を取得することをお勧めします。

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>証明書ベースと SAS キー ベースのクライアント認証を有効にする

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>SAS キーベースのクライアント認証では、IoT Edge ネットワークの外部で API ポートがアクセス可能であることを前提として、非 IoT エッジ モジュールによる管理と実行時操作が許可されます。
