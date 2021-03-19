---
title: Azure AD、RBAC を使用したデータ プレーンへのアクセス
description: Azure Active Directory のロールベースのアクセス制御を使用してデータ プレーンにアクセスする方法。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 2909d40fbc7cb5b310ea17f17a6e683ce47638ce
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220098"
---
# <a name="access-the-data-plane-with-azure-active-directory-and-role-based-access-control"></a>Azure Active Directory とロールベースのアクセス制御を使用してデータ プレーンにアクセスする

この記事では、Azure Active Directory (AAD) のロールベースのアクセス制御 (RBAC) を使用して Azure Spring Cloud 構成サーバーとサービス レジストリ エンドポイントにアクセスする方法について説明します。

## <a name="assign-role-to-aad-usergroup-msi-or-service-principal"></a>AAD ユーザーまたはグループ、MSI、またはサービス プリンシパルにロールを割り当てる

AAD と RBAC を使用するには、次の手順で *Azure Spring Cloud Data Reader* ロールをユーザー、グループ、またはサービス プリンシパルに割り当てる必要があります。

1. サービス インスタンスのサービスの概要ページに移動します。

2. **[アクセス制御 (IAM)]** をクリックしてアクセス制御ブレードを開きます。

3. **[追加]** ボタンと **[Add role assignments]\(ロールの割り当ての追加\)** をクリックします (追加するには承認が必要な場合があります)。

4. **[ロール]** の下にある *Azure Spring Cloud Data Reader* を見つけて選択します。
5. ユーザーの種類に応じて、`User, group, or service principal` または `User assigned managed identity` へのアクセスを割り当てます。 ユーザーを検索して選択します。  
6. [`Save`] をクリックします。

   ![assign-role](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-data-plane"></a>データ プレーンにアクセスする

AAD ユーザーに *Azure Spring Cloud Data Reader* ロールが割り当てられると、お客様は、ユーザー、サービス プリンシパル、またはマネージド ID を使用して Azure CLI にログインできるようになります。  アクセス トークンの取得については、[Azure CLI の認証](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)に関するページを参照してください。  次に、以下のコマンドを使用します。

```azurecli
az login
az account get-access-token
```

現在、CLI は、構成サーバーとサービス レジストリの既定のエンドポイントをサポートしています。 詳細については、[運用環境に対応するエンドポイント](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)に関するページを参照してください。 

エンドポイントにアクセスすることで、構成サーバーとサービス レジストリでサポートされているエンドポイントの完全な一覧を取得することもできます。
* *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/*
* *https://SERVICE_NAME.Root_Endpoint/config/actuator/* 

ヘッダーのアクセス トークンにより、承認が提供されます。 "GET" メソッドのみがサポートされています。

たとえば、 *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/health* のようなエンドポイントにアクセスして、eureka の正常性状態を確認します。

さまざまなクラウドの種類に応じた、さまざまなルート エンドポイントを以下に示します。

| クラウド          | ルート エンドポイント              |
| -------------- | -------------------------- |
| パブリック         | svc.azuremicroservices.io  |
| Mooncake/China | svc.microservices.azure.cn |

応答が *401 Unauthorized* の場合は、ロールが正常に割り当てられているかどうかをご確認ください。  ロールが有効になるか、アクセス トークンの有効期限が切れていないことを確認するには、数分かかります。

## <a name="next-steps"></a>次のステップ
* [Azure CLI を認証する](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [運用環境に対応したエンドポイント](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>関連項目
* [ロールとアクセス許可を作成する](spring-cloud-howto-permissions.md)