---
title: Azure CLI を使用して Cognitive Services リソースを作成する
titleSuffix: Azure Cognitive Services
description: Azure コマンド ライン インターフェイスを使用してリソースを作成し、それをサブスクライブすることによって、Azure Cognitive Services の使用を開始します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: Cognitive Services, コグニティブ インテリジェンス, コグニティブ ソリューション, AI サービス
ms.topic: quickstart
ms.date: 06/04/2021
ms.author: aahi
ms.openlocfilehash: 1c838a9b805eed20fec9f9feabf4aa24c58b1cd5
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429942"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>クイックスタート: Azure コマンド ライン インターフェイス (CLI) を使用して Cognitive Services リソースを作成する

このクイックスタートでは、[Azure コマンド ライン インターフェイス (CLI)](/cli/azure/install-azure-cli) を使用した Azure Cognitive Services の基本操作について説明します。

Azure Cognitive Services は、開発者が直接的な人工知能 (AI) またはデータ サイエンスのスキルや知識がなくてもコグニティブかつインテリジェントなアプリケーションを構築できる、REST API シリーズとクライアント ライブラリ SDK を含むクラウドベースのサービスです。 開発者は Azure Cognitive Services を使用して、見たり、聞いたり、話したり、理解したり、推論し始めたりできるコグニティブ ソリューションを使用したコグニティブ機能をそのアプリケーションに容易に追加することができます。

Cognitive Services は、ご利用の Azure サブスクリプションに作成した Azure [リソース](../azure-resource-manager/management/manage-resources-portal.md)によって表されます。 リソースの作成後、自動的に生成されたキーとエンドポイントを使用して、自分のアプリケーションの認証を行います。

このクイックスタートでは、[Azure コマンドライン インターフェイス (CLI)](/cli/azure/install-azure-cli) を使用して、Azure Cognitive Services にサインアップし、単一サービスまたはマルチサービスを持つアカウントを作成する方法を学習します。 これらのサービスは Azure [リソース](../azure-resource-manager/management/manage-resources-portal.md)によって表され、Azure Cognitive Services APIs の中の 1 つ以上の API に接続できます。

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>前提条件

