---
title: クイック スタート:Python 用 Azure 管理クライアント ライブラリ
description: このクイックスタートでは、Python 用の Azure 管理クライアント ライブラリの使用を開始します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: fb908cdcf3e235654effc043de29e599a48179d4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879592"
---
[リファレンス ドキュメント](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices) | [パッケージ (PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/) | [サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="python-prerequisites"></a>Python の前提条件

* 有効な Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)。
* [Python 3.x](https://www.python.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

お気に入りのエディターまたは IDE で新しい Python アプリケーションを作成し、コンソール ウィンドウでプロジェクトに移動します。

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

次のようにして、クライアント ライブラリをインストールできます。

```console
pip install azure-mgmt-cognitiveservices
```

Visual Studio IDE を使用している場合、クライアント ライブラリは、ダウンロード可能な NuGet パッケージとして入手できます。

### <a name="import-libraries"></a>ライブラリをインポートする

Python スクリプトを開き、以下のライブラリをインポートします。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_imports)]

## <a name="authenticate-the-client"></a>クライアントを認証する

スクリプトのルートに以下のフィールドを追加し、作成したサービス プリンシパルと Azure アカウント情報を使用して値を指定します。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_constants)]

次に、次のコードを追加して、**CognitiveServicesManagementClient** オブジェクトを構築します。 このオブジェクトは、すべての Azure 管理操作に必要です。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_auth)]

## <a name="create-a-cognitive-services-resource-python"></a>Cognitive Services リソースを作成する (Python)

新しい Cognitive Services リソースを作成してサブスクライブするには、**Create** 関数を使用します。 この関数で、渡したリソース グループに新しい課金対象リソースが追加されます。 新しいリソースを作成するときには、使用するサービスの "種類"、その価格レベル (つまり SKU)、および Azure の場所を把握している必要があります。 次の関数は、これらのすべての引数を受け取り、リソースを作成します。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>サービスと価格レベルを選択する

新しいリソースを作成するときには、使用するサービスの "種類" と、必要な[価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/) (つまり SKU) を把握する必要があります。 リソースを作成するときに、この情報と他の情報をパラメーターとして使用します。 次の関数は、利用可能な Cognitive Service の "種類" を一覧表示します。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>リソースを表示する

Azure アカウントのすべてのリソース (すべてのリソース グループにわたる) を表示するには、次の関数を使用します。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list)]

## <a name="delete-a-resource"></a>リソースの削除

次の関数は、特定のリソース グループから指定されたリソースを削除します。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_delete)]

## <a name="call-management-functions"></a>管理関数を呼び出す

以下のコードをスクリプトの末尾に追加して、上の関数を呼び出します。 このコードは、使用可能なリソースの一覧表示、サンプル リソースの作成、所有しているリソースの一覧表示、およびサンプル リソースの削除を行います。

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_calls)]

## <a name="run-the-application"></a>アプリケーションの実行

コマンド ラインで `python` コマンドを使用して、アプリケーションを実行します。

```console
python <your-script-name>.py
```

## <a name="see-also"></a>関連項目

* Cognitive Services を安全に使用する方法については、「 **[Azure Cognitive Services に対する要求の認証](../../authentication.md)** 」をご覧ください。
* Cognitive Services 内のさまざまなカテゴリの一覧を入手するには、「 **[Azure Cognitive Services とは](../../what-are-cognitive-services.md)** 」をご覧ください。
* Cognitive Services がサポートする自然言語の一覧を確認するには、 **[自然言語のサポート](../../language-support.md)** に関する記事をご覧ください。
* Cognitive Services をオンプレミスで使用する方法については、 **[コンテナーとしての Cognitive Services の使用](../../cognitive-services-container-support.md)** に関する記事をご覧ください。
* Cognitive Services の使用コストを見積もるには、 **[Cognitive Services のコストの計画および管理](../../plan-manage-costs.md)** に関する記事をご覧ください。
* Management SDK の詳細については、 **[Azure Management SDK のリファレンス ドキュメント](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices)** を参照してください。
