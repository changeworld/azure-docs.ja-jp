---
title: BLOB ストレージ イベントを Web エンドポイントに送信する - テンプレート
description: Azure Event Grid と Azure Resource Manager テンプレートを使用して BLOB ストレージ アカウントを作成し、そのイベントをサブスクライブします。 Webhook にイベントを送信します。
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 01/15/2020
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: 73f0e6cf5d7ebb3ae36d4048ce5f36f5c0547286
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122711"
---
# <a name="route-blob-storage-events-to-web-endpoint-by-using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して BLOB ストレージ イベントを Web エンドポイントにルーティングする
Azure Event Grid は、クラウドのイベント処理サービスです。 この記事では、**Azure Resource Manager テンプレート**を使用して BLOB ストレージ アカウントを作成し、その BLOB ストレージのイベントをサブスクライブします。次に、イベントをトリガーして結果を表示します。 通常は、イベント データを処理し、アクションを実行するエンドポイントにイベントを送信します。 ただし、この記事では、単純化するために、メッセージを収集して表示する Web アプリにイベントを送信します。

[Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)は JavaScript Object Notation (JSON) ファイルであり、プロジェクトのインフラストラクチャと構成が定義されています。 このテンプレートでは、デプロイしようとしているものを、それを作成する一連のプログラミング コマンドを記述しなくても記述できる、宣言型の構文を使用しています。 Resource Manager テンプレートの開発の詳細については、[Resource Manager のドキュメント](/azure/azure-resource-manager/)を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="prerequisites"></a>前提条件
### <a name="create-a-message-endpoint"></a>メッセージ エンドポイントの作成
BLOB ストレージのイベントをサブスクライブする前に、イベント メッセージのエンドポイントを作成しましょう。 通常、エンドポイントは、イベント データに基づくアクションを実行します。 このクイック スタートを簡素化するために、イベント メッセージを表示する[構築済みの Web アプリ](https://github.com/Azure-Samples/azure-event-grid-viewer)をデプロしします。 デプロイされたソリューションには、App Service プラン、App Service Web アプリ、および GitHub からのソース コードが含まれています。

1. **[Deploy to Azure]\(Azure にデプロイ\)** を選択して、ソリューションをサブスクリプションにデプロイします。 Azure portal で、パラメーターの値を指定します。

    [Azure へのデプロイ](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)
1. デプロイが完了するまでに数分かかる場合があります。 デプロイが成功した後で、Web アプリを表示して、実行されていることを確認します。 Web ブラウザーで `https://<your-site-name>.azurewebsites.net` にアクセスします

1. サイトは表示されますが、イベントはまだ送信されていません。

   ![新しいサイトを表示する](./media/blob-event-quickstart-portal/view-site.png)


## <a name="create-a-storage-account-with-an-event-grid-subscription"></a>Event Grid サブスクリプションを持つストレージ アカウントを作成する
このクイック スタートで使用されるテンプレートは [Azure クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)からのものです。

[!code-json[<Azure Resource Manager template create Blob strage Event Grid subscription>](~/quickstart-templates/101-event-grid-subscription-and-storage/azuredeploy.json)]

テンプレートでは、次の 2 つの Azure リソースが定義されています。

* **Microsoft.Storage/storageAccounts**: Azure のストレージ アカウントを作成します。
* **"Microsoft. Storage/storageAccounts/providers/eventSubscriptions**: ストレージ アカウントの Azure Event Grid サブスクリプションを作成します。 

1. Azure にサインインして、テンプレートを開くには、次のリンクを選択します。 このテンプレートを使用すると、キー コンテナーとシークレットが作成されます。

    [Azure へのデプロイ](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json)
2. **エンドポイント**を指定するには、Web アプリの URL を入力し、ホーム ページの URL に `api/updates` を追加します。
3. **[購入]** を選択してテンプレートをデプロイします。 

  テンプレートをデプロイするために、ここでは Azure portal を使用します。 Azure PowerShell、Azure CLI、および REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。

> [!NOTE]
> その他の Azure Event Grid テンプレートのサンプルについては、[こちら](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid)を参照してください。

## <a name="validate-the-deployment"></a>デプロイの検証
Web アプリをもう一度表示し、その Web アプリにサブスクリプションの検証イベントが送信されたことに注目します。 目のアイコンを選択してイベント データを展開します。 Event Grid は検証イベントを送信するので、エンドポイントはイベント データを受信することを確認できます。 Web アプリには、サブスクリプションを検証するコードが含まれています。

![サブスクリプション イベントの表示](./media/blob-event-quickstart-portal/view-subscription-event.png)

では、イベントをトリガーして、Event Grid がメッセージをエンドポイントに配信するようすを見てみましょう。

BLOB ストレージのイベントをトリガーするには、ファイルをアップロードします。 このファイルは、特定のコンテンツを必要としません。 この記事では、testfile.txt という名前のファイルがあると仮定していますが、任意のファイルを使用できます。

ファイルを Azure BLOB ストレージにアップロードすると、Event Grid から、サブスクライブ時に構成したエンドポイントにメッセージが送信されます。 メッセージは JSON 形式であり、1 つまたは複数のイベントの配列が含まれています。 次の例の JSON メッセージには、1 つのイベントの配列が含まれています。 Web アプリを表示して、BLOB 作成イベントが受信されたことを確認します。 

![結果の表示](./media/blob-event-quickstart-portal/view-results.png)



## <a name="clean-up-resources"></a>リソースをクリーンアップする
不要になったら、[リソース グループを削除します](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group
)。 

## <a name="next-steps"></a>次のステップ
Azure Resource Manager テンプレートの詳細については、次の記事を参照してください。

- [Azure Resource Manager のドキュメント](/azure/azure-resource-manager)
- [Azure Resource Manager テンプレートのリソースを定義する](/azure/templates/)
- [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/)
- [Azure Event Grid テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid)。
