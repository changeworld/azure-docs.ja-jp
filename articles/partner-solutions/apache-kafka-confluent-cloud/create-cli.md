---
title: Azure CLI を使用して Apache Kafka for Confluent Cloud を作成する - Azure パートナー ソリューション
description: この記事では、Azure CLI を使用して Apache Kafka for Confluent Cloud のインスタンスを作成する方法について説明します。
ms.service: partner-services
ms.topic: quickstart
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: a9c49b1a19cbed080255492b90554ce0b138c3bd
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2021
ms.locfileid: "112535030"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud---azure-cli"></a>クイックスタート: Apache Kafka for Confluent Cloud の概要 - Azure CLI

このクイックスタートでは、Azure Marketplace と Azure CLI を使用して Apache Kafka for Confluent Cloud のインスタンスを作成します。

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 有効な Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
- Azure サブスクリプションの "_所有者_" ロールまたは "_共同作成者_" ロールが必要です。 Azure と Confluent の統合を設定できるのは、"_所有者_" または "_共同作成者_" のアクセス権を持つユーザーだけです。 作業を始める前に、[適切なアクセス権があることを確認](../../role-based-access-control/check-access.md)してください。

## <a name="find-offer"></a>プランを探す

Azure portal を使用して Apache Kafka for Confluent Cloud アプリケーションを探します。

1. Web ブラウザーで、[Azure portal](https://portal.azure.com/) に移動してサインインします。

1. 最近のセッションで **Marketplace** にアクセスした場合は、表示されているオプションからそのアイコンを選択します。 それ以外の場合は、_Marketplace_ を検索します。

    :::image type="content" source="media/marketplace.png" alt-text="Marketplace アイコン。":::

1. **[Marketplace]** ページには、使用したいプランの種類に応じて選択できるオプションが 2 つあります。 従量課金制プランまたはコミットメント プランにサインアップすることができます。 従量課金制は一般公開されています。 コミットメント プランは、プライベート プランが承認されているユーザーが利用できます。

   - **従量課金制** をご利用の場合は、_Apache Kafka on Confluent Cloud_ を検索します。 Apache Kafka on Confluent Cloud のプランを選択します。

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="Azure Marketplace プランの検索。":::

   - **コミットメント** をご利用の場合は、 **[View Private offers]\(プライベート プランを表示\)** リンクを選択します。 コミットメントを利用するには、最低支出額にサインアップする必要があります。 このオプションは、このサービスが長期間必要であることがわかっている場合にのみ使用してください。

     :::image type="content" source="media/view-private-offers.png" alt-text="プライベート プランの表示。":::

     _Apache Kafka on Confluent Cloud_ を探します。

     :::image type="content" source="media/select-from-private-offers.png" alt-text="プライベート プランの選択。":::

## <a name="create-resource"></a>リソースを作成する

Apache Kafka on Confluent Cloud のプランを選択したら、アプリケーションを設定する準備は完了です。

まず、Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

サインインした後、[az confluent organization create](/cli/azure/confluent/organization#az_confluent_organization_create) コマンドを使用して、新しい組織のリソースを作成します。

```azurecli
az confluent organization create --name "myOrganization" --resource-group "myResourceGroup" \
 --location "my location" \ 
 --offer-detail id="string" plan-id="string" plan-name="string" publisher-id="string" term-unit="string" \ 
 --user-detail email-address="contoso@microsoft.com" first-name="string" last-name="string" \ 
 --tags Environment="Dev" 
```

> [!NOTE]
> 作成操作が完了する前にコマンドから戻るようにするには、省略可能なパラメーター `--no-wait` を追加します。 Confluent 組織が作成されるまで、操作の実行は続きます。
 
組織の特定のイベントまたは条件が発生するまで CLI の実行を一時停止するには、[az confluent organization wait](/cli/azure/confluent/organization#az_confluent_organization_wait) コマンドを使用します。 たとえば、組織が作成されるまで待つには、次のようにします。

```azurecli
az confluent organization wait --name "myOrganization" --resource-group "myResourceGroup" --created
```

既存の組織の一覧を表示するには、[az confluent organization list](/cli/azure/confluent/organization#az_confluent_organization_list) コマンドを使用します。

サブスクリプション内のすべての組織を表示できます。

```azurecli
az confluent organization list
```

または、リソース グループ内の組織を表示します。

```azurecli
az confluent organization list --resource-group "myResourceGroup"
```

特定の組織のプロパティを表示するには、[az confluent organization show](/cli/azure/confluent/organization#az_confluent_organization_show) コマンドを使用します。

名前で組織を表示できます。

```azurecli
az confluent organization show --name "myOrganization" --resource-group "myResourceGroup"
```

または、リソース ID で組織を表示します。

```azurecli
az confluent organization show --ids "/subscriptions/{SubID}/resourceGroups/{myResourceGroup}/providers/Microsoft.Confluent/organizations/{myOrganization}"
```

エラーが表示された場合は、「[Apache Kafka for Confluent Cloud ソリューションのトラブルシューティング](troubleshoot.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Confluent Cloud リソースを管理する](manage.md)
