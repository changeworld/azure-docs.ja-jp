---
title: Service Connector の内部構造
description: Service Connector の内部構造、アーキテクチャ、接続、データの送信方法について説明します。
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fda89409a39644057ebd76d949665b281d32dc2a
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501668"
---
# <a name="service-connector-internals"></a>Service Connector の内部構造

Service Connector は、サービス間の接続を簡単に作成および管理する方法を提供する拡張リソース プロバイダーです。
- クラウド ネイティブ アプリケーションと共に使用される主要なデータベース、ストレージ、リアルタイム サービス、状態、シークレット ストアをサポートします (一覧の拡張を積極的に進めています)。
- 1 つのコマンドまたは数回のクリックでサービス接続を作成して、ネットワーク設定、認証を構成し、接続環境変数またはプロパティを管理します。
- 接続を検証し、対応する提案を検出してサービス接続を修正します。 

## <a name="service-connection-overview"></a>サービス接続の概要

サービス接続は、Service Connector のリソース モデルで重要となる概念です。 Service Connector では、サービス接続は、2 つのサービス間のリンクの抽象化を表します。 サービス接続では、次のプロパティが定義されます。

| プロパティ | 説明 |
|--------|-----------|
| 接続名 | サービス接続の一意の名前。  |
| ソース サービスの種類 | ソース サービスは、通常、Azure コンピューティング サービスです。 Service Connector の機能については、これらの Azure コンピューティング サービス プロバイダーを展開して、サポートされるコンピューティング サービスを参照してください。  |
| ターゲット サービスの種類 | ターゲット サービスは、コンピューティング サービスの接続先となるバッキング サービスまたは依存関係サービスです。 Service Connector では、主要なデータベース、ストレージ、リアルタイム サービス、状態、シークレット ストアなど、さまざまなターゲット サービスの種類がサポートされます。 |
| クライアントの種類 | クライアントの種類とは、コンピューティング ランタイム スタック、開発フレームワーク、または特定のクライアント ライブラリの種類を意味し、接続環境変数またはプロパティの特定の形式が受け入れられます。 |
| 認証の種類 | サービス接続で使用される認証の種類。 これは、純粋なシークレットまたは接続文字列、マネージド ID、またはサービス プリンシパルを指定できます。 |

インスタンスで複数のターゲット リソースに接続する必要がある場合、1 つのソース サービス インスタンスから複数のサービス接続を作成できます。 また、同じターゲット リソースを複数のソース インスタンスから接続することもできます。 Service Connector では、ソース インスタンスのプロパティ内のすべての接続が管理されます。 つまり、ソース サービス インスタンスのポータルまたは CLI コマンドで接続を作成、取得、更新、削除できます。 

この接続では、サブスクリプション間またはテナント間がサポートされます。 ソース サービスとターゲット サービスは、異なるサブスクリプションまたはテナントに属することができます。 新しいサービス接続を作成すると、既定では、接続リソースはコンピューティング サービス インスタントと同じリージョン内にあります。

## <a name="create-or-update-a-service-connection"></a>サービス接続を作成または更新する

Service Connector では、接続の作成または更新中に、次のような複数の手順が実行されます。

- ソースおよびターゲット サービスがネットワーク レベルで相互に通信できるようにターゲット リソースのネットワークとファイアウォールの設定を構成します。
- ソース リソースの接続情報を構成します
- 必要に応じて、ソースおよびターゲットの認証情報を構成します
- 失敗した場合にロールバックをサポートするように接続を作成または更新します。

接続の作成および更新には、複数の手順が含まれます。 いずれかの手順が失敗した場合、Service Connector により、前のすべての手順がロールバックされ、ソースおよびターゲットのインスタンスで初期設定が保持されます。

## <a name="connection-configurations"></a>接続の構成

サービス接続が作成されると、接続の構成がソース サービスに設定されます。
portal で、 **[Service Connector (プレビュー)]** ページに移動します。 各接続を展開して、接続の構成を表示できます。

:::image type="content" source="media/tutorial-java-spring-confluent-kafka/portal-list-connection-config.png" alt-text="portal の構成の一覧":::

CLI では、`list-configuration` コマンドを使用して、接続の構成を表示できます。

```azurecli
az webapp connection list-configuration -g {webapp_rg} -n {webapp_name} --connection {service_connection_name}
```

```azurecli
az spring-cloud connection list-configuration -g {spring_cloud_rg} -n {spring_cloud_name} --connection {service_connection_name}
```

## <a name="configuration-naming-convention"></a>構成の名前付け規則

Service Connector では、接続を作成するときに構成 (環境変数または Spring Boot の構成) が設定されます。 環境変数のキーと値のペアは、クライアントの種類と認証の種類によって決まります。 たとえば、Azure SDK とマネージド ID を使用するには、クライアント ID、クライアント シークレットなどが必要です。JDBC ドライバーを使用するには、データベース接続文字列が必要です。 構成の名前付けルールは次のとおりです。

クライアントの種類として **Spring Boot** を使用する場合:

* 各ターゲット サービスの Spring Boot ライブラリには、独自の名前付け規則があります。 たとえば、MySQL 接続の設定は、`spring.datasource.url`、`spring.datasource.username`、`spring.datasource.password` です。 Kafka 接続の設定は、`spring.kafka.properties.bootstrap.servers` です。

Spring Boot を除く **他のクライアントの種類** を使用する場合:

* ターゲット サービスに接続する場合、最初の接続構成のキー名は、`{Cloud}_{Type}_{Name}` という形式になります。 たとえば、`AZURE_STORAGEBLOB_RESOURCEENDPOINT`、`CONFLUENTCLOUD_KAFKA_BOOTSTRAPSERVER` などです。 
* ターゲット リソースの種類が同じ場合、2 番目の接続構成のキー名は `{Cloud}_{Type}_{Connection Name}_{Name}` という形式になります。 たとえば、`AZURE_STORAGEBLOB_CONN2_RESOURCEENDPOINT`、`CONFLUENTCLOUD_KAFKA_CONN2_BOOTSTRAPSERVER` などです。

## <a name="validate-a-service-connection"></a>サービス接続を検証する
接続の検証では、次の項目がチェックされます。

* ソースおよびターゲットのリソースが存在するかどうかを検証する
* ターゲット リソースのネットワークとファイアウォールの設定を検証する
* ソース リソースの接続情報を検証する
* 必要に応じて、ソースおよびターゲットの認証情報を検証する

## <a name="delete-connection"></a>接続の削除

接続を削除すると、ソース リソースの接続情報も削除されます。 
