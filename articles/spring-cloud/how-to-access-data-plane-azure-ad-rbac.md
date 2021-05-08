---
title: Config Server とサービス レジストリにアクセスする
titleSuffix: Azure Spring Cloud
description: Azure Active Directory のロールベースのアクセス制御を使用して、Config Server とサービス レジストリ エンドポイントにアクセスする方法。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 16433d5b148d7bc441e375591c64af497cd7b8de
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505334"
---
# <a name="access-config-server-and-service-registry"></a>Config Server とサービス レジストリにアクセスする

この記事では、Azure Active Directory (Azure AD) のロールベースのアクセス制御 (RBAC) を使用して、Azure Spring Cloud によって管理されている Spring Cloud Config Server と Spring Cloud サービス レジストリ にアクセスする方法について説明します。

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Azure AD ユーザーまたはグループ、MSI、またはサービス プリンシパルにロールを割り当てる

Azure AD と RBAC を使用するには、次の手順で *Azure Spring Cloud Data Reader* ロールをユーザー、グループ、またはサービス プリンシパルに割り当てる必要があります。

1. サービス インスタンスのサービスの概要ページに移動します。

2. **[アクセス制御 (IAM)]** をクリックしてアクセス制御ブレードを開きます。

3. **[追加]** ボタンと **[Add role assignments]\(ロールの割り当ての追加\)** をクリックします (追加するには承認が必要な場合があります)。

4. **[ロール]** の下にある *Azure Spring Cloud Data Reader* を見つけて選択します。
5. ユーザーの種類に応じて、`User, group, or service principal` または `User assigned managed identity` へのアクセスを割り当てます。 ユーザーを検索して選択します。  
6. [`Save`] をクリックします。

   ![assign-role](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-config-server-and-service-registry-endpoints"></a>Config Server とサービス レジストリ エンドポイントにアクセスする

Azure Spring Cloud Data Reader ロールが割り当てられると、お客様は Spring Cloud Config Server と Spring Cloud サービス レジストリのエンドポイントにアクセスできるようになります。 次の手順を実行します。

1. アクセス トークンを取得します。 Azure AD ユーザーに Azure Spring Cloud Data Reader ロールが割り当てられると、次のコマンドを使用して、ユーザー、サービス プリンシパル、またはマネージド ID で Azure CLI にログインし、アクセス トークンを取得することができます。 詳細については、[Azure CLI の認証](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)に関する記事を参照してください。 

    ```azurecli
    az login
    az account get-access-token
    ```
2. エンドポイントを作成します。 Azure Spring Cloud によって管理されている Spring Cloud Config Server と Spring Cloud サービス レジストリの既定のエンドポイントがサポートされています。 詳細については、[運用環境に対応するエンドポイント](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)に関するページを参照してください。 また、エンドポイントにアクセスすることで、Azure Spring Cloud によって管理されている Spring Cloud Config Server と Spring Cloud サービス レジストリのサポートされているエンドポイントの完全なリストを取得することもできます。

    * *https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/*
    * *https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/* 

>[!NOTE]
> Azure China を使用している場合は `*.azuremicroservices.io` を `*.microservices.azure.cn` に置き換えてください ([詳細](https://docs.microsoft.com/azure/china/resources-developer-guide#check-endpoints-in-azure))。

3. 作成されたエンドポイントにアクセス トークンを使用してアクセスします。 アクセス トークンをヘッダーに配置して認可を提供します。  "GET" メソッドのみがサポートされています。

    たとえば、 *https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/health* のようなエンドポイントにアクセスして、eureka の正常性状態を確認します。

    応答が *401 Unauthorized* の場合は、ロールが正常に割り当てられているかどうかをご確認ください。  ロールが有効になるか、アクセス トークンの有効期限が切れていないことを確認するには、数分かかります。

## <a name="next-steps"></a>次のステップ
* [Azure CLI を認証する](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [運用環境に対応したエンドポイント](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>関連項目
* [ロールとアクセス許可を作成する](how-to-permissions.md)
