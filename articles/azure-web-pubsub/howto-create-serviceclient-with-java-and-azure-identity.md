---
title: Java と Azure ID を使用して WebPubSubServiceClient を作成する方法
description: Java と Azure ID を使用して WebPubSubServiceClient を作成する方法
author: terencefan
ms.author: tefa
ms.date: 11/15/2021
ms.service: azure-web-pubsub
ms.topic: how-to
ms.openlocfilehash: aa0f9918b971015632866eb50995e6a2d6eb2f75
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725430"
---
# <a name="how-to-create-a-webpubsubserviceclient-with-java-and-azure-identity"></a>Java と Azure ID を使用して `WebPubSubServiceClient` を作成する方法

この攻略ガイドでは、Java と Azure ID を使用して `WebPubSubServiceClient` を作成する方法について説明します。

## <a name="requirements"></a>要件

- [azure-identity](https://mvnrepository.com/artifact/com.azure/azure-identity) 依存関係を `pom.xml` に追加します。

  ```xml
  <dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.4.1</version>
  </dependency>
  ```

  > [!Tip]
  > 最新バージョンは、こちらの[ページ](https://mvnrepository.com/artifact/com.azure/azure-identity)にあります。

  詳細については、「[Java と Azure ID を使用した Azure 認証](/azure/developer/java/sdk/identity)」を参照してください。

- [azure-messaging-webpubsub](https://mvnrepository.com/artifact/com.azure/azure-messaging-webpubsub) 依存関係を `pom.xml` に追加します。

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-messaging-webpubsub</artifactId>
      <version>1.0.0-beta.6</version>
  </dependency>
  ```

  > [!Tip]
  > 最新バージョンは、こちらの[ページ](https://mvnrepository.com/artifact/com.azure/azure-messaging-webpubsub)にあります。

## <a name="sample-codes"></a>サンプル コード

1. Azure Identity SDK を使用して `TokenCredential` を作成します。

    ```java
    package com.webpubsub.tutorial;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.DefaultAzureCredentialBuilder;

    public class App {

        public static void main(String[] args) {
            TokenCredential credential = new DefaultAzureCredentialBuilder().build();
        }
    }
    ```

    `credential` は、`TokenCredential` クラスから継承する任意のクラスにすることができます。

    - EnvironmentCredential
    - ClientSecretCredential
    - ClientCertificateCredential
    - ManagedIdentityCredential
    - VisualStudioCredential
    - VisualStudioCodeCredential
    - AzureCliCredential

    詳細については、[Java 用の Azure ID クライアント ライブラリ](/java/api/overview/azure/identity-readme)に関するページを参照してください。

2. 次に、`endpoint`、`hub`、`credential` を使用して `client` を作成し ます。 

    ```Java
    package com.webpubsub.tutorial;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.DefaultAzureCredentialBuilder;
    import com.azure.messaging.webpubsub.WebPubSubServiceClient;
    import com.azure.messaging.webpubsub.WebPubSubServiceClientBuilder;

    public class App {
        public static void main(String[] args) {

            TokenCredential credential = new DefaultAzureCredentialBuilder().build();

            // create the service client
            WebPubSubServiceClient client = new WebPubSubServiceClientBuilder()
                    .endpoint("<endpoint>")
                    .credential(credential)
                    .hub("<hub>")
                    .buildClient();
        }
    }
    ```

    このクライアントの使用方法については、[Java 用 Azure Web PubSub サービス クライアント ライブラリ](/java/api/overview/azure/messaging-webpubsub-readme)に関する記事を参照してください

## <a name="complete-sample"></a>完全なサンプル

- [AAD 認証を使用した簡単なチャットルーム](https://github.com/Azure/azure-webpubsub/tree/main/samples/java/chatapp-aad)