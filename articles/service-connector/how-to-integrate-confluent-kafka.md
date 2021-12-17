---
title: Confluent Cloud 上の Apache kafka と Service Connector に統合する
description: Service Connector を使用して Apache kafka on Confluent Cloud をアプリケーションに統合する
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: c0b5c47982fb0b3af7b2ecac6fde12f1e2f2f101
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089752"
---
# <a name="integrate-apache-kafka-on-confluent-cloud-with-service-connector"></a>Confluent Cloud 上の Apache kafka と Service Connector に統合する

このページでは、Service Connector を使用した Confluent Cloud と Service でサポートされている認証の種類と Apache kafka のクライアントの種類を示します。 Service Connector を使用せずに、他のプログラミング言語で Confluent Cloud 上の Apache kafka に接続できる場合があります。 このページには、サービス接続を作成するときに取得する既定の環境変数の名前と値 (Spring Boot 構成) も示されています。 [Service Connector 環境変数の名前付け規則](concept-service-connector-internals.md)の詳細を参照してください。

## <a name="supported-compute-service"></a>サポートされているコンピューティング サービス

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>サポートされている認証の種類とクライアントの種類

| クライアントの種類 | システム割り当てマネージド ID | ユーザー割り当てマネージド ID | Secret/ConnectionString | サービス プリンシパル |
| --- | --- | --- | --- | --- |
| .NET | | | ![[はい] アイコン](./media/green-check.png) | |
| Java | | | ![[はい] アイコン](./media/green-check.png) | |
| Java - Spring Boot | | | ![[はい] アイコン](./media/green-check.png) | |
| Node.js | | | ![[はい] アイコン](./media/green-check.png) | |
| Python | | | ![[はい] アイコン](./media/green-check.png) | |

## <a name="default-environment-variable-names-or-application-properties"></a>既定の環境変数名またはアプリケーション プロパティ

### <a name="net-java-nodejs-and-python"></a>.NET、Java、Node.JS、Python

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_CONFLUENTCLOUDKAFKA_BOOTSTRAPSERVER | Kafka ブートストラップ サーバー | `pkc-{serverName}.eastus.azure.confluent.cloud:9092` |
| AZURE_CONFLUENTCLOUDKAFKA_KAFKASASLCONFIG | Kafka SASL の構成 | `org.apache.kafka.common.security.plain.PlainLoginModule required username='{bootstrapServerKey}' password='{bootstrapServerSecret}';` |
| AZURE_CONFLUENTCLOUDSCHEMAREGISTRY_URL | Confluent レジストリの URL | `https://psrc-{serverName}.westus2.azure.confluent.cloud` |
| AZURE_CONFLUENTCLOUDSCHEMAREGISTRY_USERINFO |  Confluent レジストリ のユーザー情報 | `{schemaRegistryKey} + ":" + {schemaRegistrySecret}` |

### <a name="spring-boot"></a>Spring Boot

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| spring.kafka.properties.bootstrap.servers | Kafka ブートストラップ サーバー | `pkc-{serverName}.eastus.azure.confluent.cloud:9092` |
| spring.kafka.properties.sasl.jaas.config | Kafka SASL の構成 | `org.apache.kafka.common.security.plain.PlainLoginModule required username='{bootstrapServerKey}' password='{bootstrapServerSecret}';` |
| spring.kafka.properties.schema.registry.url | Confluent レジストリの URL | `https://psrc-{serverName}.westus2.azure.confluent.cloud` |
| spring.kafka.properties.schema.registry.basic.auth.user.info | Confluent レジストリ のユーザー情報 | `{schemaRegistryKey} + ":" + {schemaRegistrySecret}` |

## <a name="next-steps"></a>次のステップ

Service Connector の詳細については、以下のチュートリアルに従ってください。

> [!div class="nextstepaction"]
> [Service Connector の概念について学習する](./concept-service-connector-internals.md)
