---
title: ポータルでワークスペースを作成する
titleSuffix: Azure Machine Learning
description: Azure portal で Azure Machine Learning ワークスペースを作成、表示、削除する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: e2f13cbdca9d6372677bbba24d60f4a73436cfd7
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179287"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Azure portal 内で Azure Machine Learning ワークスペースを作成および管理する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、[Azure Machine Learning ](overview-what-is-azure-ml.md)用の Azure portal で [**Azure Machine Learning ワークスペース**](concept-workspace.md)を作成、表示、削除します。  ワークスペースを初めて利用する際にはポータルが最も手軽ですが、ニーズに変化が生じたり自動化の要件が高まったりした場合は、[CLI](reference-azure-machine-learning-cli.md)、[Python コード](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)、または [VS Code 拡張機能](tutorial-setup-vscode-extension.md)を使用して、ワークスペースを作成したり、削除したりすることもできます。

## <a name="create-a-workspace"></a>ワークスペースの作成

ワークスペースを作成するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

1. ご利用の Azure サブスクリプションの資格情報を使って、[Azure portal](https://portal.azure.com/) にサインインします。 

1. Azure portal の左上隅にある **[+ リソースの作成]** を選択します。

      ![新しいリソースを作成](./media/how-to-manage-workspace/create-workspace.gif)

1. 検索バーを使用して、**Machine Learning** を見つけます。

1. **[Machine Learning]** を選択します。

1. **[Machine Learning]** ウィンドウで、 **[作成]** を選択して開始します。

1. 新しいワークスペースを構成するには、次の情報を指定します。

   フィールド|説明 
   ---|---
   ワークスペース名 |ワークスペースを識別する一意の名前を入力します。 この例では、**docs-ws** を使用します。 名前は、リソース グループ全体で一意である必要があります。 覚えやすく、他のユーザーが作成したワークスペースと区別しやすい名前を使用します。 ワークスペース名では、大文字と小文字は区別されません。
   サブスクリプション |使用する Azure サブスクリプションを選択します。
   Resource group | サブスクリプションの既存のリソース グループを使用するか、任意の名前を入力して新しいリソース グループを作成します。 リソース グループは、Azure ソリューションの関連するリソースを保持します。 この例では、**docs-aml** を使用します。 既存のリソース グループを使用するには、*共同作成者*または*所有者*のロールが必要です。  アクセスの詳細については、「[Azure Machine Learning ワークスペースへのアクセスの管理](how-to-assign-roles.md)」の記事をご覧ください。
   場所 | ユーザーとデータ リソースに最も近い場所を選択し、ワークスペースを作成します。
   場所 | ユーザーとデータ リソースに最も近い場所を選択し、ワークスペースを作成します。
   ワークスペースのエディション | **[Basic]** または **[Enterprise]** を選択します。  このワークスペース エディションによって、アクセスでき、価格設定の対象となる機能が決まります。 詳細については、[Basic および Enterprise Edition のオファリング](overview-what-is-azure-ml.md#sku)に関するページを参照してください。 

    ![ワークスペースを構成する](./media/how-to-manage-workspace/select-edition.png)

1. ワークスペースの構成が完了したら、 **[確認および作成]** を選択します。 必要に応じて、[ネットワーク](#networking)に関するセクションと[詳細設定](#advanced)に関するセクションを使用し、ワークスペースに対してさらに多くの設定を行います。
2. 設定を確認し、追加の変更または訂正があれば実行します。 設定に問題がなければ、 **[作成]** を選択します。

   > [!Warning] 
   > クラウドへのワークスペースの作成には数分かかる場合があります。

   プロセスが完了すると、デプロイ成功メッセージが表示されます。 
 
 1. 新しいワークスペースを表示するには、 **[Go to resource]\(リソースに移動\)** を選択します。


### <a name="networking"></a>ネットワーク

> [!IMPORTANT]
> お使いのワークスペースでのプライベート エンドポイントと仮想ネットワークの使用の詳細については、[ネットワークの分離とプライバシー](how-to-enable-virtual-network.md)に関する記事を参照してください。

1. 既定のネットワーク構成では、公衆インターネット上の__パブリック エンドポイント__を使用します。 作成した Azure Virtual Network にお使いのワークスペースへのアクセスを制限するには、代わりに __[接続方法]__ として __[プライベート エンドポイント]__ (プレビュー) を選択し、 __[+ 追加]__ を使用してそのエンドポイントを構成します。

   > [!IMPORTANT]
   > Azure Machine Learning ワークスペースでのプライベート エンドポイントの使用は、現在パブリック プレビュー段階です。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="プライベート エンドポイントを選択する":::

1. __[プライベート エンドポイントの作成]__ フォームで、使用する場所、名前、仮想ネットワークを設定します。 そのエンドポイントをプライベート DNS ゾーンと共に使用する場合は、 __[プライベート DNS ゾーンと統合する]__ を選択し、 __[プライベート DNS ゾーン]__ フィールドを使用してそのゾーンを選択します。 __[OK]__ を選択してエンドポイントを作成します。 

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="プライベート エンドポイントを作成する":::

1. ネットワークの構成が完了したら、 __[確認および作成]__ を選択するか、オプションの __[詳細]__ 構成に進みます。

    > [!WARNING]
    > プライベート エンドポイントを作成すると、__privatelink.api.azureml.ms__ という名前の新しいプライベート DNS ゾーンが作成されます。 これには、仮想ネットワークへのリンクが含まれます。 プライベート エンドポイントがあるワークスペースを同じリソース グループ内に複数作成した場合、最初のプライベート エンドポイントの仮想ネットワークしか DNS ゾーンに追加されない場合があります。 追加のワークスペースまたはプライベート エンドポイントで使用される仮想ネットワークを追加するには、次の手順に従います。
    > 
    > 1. [Azure portal](https://portal.azure.com) で、そのワークスペースが含まれているリソース グループを選択します。 次いで __privatelink.api.azureml.ms__ という名前のプライベート DNS ゾーンのリソースを選択します。
    > 2. __[設定]__ で、 __[仮想ネットワーク リンク]__ を選択します。
    > 3. __[追加]__ を選択します。 __[仮想ネットワーク リンクの追加]__ ページで一意の __[リンク名]__ を指定し、次いで追加する__仮想ネットワーク__を選択します。 __[OK]__ を選択して、ネットワーク リンクを追加します。
    >
    > 詳細については、「[Azure プライベート エンドポイントの DNS 構成](/azure/private-link/private-endpoint-dns)」をご覧ください。

### <a name="advanced"></a>詳細設定

既定では、ワークスペースのメトリックとメタデータは、Microsoft が管理する Azure Cosmos DB インスタンスに格納されます。 このデータは Microsoft のマネージド キーで暗号化されます。 

Microsoft がお使いのワークスペースで収集するデータを制限するには、 __[High business impact workspace]__ \(業務への影響が大きいワークスペース\) を選択します。

> [!IMPORTANT]
> High Business Impact の選択は、ワークスペースの作成時にのみ実行できます。 ワークスペースの作成後にこの設定を変更することはできません。

__Enterprise__ バージョンの Azure Machine Learning を使用している場合は、代わりに自分独自のキーを使用できます。 これを行うと、Azure サブスクリプションにメトリックとメタデータを格納する Azure Cosmos DB インスタンスが作成されます。 自分で用意したキーを使用するには、次の手順を実行します。

> [!IMPORTANT]
> これらの手順を実行する前に、まず次のアクションを実行する必要があります。
>
> 1. サブスクリプションに対する共同作成者のアクセス許可を使用して、__Machine Learning アプリ__ (ID とアクセスの管理) を承認します。
> 1. [カスタマー マネージド キーの構成](/azure/cosmos-db/how-to-setup-cmk)に関する記事の手順に従って以下を行います。
>     * Azure Cosmos DB プロバイダーを登録する
>     * Azure Key Vault を作成して構成する
>     * キーを生成する
>
>     Azure Cosmos DB インスタンスを手動で作成する必要はありません。ワークスペースの作成時に作成されます。 この Azure Cosmos DB インスタンスは、`<your-resource-group-name>_<GUID>` というパターンに基づく名前を使用して、別のリソース グループ内に作成されます。
>
> ワークスペースの作成後にこの設定を変更することはできません。 ワークスペースによって使用されている Azure Cosmos DB を削除する場合は、それを使用しているワークスペースも削除する必要があります。

1. __[カスタマー マネージド キー]__ を選択し、 __[クリックしてキーを選択します]__ を選択します。

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="カスタマー マネージド キー":::。

1. __[Azure Key Vault からのキーの選択]__ フォームから既存の Azure Key Vault、それに含まれているキー、およびそのキーのバージョンを選択します。 このキーは、Azure Cosmos DB に格納されているデータを暗号化するために使用されます。 最後に、 __[選択]__ ボタンをクリックして、このキーが使用されるようにします。

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="キーを選択する":::

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Enterprise Edition へのアップグレード

Basic Edition から Enterprise Edition にワークスペースをアップグレードすることで、ローコード エクスペリエンスや強化されたセキュリティ機能などの拡張機能を利用できます。

1. [Azure Machine Learning Studio](https://ml.azure.com) にサインインします。

1. アップグレードするワークスペースを選択します。

1. ページの右上部にある **[詳細]** を選択します。

   [![ワークスペースをアップグレードする](./media/how-to-manage-workspace/upgrade.png)](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. 表示されるウィンドウで **[アップグレード]** を選択します。


> [!IMPORTANT]
> Enterprise Edition ワークスペースを Basic Edition ワークスペースにダウングレードすることはできません。

### <a name="download-a-configuration-file"></a>構成ファイルをダウンロードする

1. [コンピューティング インスタンス](tutorial-1st-experiment-sdk-setup.md#azure)を作成する場合は、この手順をスキップしてください。

1. このワークスペースを参照するローカル環境でコードを使用する場合は、ワークスペースの **[概要]** セクションから **[config. json をダウンロード]** を選択します。  

   ![config.json をダウンロードする](./media/how-to-manage-workspace/configure.png)
   
   このファイルは、Python スクリプトまたは Jupyter Notebook を含むディレクトリ構造内に置きます。 それは、同じディレクトリ内、 *.azureml* という名前のサブディレクトリ内、または親ディレクトリ内に置くことができます。 コンピューティング インスタンスを作成するとき、このファイルは VM 上の正しいディレクトリに自動的に追加されます。

## <a name="find-a-workspace"></a><a name="view"></a>ワークスペースの検索

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 上部の検索フィールドに「**Machine Learning**」と入力します。  

1. **[Machine Learning]** を選択します。

   ![Azure Machine Learning ワークスペースの検索](./media/how-to-manage-workspace/find-workspaces.png)

1. 見つかったワークスペースの一覧にひととおり確認します。 サブスクリプション、リソース グループ、場所に基づいてフィルター処理できます。  

1. ワークスペースを選択して、そのプロパティを表示します。

## <a name="delete-a-workspace"></a>ワークスペースを削除する

[Azure portal](https://portal.azure.com/) で、削除するワークスペースの上部にある **[削除]** を選択します。

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="ワークスペースの削除":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="resource-provider-errors"></a>リソース プロバイダーのエラー

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>ワークスペースの移動

> [!WARNING]
> Azure Machine Learning ワークスペースを別のサブスクリプションに移動したり、所有するサブスクリプションを新しいテナントに移動したりすることは、サポートされていません。 エラーの原因になります。

### <a name="deleting-the-azure-container-registry"></a>Azure Container Registry の削除

Azure Machine Learning ワークスペースでは、一部の操作に対して Azure Container Registry (ACR) が使用されます。 これにより ACR インスタンスは、最初に必要になったときに自動的に作成されます。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>次のステップ

ワークスペースを使用して、Azure Machine Learning でモデルをビルド、トレーニング、デプロイする方法については、完全版のチュートリアルをご利用ください。

> [!div class="nextstepaction"]
> [チュートリアル:モデルをトレーニングする](tutorial-train-models-with-aml.md)
