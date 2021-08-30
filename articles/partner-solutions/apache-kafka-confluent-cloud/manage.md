---
title: Confluent Cloud を管理する - Azure パートナー ソリューション
description: この記事では、Azure portal での Confluent Cloud の管理について説明します。 シングル サインオンをセットアップし、Confluent 組織を削除し、サポートを受ける方法。
ms.service: partner-services
ms.topic: conceptual
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: c1e53382b6f399bccac53a75595eda4e61a915a4
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2021
ms.locfileid: "112520998"
---
# <a name="manage-the-confluent-cloud-resource"></a>Confluent Cloud リソースを管理する

この記事では、Azure 上で Apache Kafka for Confluent Cloud のインスタンスを管理する方法について説明します。 シングル サインオン (SSO) を設定したり、Confluent 組織を削除したりする方法を紹介しています。

## <a name="single-sign-on"></a>シングル サインオン

組織に SSO を実装するために、テナント管理者はギャラリー アプリケーションをインポートできます。 この手順は省略可能です。 アプリケーションをインポートする方法については、「[クイックスタート:Azure Active Directory (Azure AD) テナントにアプリケーションを追加する](../../active-directory/manage-apps/add-application-portal.md)」を参照してください。 テナント管理者がアプリケーションをインポートするとき、ユーザーは、Confluent Cloud ポータルへのアクセスを許可することに明示的に同意する必要はありません。

SSO を有効にするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Confluent Cloud リソースのインスタンスの **[概要]** に移動します。
1. **[Manage on Confluent Cloud]\(Confluent Cloud の管理\)** のリンクを選択します。

   :::image type="content" source="media/manage/sso-link.png" alt-text="Confluent ポータルのシングル サインオン。":::

1. SSO への同意のためのギャラリー アプリケーションをテナント管理者がインポートしていない場合、アクセス許可と同意を付与します。 この手順は、 **[Manage on Confluent Cloud]\(Confluent Cloud の管理\)** のリンクに初めてアクセスするときにのみ必要です。

   :::image type="content" source="media/manage/permissions-requested.png" alt-text="アクセス許可を付与する。":::

1. Confluent Cloud ポータルにシングル サインオンするための Azure AD アカウントを選択します。
1. 同意を指定すると、Confluent Cloud ポータルにリダイレクトされます。

## <a name="set-up-cluster"></a>クラスターをセットアップする

クラスターのセットアップの詳細については、[Confluent Cloud でのクラスターの作成に関する Confluent のドキュメント](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)を参照してください。

## <a name="delete-confluent-organization"></a>Confluent 組織を削除する

Confluent Cloud リソースが不要になった場合は、Azure と Confluent Cloud 内のリソースを削除します。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure 内のリソースを削除するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[すべてのリソース]** を選択し、Confluent Cloud リソースの名前または **リソースの種類** "_Confluent 組織_" によって **フィルター処理** します。 または Azure portal で、リソース名を検索します。
1. リソースの **[概要]** で、 **[削除]** を選択します。

    :::image type="content" source="media/delete-resources-icon.png" alt-text="リソースの削除アイコン。":::

1. 削除を確認するには、リソース名を入力して **[削除]** を選択します。

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="リソースの削除の確認を求めるメッセージ。":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

まず、Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

サインインした後、[az confluent organization delete](/cli/azure/confluent#az_confluent_organization_delete) コマンドを使用し、名前を指定して組織のリソースを削除します。

```azurecli
az confluent organization delete --name "myOrganization" --resource-group "myResourceGroup"
```

または、リソース ID を指定することもできます。

```azurecli
az confluent organization delete --id "/subscriptions/{SubID}/resourceGroups/{myResourceGroup}/providers/Microsoft.Confluent/organizations/{myOrganization}"
```

---

Confluent Cloud 上のリソースを削除するには、[Confluent Cloud 環境に関する Confluent のドキュメント](https://docs.confluent.io/current/cloud/using/environments.html)と [Confluent Cloud の基礎に関する Confluent のドキュメント](https://docs.confluent.io/current/cloud/using/cloud-basics.html)を参照してください。

クラスターとクラスター内のすべてのデータが完全に削除されます。 契約にデータ保持条項が含まれている場合、Confluent は[サービス使用条件に関する Confluent のドキュメント](https://www.confluent.io/confluent-cloud-tos)に指定されている期間、データを保持します。

クラスターを削除した時点までの日割りの使用量に対して課金されます。 クラスターが完全に削除されると、Confluent から確認用の電子メールが送信されます。

## <a name="next-steps"></a>次の手順

トラブルシューティングの支援については、「[Apache Kafka for Confluent Cloud ソリューションのトラブルシューティング](troubleshoot.md)」を参照してください。

サポートに連絡する必要がある場合は、「[Confluent Cloud リソースのサポートを利用する](get-support.md)」を参照してください。
