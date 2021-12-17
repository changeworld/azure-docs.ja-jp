---
title: Config Server とサービス レジストリにアクセスする
titleSuffix: Azure Spring Cloud
description: Azure Active Directory のロールベースのアクセス制御を使用して、Config Server とサービス レジストリ エンドポイントにアクセスする方法。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/25/2021
ms.custom: devx-track-java, subject-rbac-steps
ms.openlocfilehash: 9c4a5715af9fe98f5e577410219a54368c27b6df
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054877"
---
# <a name="access-config-server-and-service-registry"></a>Config Server とサービス レジストリにアクセスする

この記事では、Azure Active Directory (Azure AD) のロールベースのアクセス制御 (RBAC) を使用して、Azure Spring Cloud によって管理されている Spring Cloud Config Server と Spring Cloud サービス レジストリ にアクセスする方法について説明します。

> [!NOTE]
> Azure Spring Cloudサービス内にデプロイおよび実行されているアプリケーションは、管理対象のSpring Cloud Configサーバーおよびサービスレジストリにアクセスするときに、証明書ベースの認証と承認に自動的に接続されます。 これらのアプリケーションについては、このガイダンスに従う必要があります。 関連する証明書は Azure Spring Cloud プラットフォームによって完全に管理され、Config Server および Service Registry に接続すると、アプリケーションに自動的に挿入されます。

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Azure AD ユーザーまたはグループ、MSI、またはサービス プリンシパルにロールを割り当てる

[管理グループ | サブスクリプション | リソース グループ | リソース] スコープでロールを [ユーザー | グループ | サービス プリンシパル | マネージド ID] に割り当てます。

| ロール名                                       | 説明                                                                  |
|-------------------------------------------------|------------------------------------------------------------------------------|
| Azure Spring Cloud Config Server 閲覧者         | Azure Spring Cloud Config Server への読み取りアクセスを許可します。                       |
| Azure Spring Cloud Config Server 共同作成者    | Azure Spring Cloud Config Server への読み取り、書き込み、削除アクセスを許可します。    |
| Azure Spring Cloud Service Registry 閲覧者      | Azure Spring Cloud Service Registry への読み取りアクセスを許可します。                    |
| Azure Spring Cloud Service Registry 共同作成者 | Azure Spring Cloud Service Registry への読み取り、書き込み、削除アクセスを許可します。 |

詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="access-config-server-and-service-registry-endpoints"></a>Config Server とサービス レジストリ エンドポイントにアクセスする

ロールが割り当てられると、割り当てられたユーザーは次の手順を使用して Spring Cloud Config Server と Spring Cloud Service Registry のエンドポイントにアクセスできるようになります。

1. アクセス トークンを取得します。 Azure AD ユーザーにロールが割り当てられると、次のコマンドを使用して、ユーザー、サービス プリンシパル、またはマネージド ID で Azure CLI にサインインし、アクセス トークンを取得することができます。 詳細については、[Azure CLI の認証](/cli/azure/authenticate-azure-cli)に関する記事を参照してください。

    ```azurecli
    az login
    az account get-access-token
    ```