* 有効な Azure サブスクリプション - 無料[アカウントを作成](https://azure.microsoft.com/free/cognitive-services)します。
* [Azure コマンド ライン インターフェイス (CLI)](/cli/azure/install-azure-cli)
* [!INCLUDE [contributor-requirement](./includes/quickstarts/contributor-requirement.md)]
* [!INCLUDE [terms-azure-portal](./includes/quickstarts/terms-azure-portal.md)]

## <a name="install-the-azure-cli-and-sign-in"></a>Azure CLI をインストールしてサインインする

[Azure CLI](/cli/azure/install-azure-cli) をインストールします。 CLI のローカル インストールにサインインするには、[az login](/cli/azure/reference-index#az_login) コマンドを実行します。

```azurecli-interactive
az login
```

また、緑色の **[使ってみる]** ボタンを使用して、お使いのブラウザーでこれらのコマンドを実行することもできます。

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>新しい Azure Cognitive Services リソース グループを作成する

Cognitive Services リソースを作成する前に、リソースを格納するための Azure リソース グループを用意する必要があります。 新しいリソースを作成するときに、新しいリソース グループを作成するか、既存のものを使用するかを選択できます。 この記事では、新しいリソース グループを作成する方法を示します。

### <a name="choose-your-resource-group-location"></a>リソース グループの場所を選択する

リソースを作成するには、お使いのサブスクリプションに使用できるいずれかの Azure の場所が必要です。 使用できる場所の一覧を取得するには、[az account list-locations](/cli/azure/account#az_account_list_locations) コマンドを使用します。 ほとんどの Cognitive Services は複数の場所からアクセスできます。 お客様に最も近いものを選択するか、サービスに使用できる場所を確認します。

> [!IMPORTANT]
> * Azure Cognitive Services を呼び出すときに必要になるので、Azure の場所は記憶しておいてください。
> * 一部の Cognitive Services を使用できるかどうかは、リージョンによって異なります。 詳細については、「[リージョン別の Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)」を参照してください。

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Azure の場所が決まったら、Azure CLI で [az group create](/cli/azure/group#az_group_create) コマンドを使用して新しいリソース グループを作成します。

次の例では、Azure の場所 `westus2` を、お使いのサブスクリプションで使用できる Azure の場所の 1 つに置き換えます。

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services リソースの作成

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Cognitive サービスと価格レベルを選択する

新しいリソースを作成するときには、必要な [価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/) (つまり sku) と共に、使用するサービスの "種類" を把握する必要があります。 リソースを作成するときは、この情報と他の情報をパラメーターとして使用します。

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]


[!INCLUDE [SKUs and pricing](./includes/quickstarts/sku-pricing.md)]

使用できる Cognitive Services の "種類" の一覧を確認するには、[az cognitiveservices account list-kinds](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_kinds) コマンドを使用します。

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>リソース グループに新しいリソースを追加する

新しい Cognitive Services リソースを作成してサブスクライブするには、[az cognitiveservices account create](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_create) コマンドを使用します。 このコマンドで、以前に作成したリソース グループに新しい課金対象リソースが追加されます。 新しいリソースを作成するときには、使用するサービスの種類、その価格レベル (つまり sku)、および Azure の場所を把握している必要があります。

次のコマンドで、`anomaly-detector-resource` という名前の Anomaly Detector 用の F0 (無料) リソースを作成できます。

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>リソースのキーを取得する

コマンドライン インターフェイス (CLI) のローカル インストールにログインするには、[az login](/cli/azure/reference-index#az_login) コマンドを使用します。

```azurecli-interactive
az login
```

Cognitive Service リソースのキーを取得するには、[az cognitiveservices account keys list](/cli/azure/cognitiveservices/account/keys#az_cognitiveservices_account_keys_list) コマンドを使用します。

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>価格レベルと請求

価格レベル (および請求される金額) は、認証情報を使用して送信するトランザクションの数に基づきます。 各価格レベルにより、以下が指定されます。
* 1 秒あたりに許可されるトランザクションの最大数 (TPS)。
* 価格レベル内で有効にされるサービス機能。
* 事前に定義された数のトランザクションのコスト。 この量を超えると、サービスの「[価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)」で指定されている追加料金が発生します。

## <a name="get-current-quota-usage-for-your-resource"></a>現在のリソースのクォータ使用量を取得する

Cognitive Service リソースのキーを取得するには、[az cognitiveservices account list-usage](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_usage) コマンドを使用します。

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services リソースをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、そのグループに含まれている他のリソースも削除されます。

リソース グループとそれに関連付けられているリソースを削除するには、az group delete コマンドを使用します。

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

削除されたリソースを復旧する必要がある場合は、[削除された Cognitive Services リソースの復旧](manage-resources.md)に関するページを参照してください。

## <a name="see-also"></a>関連項目

* Cognitive Services を安全に使用する方法については、「 **[Azure Cognitive Services に対する要求の認証](authentication.md)** 」をご覧ください。
* Cognitive Services 内のさまざまなカテゴリの一覧を入手するには、「 **[Azure Cognitive Services とは](./what-are-cognitive-services.md)** 」をご覧ください。
* Cognitive Services がサポートする自然言語の一覧を確認するには、 **[自然言語のサポート](language-support.md)** に関する記事をご覧ください。
* Cognitive Services をオンプレミスで使用する方法については、 **[コンテナーとしての Cognitive Services の使用](cognitive-services-container-support.md)** に関する記事をご覧ください。
* Cognitive Services の使用コストを見積もるには、 **[Cognitive Services のコストの計画および管理](plan-manage-costs.md)** に関する記事をご覧ください。
