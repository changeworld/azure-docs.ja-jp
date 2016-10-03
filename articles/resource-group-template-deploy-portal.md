<properties 
	pageTitle="Azure ポータルを使用した Azure リソースのデプロイ | Microsoft Azure" 
	description="Azure ポータルと Azure Resource Manager を使用して、リソースをデプロイします。" 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2016" 
	ms.author="tomfitz"/>

# Resource Manager テンプレートと Azure ポータルを使用したリソースのデプロイ

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure CLI](resource-group-template-deploy-cli.md)
- [ポータル](resource-group-template-deploy-portal.md)
- [REST API](resource-group-template-deploy-rest.md)

このトピックでは、[Azure ポータル](https://portal.azure.com)と [Azure Resource Manager](resource-group-overview.md) を使用して Azure リソースをデプロイする方法について説明します。リソース管理の詳細については、「[ポータルを使用した Azure リソースの管理](./azure-portal/resource-group-portal.md)」を参照してください。

現時点では、すべてのサービスでポータルまたはリソース マネージャーがサポートされているわけではありません。これらのサービスの場合、[クラシック ポータル](https://manage.windowsazure.com)を使用する必要があります。各サービスの状態については、「[Azure ポータルの可用性チャート](https://azure.microsoft.com/features/azure-portal/availability/)」を参照してください。

## Create resource group

1. 空のリソース グループを作成するには、**[新規]**、**[管理]**、**[リソース グループ]** の順に選択します。

    ![空のリソース グループの作成](./media/resource-group-template-deploy-portal/create-empty-group.png)

2. 名前と場所を指定し、必要に応じてサブスクリプションを選択します。リソース グループにはリソースに関するメタデータが格納されるため、リソース グループの場所を入力する必要があります。場合によっては、コンプライアンス上の理由から、そのメタデータが格納される場所を指定する必要があります。一般に、ほとんどのリソースが存在する場所を指定することをお勧めします。同じ場所を使用することで、テンプレートを簡素化できます。

    ![グループの値の設定](./media/resource-group-template-deploy-portal/set-group-properties.png)

## Marketplace からリソースをデプロイする

リソース グループを作成したら、それに Marketplace からリソースをデプロイできます。Marketplace には、一般的なシナリオに対応する事前定義されたソリューションが用意されています。

1. デプロイを開始するには、**[新規]** を選択し、デプロイするリソースの種類を選択します。次に、デプロイするリソースの特定のバージョンを探します。

    ![deploy resource](./media/resource-group-template-deploy-portal/deploy-resource.png)

2. デプロイする特定のソリューションが表示されない場合は、Marketplace で検索できます。

    ![search marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

3. 選択したリソースの種類によっては、デプロイ前に設定する必要がある、関連する一連のプロパティがあります。それらのオプションは、リソースの種類によって異なるため、ここでは説明しません。すべての種類で、対象リソース グループを選択する必要があります。次の画像は、Web アプリを作成し、先ほど作成したリソース グループにデプロイする方法を示しています。

    ![リソースグループの作成](./media/resource-group-template-deploy-portal/select-existing-group.png)

    また、リソースをデプロイするときにリソース グループを作成することもできます。**[新規作成]** を選択して、リソース グループに名前を付けます。

    ![新しいリソース グループの作成](./media/resource-group-template-deploy-portal/select-new-group.png)

4. デプロイが開始されます。デプロイには数分かかる場合があります。デプロイが完了すると、通知が表示されます。

    ![view notification](./media/resource-group-template-deploy-portal/view-notification.png)

5. リソースのデプロイ後、リソース グループのブレードにある **[追加]** コマンドを使用して、リソース グループにリソースを追加することができます。

    ![リソースの追加](./media/resource-group-template-deploy-portal/add-resource.png)

## カスタム テンプレートからリソースをデプロイする

デプロイを実行するが、Marketplace 内のテンプレートを使用しない場合は、ソリューションのインフラストラクチャを定義するカスタマイズされたテンプレートを作成できます。テンプレートの作成の詳細については、「[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。

1. ポータルからカスタマイズしたテンプレートをデプロイするには、**[新規]** を選択し、**[テンプレートのデプロイ]** を検索してオプションに表示します。

    ![テンプレートのデプロイの検索](./media/resource-group-template-deploy-portal/search-template.png)

2. 使用可能なリソースから **[テンプレートのデプロイ]** を選択します。

    ![テンプレートのデプロイの選択](./media/resource-group-template-deploy-portal/select-template.png)

3. テンプレートのデプロイを開始したら、カスタマイズに利用可能な空のテンプレートを開きます。

    ![テンプレートの作成](./media/resource-group-template-deploy-portal/show-custom-template.png)

    エディターで、デプロイするリソースを定義する JSON 構文を追加します。完了したら、**[保存]** を選択します。JSON 構文の作成に関するガイダンスについては、「[Resource Manager テンプレートのチュートリアル](resource-manager-template-walkthrough.md)」をご覧ください。

    ![テンプレートの編集](./media/resource-group-template-deploy-portal/edit-template.png)

4. あるいは、[Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)から既存のテンプレートを選択できます。これらのテンプレートはコミュニティによって提供されています。さまざまな共通シナリオが取り上げられています。あなたがデプロイしようとしているテンプレートと似たようなテンプレートを追加している人がいるかもしれません。自分のシナリオに一致するテンプレートを検索できます。

    ![クイックスタート テンプレートの選択](./media/resource-group-template-deploy-portal/select-quickstart-template.png)

    エディターで、選択したテンプレートを表示できます。

5. その他の値をすべて指定してから、**[作成]** を選択してテンプレートをデプロイします。

    ![テンプレートのデプロイ](./media/resource-group-template-deploy-portal/create-custom-deploy.png)

## アカウントに保存されたテンプレートからリソースをデプロイする

ポータルでは、Azure アカウントにテンプレートを保存し、後で再デプロイを行うことができます。保存したテンプレートの操作の詳細については、「[Azure ポータルでプライベート テンプレートを使ってみる](./marketplace-consumer/mytemplates-getstarted.md)」を参照してください。

1. 保存したテンプレートを検索するには、**[参照]**、**[テンプレート]** の順に選択します。

    ![テンプレートの参照](./media/resource-group-template-deploy-portal/browse-templates.png)

2. アカウントに保存されたテンプレートの一覧から、使用するテンプレートを選択します。

    ![保存されているテンプレート](./media/resource-group-template-deploy-portal/saved-templates.png)

3. **[デプロイ]** を選択し、保存されたテンプレートを再デプロイします。

    ![保存されているテンプレートのデプロイ](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## 次のステップ

- 監査ログの表示については、「[Resource Manager の監査操作](resource-group-audit.md)」を参照してください。
- デプロイ エラーのトラブルシューティングについては、[Azure ポータルでのリソース グループのデプロイのトラブルシューティング](resource-manager-troubleshoot-deployments-portal.md)に関するページを参照してください。
- デプロイまたはリソース グループからテンプレートを取得するには、「[既存のリソースから Azure Resource Manager テンプレートをエクスポートする](resource-manager-export-template.md)」を参照してください。

<!---HONumber=AcomDC_0921_2016-->