1. エンドポイントを作成します。 Azure Spring Cloud によって管理されている Spring Cloud Config Server と Spring Cloud Service Registry の既定のエンドポイントがサポートされています。

    * *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/{path}'*
    * *'https://SERVICE_NAME.svc.azuremicroservices.io/config/{path}'*

    >[!NOTE]
    > Azure China を使用している場合は、`*.azuremicroservices.io` を `*.microservices.azure.cn` に置き換えます。 詳細については、「[Azure China 開発者ガイド](/azure/china/resources-developer-guide)」のセクション「[Azure でエンドポイントを確認する](/azure/china/resources-developer-guide#check-endpoints-in-azure)」を参照してください。

1. 作成されたエンドポイントにアクセス トークンを使用してアクセスします。 アクセス トークンをヘッダーに配置して認可を提供します: `--header 'Authorization: Bearer {TOKEN_FROM_PREVIOUS_STEP}`。

    次に例を示します。

    a. たとえば、 *"https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/health"* のようなエンドポイントにアクセスして、Config Server の正常性状態を確認します。

    b. *"https://SERVICE_NAME.svc.azuremicroservices.io/eureka/eureka/apps"* のようなエンドポイントにアクセスして、Spring Cloud Service Registry に登録されているアプリを表示します (Eureka はこちら)。

    応答が *401 Unauthorized* の場合は、ロールが正常に割り当てられているかどうかをご確認ください。  ロールが有効になるか、アクセス トークンの有効期限が切れていないことを確認するには、数分かかります。

アクチュエータ エンドポイントの詳細については、[運用環境に対応するエンドポイント](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)に関するページを参照してください。

Eureka エンドポイントについては、[Eureka の REST 操作](https://github.com/Netflix/eureka/wiki/Eureka-REST-operations)に関するページを参照してください。

構成サーバー エンドポイントと詳細なパス情報については、[ResourceController.java](https://github.com/spring-cloud/spring-cloud-config/blob/main/spring-cloud-config-server/src/main/java/org/springframework/cloud/config/server/resource/ResourceController.java) と [EncryptionController.java](https://github.com/spring-cloud/spring-cloud-config/blob/main/spring-cloud-config-server/src/main/java/org/springframework/cloud/config/server/encryption/EncryptionController.java) に関するページを参照してください。

## <a name="register-spring-boot-apps-to-spring-cloud-config-server-and-service-registry-managed-by-azure-spring-cloud"></a>Spring Boot アプリを Azure Spring Cloud によって管理されている Spring Cloud Config Server と Service Registry に登録する

ロールが割り当てられた後、Azure AD トークン認証を使用して、Spring Boot アプリを Azure Spring Cloud によって管理されている Spring Cloud Config Server と Service Registry に登録できます。 Config Server と Service Registry の両方で、認証用のベアラー トークンを挿入するための[カスタム REST テンプレート](https://cloud.spring.io/spring-cloud-config/reference/html/#custom-rest-template)がサポートされています。

詳細については、サンプル「[Azure Spring Cloud によって管理されている Config Server にアクセスする](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-config-server-client)」と「[Azure Spring Cloud によって管理されている Spring Cloud Service Registry にアクセスする](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-eureka-client)」を参照してください。 次のセクションでは、これらのサンプルの重要な詳細について説明します。

***AccessTokenManager.java* 内:**

`AccessTokenManager` は Azure AD からアクセス トークンを取得することを担います。 *application.properties* ファイル内のサービス プリンシパルのサインイン情報を構成し、`ApplicationTokenCredentials` を初期化してトークンを取得します。 このファイルは、両方のサンプルで見つかります。

```java
prop.load(in);
tokenClientId = prop.getProperty("access.token.clientId");
String tenantId = prop.getProperty("access.token.tenantId");
String secret = prop.getProperty("access.token.secret");
String clientId = prop.getProperty("access.token.clientId");
credentials = new ApplicationTokenCredentials(
    clientId, tenantId, secret, AzureEnvironment.AZURE);
```

***CustomConfigServiceBootstrapConfiguration.java* 内:**

`CustomConfigServiceBootstrapConfiguration` によって Config Server のカスタム REST テンプレートが実装され、Azure AD から トークンが `Authorization` のヘッダーとして挿入されます。 このファイルは、[Config Server のサンプル](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-config-server-client)で見つかります。

```java
public class RequestResponseHandlerInterceptor implements ClientHttpRequestInterceptor {

    @Override
    public ClientHttpResponse intercept(HttpRequest request, byte[] body, ClientHttpRequestExecution execution) throws IOException {
        String accessToken = AccessTokenManager.getToken();
        request.getHeaders().remove(AUTHORIZATION);
        request.getHeaders().add(AUTHORIZATION, "Bearer " + accessToken);

        ClientHttpResponse response = execution.execute(request, body);
        return response;
    }

}
```

***CustomRestTemplateTransportClientFactories.java* 内:**

前の 2 つのクラスは、Spring Cloud Service Registry 用のカスタム REST テンプレートを実装するためのものです。 この `intercept` の部分は、前述の Config Server と同じです。 必ずメッセージ コンバーターに `factory.mappingJacksonHttpMessageConverter()` を追加してください。 このファイルは、[Spring Cloud Service Registry のサンプル](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-eureka-client)で見つかります。

```java
private RestTemplate customRestTemplate() {
    /*
     * Inject your custom rest template
     */
    RestTemplate restTemplate = new RestTemplate();
    restTemplate.getInterceptors()
        .add(new RequestResponseHandlerInterceptor());
    RestTemplateTransportClientFactory factory = new RestTemplateTransportClientFactory();

    restTemplate.getMessageConverters().add(0, factory.mappingJacksonHttpMessageConverter());

    return restTemplate;
}
```

Kubernetes クラスターでアプリケーションを実行している場合は、アクセス用に IP アドレスを使用して Spring Cloud Service Registry を登録することをお勧めします。

```properties
eureka.instance.prefer-ip-address=true
```

## <a name="next-steps"></a>次のステップ

* [Azure CLI を認証する](/cli/azure/authenticate-azure-cli)
* [運用環境に対応したエンドポイント](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)
* [ロールとアクセス許可を作成する](how-to-permissions.md)
