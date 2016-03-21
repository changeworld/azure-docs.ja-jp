<properties 
	pageTitle="Azure ポータルを使用した Azure リソースの管理 | Microsoft Azure" 
	description="含まれるリソースのライフサイクルの境界となる論理グループとして、複数のリソースをグループ化します。" 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/11/2016" 
	ms.author="tomfitz"/>


# Azure ポータルを使用した Azure リソースの管理

## はじめに

Azure リソース マネージャーでは、リソース グループを使ってソリューションのデプロイと管理を行うことができます。このトピックでは、Azure ポータル内でリソース グループを使用する方法を概説します。通常、1 つのリソース グループには、特定の 1 つのアプリケーションに関連するリソースが含まれています。たとえば、公開する Web サイトをホストする Web アプリ、そのサイトが使用するリレーショナル データを格納する SQL Database、および非リレーショナル アセットを格納するストレージ アカウントを、1 つのグループに含めることができます。リソース グループ内のすべてのリソースで同じライフ サイクルを共有する必要があります。リソース マネージャーの詳細については、「[リソース マネージャーの概要](../resource-group-overview.md)」をご覧ください。

現時点では、すべてのサービスでポータルまたはリソース マネージャーがサポートされているわけではありません。それらがサポートされていないサービスの場合、[クラシック ポータル](https://manage.windowsazure.com)を使用する必要があります。各サービスの状態については、「[Azure ポータルで利用できるサービスの表](https://azure.microsoft.com/features/azure-portal/availability/)」を参照してください。

また、Azure PowerShell と Azure CLI でリソースを管理することもできます。これらのインターフェイスの使用方法については、「[Azure リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)」と「[Azure リソース マネージャーでの、Mac、Linux、および Windows 用 Azure CLI の使用](../xplat-cli-azure-resource-manager.md)」を参照してください。


## リソース グループとリソースの作成

空のリソース グループを作成する必要がある場合、**[新規]**、**[管理]**、**[リソース グループ]** の順に選択して作成できます。

![空のリソース グループの作成](./media/resource-group-portal/create-empty-group.png)

名前と場所を指定し、必要に応じて、サブスクリプションを選択します。

![グループの値の設定](./media/resource-group-portal/set-group-properties.png)

リソースをデプロイするときに、作成したリソース グループに対してデプロイすることもできます。次の図は、既存のリソース グループで新しい Web アプリを作成する方法を示しています。

![リソースグループの作成](./media/resource-group-portal/select-existing-group.png)

また、リソースをデプロイするときに新しいリソース グループを作成することもできます。サブスクリプションで既存のリソース グループのいずれかを選択する代わりに、**[新規]** を選択し、リソース グループに名前を付けます。

![新しいリソース グループの作成](./media/resource-group-portal/select-new-group.png)

## リソース グループの参照

**[リソース グループ]** をクリックして、すべてのリソース グループを参照できます。

![リソース グループの参照](./media/resource-group-portal/browse-groups.png)

特定のリソース グループを選択すると、グループ内のすべてのリソースの一覧を含む、そのリソース グループに関する情報が示されたリソース グループのブレードが表示されます。

![リソース グループのサマリ](./media/resource-group-portal/group-summary.png)

リソース グループのブレードには、そのリソース グループ内にあるすべてのリソースに関する課金情報と監視情報を示す統合ビューも表示されます。

![監視および課金](./media/resource-group-portal/monitoring-billing.png)

## サブスクリプションとコストを表示する

サブスクリプションと、すべてのリソースの合計コストに関する情報を表示できます。**[サブスクリプション]** と、表示するサブスクリプションを選択します。選択できるサブスクリプションが 1 つのみの場合もあります。

![subscription](./media/resource-group-portal/select-subscription.png)

サブスクリプション ブレードには、バーン レートが表示されます。

![バーン レート](./media/resource-group-portal/burn-rate.png)

また、リソースの種類別のコストの明細が表示されます。

![リソース コスト](./media/resource-group-portal/cost-by-resource.png)

## インターフェイスのカスタマイズ

リソース グループの概要にすばやくアクセスするために、スタート画面にブレードを固定することができます。

![リソース グループの固定](./media/resource-group-portal/pin-group.png)

また、ブレードのセクションの上にある省略記号 (...) を選択して、スタート画面にブレードのセクションを固定することができます。さらに、ブレードのセクションのサイズをカスタマイズしたり、完全に削除したりできます。次の図は、[イベント] セクションを固定、カスタマイズ、または削除する方法を示しています。

![セクションの固定](./media/resource-group-portal/pin-section.png)

スタート画面に [イベント] セクションを固定すると、イベントの概要がスタート画面に表示されます。

![イベントのスタート画面](./media/resource-group-portal/events-startboard.png)

それを選択すると、イベントに関する詳細情報が即時に表示されます。

## 過去のデプロイの表示

リソース グループのブレードから、このリソース グループの最後のデプロイの日付と状態を確認できます。リンクを選択すると、グループのデプロイの履歴が表示されます。

![最終デプロイ](./media/resource-group-portal/last-deployment.png)

履歴からデプロイを選択すると、そのデプロイの詳細が表示されます。

![デプロイの概要](./media/resource-group-portal/deployment-summary.png)

デプロイ中に実行された個々の操作を確認できます。次の画像は、成功した操作と失敗した操作を示しています。

![操作の詳細](./media/resource-group-portal/operation-details.png)

いずれかの操作を選択すると、その操作の詳細情報が表示されます。以下に示されているように、これは操作が失敗した場合に特に役立ちます。デプロイが失敗した原因をトラブルシューティングするのに役立てることができます。次の画像では、Web サイトの名前が一意ではなかったため、そのサイトがデプロイされなかったことがわかります。

![操作メッセージ](./media/resource-group-portal/operation-message.png)

## 監査ログの表示

監査ログにはデプロイ操作だけではなく、サブスクリプションでリソースに対して実行したすべての管理操作が含まれています。たとえば、組織内のユーザーがアプリを停止した時間を監査ログで確認できます。監査ログを表示するには、**[すべて参照]** と **[監査ログ]** を選択します。

![監査ログの参照](./media/resource-group-portal/browse-audit-logs.png)

操作セクションで、サブスクリプション全般にわたって実行された個々の操作を確認できます。

![監査ログの表示](./media/resource-group-portal/view-audit-log.png)

いずれかの操作を選択すると、その操作を行ったユーザーなど、より詳細な情報を確認できます。

**[フィルター]** を選択して、監査ログに表示される項目をフィルタリングできます。

![ログのフィルター](./media/resource-group-portal/filter-logs.png)

表示する操作のタイプ (属するリソース グループまたはリソース、特定の期間、捜査を開始した特定の呼び出し元、操作レベル) を選択できます。

![フィルター オプション](./media/resource-group-portal/filter-options.png)

## リソース グループへのリソースの追加

リソース グループのブレードにある **[追加]** コマンドを使用して、リソース グループにリソースを追加することができます。

![リソースの追加](./media/resource-group-portal/add-resource.png)

使用可能な一覧から、目的のリソースを選択できます。

## リソース グループの削除

リソース グループでは、その中に含まれているすべてのリソースのライフサイクルを管理することができるため、リソース グループを削除すると、その中に含まれているすべてのリソースが削除されます。また、リソース グループ内にある個々のリソースを削除することもできます。リソース グループに対して他のリソースがリンクされている可能性があるため、リソース グループを削除する場合は注意を払う必要があります。リンクされているリソースは、リソース マップ内で確認できます。リソース グループを削除した結果、意図していない結果が生じることを防止するために、必要な手順を実行できます。リンクされたリソースは削除されませんが、期待どおりに機能しない可能性があります。

![グループの削除](./media/resource-group-portal/delete-group.png)

## リソースのタグ付け

リソース グループやリソースにタグを適用して、アセットを論理的に整理できます。ポータルを使用してタグを処理する方法の詳細については、「[タグを使用した Azure リソースの整理](../resource-group-using-tags.md)」をご覧ください。

## カスタム テンプレートのデプロイ

デプロイを実行するが、Marketplace 内のテンプレートを使用しない場合は、ソリューションのインフラストラクチャを定義するカスタマイズされたテンプレートを作成できます。テンプレートの詳細については、「[Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates.md)」をご覧ください。

ポータルでカスタマイズしたテンプレートをデプロイするには、**[新規]** を選択し、**[テンプレートのデプロイ]** を検索してオプションに表示します。

![テンプレートのデプロイの検索](./media/resource-group-portal/search-template.png)

使用できるリソースから **[テンプレートのデプロイ]** を選択します。

![テンプレートのデプロイの選択](./media/resource-group-portal/select-template.png)

テンプレートのデプロイを開始したら、カスタム テンプレートを作成し、デプロイの値を設定できます。

![テンプレートの作成](./media/resource-group-portal/show-custom-template.png)

## 次のステップ
Getting Started (概要)

- リソース マネージャーでの概念の概要については、「[Azure リソース マネージャーの概要](../resource-group-overview.md)」をご覧ください。
- リソースのデプロイ時に Azure PowerShell を使用する方法の概要については、「[Azure リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。
- リソースのデプロイ時に Azure CLI を使用する方法の概要については、「[Azure リソース管理での Mac、Linux、および Windows 用 Azure CLI の使用](../xplat-cli-azure-resource-manager.md)」をご覧ください。

<!---HONumber=AcomDC_0218_2016-->