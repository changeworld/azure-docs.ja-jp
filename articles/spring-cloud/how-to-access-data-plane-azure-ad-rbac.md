---
title: Config Server とサービス レジストリにアクセスする
titleSuffix: Azure Spring Cloud
description: Azure Active Directory のロールベースのアクセス制御を使用して、Config Server とサービス レジストリ エンドポイントにアクセスする方法。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java, subject-rbac-steps
ms.openlocfilehash: 7c792164f30de2c8c1d2614a54aaf79bebfbd873
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860781"
---
# <a name="access-config-server-and-service-registry"></a>Config Server とサービス レジストリにアクセスする

この記事では、Azure Active Directory (Azure AD) のロールベースのアクセス制御 (RBAC) を使用して、Azure Spring Cloud によって管理されている Spring Cloud Config Server と Spring Cloud サービス レジストリ にアクセスする方法について説明します。

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Azure AD ユーザーまたはグループ、MSI、またはサービス プリンシパルにロールを割り当てる

[management-group | subscription | resource-group | resource] スコープで [azure-spring-cloud-data-reader](../role-based-access-control/built-in-roles.md#azure-spring-cloud-data-reader) ロールを [user | group | service-principal | managed-identity] に割り当てます。

詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="access-config-server-and-service-registry-endpoints"></a>Config Server とサービス レジストリ エンドポイントにアクセスする

Azure Spring Cloud Data Reader ロールが割り当てられると、お客様は Spring Cloud Config Server と Spring Cloud サービス レジストリのエンドポイントにアクセスできるようになります。 次の手順を実行します。

1. アクセス トークンを取得します。 Azure AD ユーザーに Azure Spring Cloud Data Reader ロールが割り当てられると、次のコマンドを使用して、ユーザー、サービス プリンシパル、またはマネージド ID で Azure CLI にログインし、アクセス トークンを取得することができます。 詳細については、[Azure CLI の認証](/cli/azure/authenticate-azure-cli)に関する記事を参照してください。

    ```azurecli
    az login
    az account get-access-token
    ```

2. エンドポイントを作成します。 Azure Spring Cloud によって管理されている Spring Cloud Config Server と Spring Cloud サービス レジストリの既定のエンドポイントがサポートされています。 詳細については、[運用環境に対応するエンドポイント](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)に関するページを参照してください。 また、エンドポイントにアクセスすることで、Azure Spring Cloud によって管理されている Spring Cloud Config Server と Spring Cloud サービス レジストリのサポートされているエンドポイントの完全なリストを取得することもできます。

    * *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/'*
    * *'https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/'*

>[!NOTE]
> Azure China を使用している場合は `*.azuremicroservices.io` を `*.microservices.azure.cn` に置き換えてください ([詳細](/azure/china/resources-developer-guide#check-endpoints-in-azure))。

3. 作成されたエンドポイントにアクセス トークンを使用してアクセスします。 アクセス トークンをヘッダーに配置して認可を提供します: `--header 'Authorization: Bearer {TOKEN_FROM_PREVIOUS_STEP}`。  "GET" メソッドのみがサポートされています。

    たとえば、 *"https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/health"* のようなエンドポイントにアクセスして、eureka の正常性状態を確認します。

    応答が *401 Unauthorized* の場合は、ロールが正常に割り当てられているかどうかをご確認ください。  ロールが有効になるか、アクセス トークンの有効期限が切れていないことを確認するには、数分かかります。

## <a name="next-steps"></a>次のステップ

* [Azure CLI を認証する](/cli/azure/authenticate-azure-cli)
* [運用環境に対応したエンドポイント](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)
* [ロールとアクセス許可を作成する](how-to-permissions.md)
