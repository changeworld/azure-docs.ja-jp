<properties 
	pageTitle="Azure ポータルを使用した Azure リソースの管理 | Microsoft Azure" 
	description="Azure ポータルと Azure Resource Manager を使用して、リソースをデプロイおよび管理します。リソースにタグ付けする方法や監査ログを表示する方法について説明します。" 
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
	ms.date="05/16/2016" 
	ms.author="tomfitz"/>


# Azure ポータルを使用した Azure リソースのデプロイと管理

## はじめに

このトピックでは、[Azure ポータル](https://portal.azure.com)と [Azure Resource Manager](../resource-group-overview.md) を使用して Azure リソースをデプロイおよび管理する方法について説明します。

現時点では、すべてのサービスでポータルまたはリソース マネージャーがサポートされているわけではありません。それらがサポートされていないサービスの場合、[クラシック ポータル](https://manage.windowsazure.com)を使用する必要があります。各サービスの状態については、[Azure ポータルで利用できるサービスの表](https://azure.microsoft.com/features/azure-portal/availability/)を参照してください。

また、Azure PowerShell と Azure CLI でリソースを管理することもできます。これらのインターフェイスの使用方法の詳細については、「[Azure Resource Manager での Azure PowerShell の使用](../powershell-azure-resource-manager.md)」と「[Azure Resource Manager での、Mac、Linux、および Windows 用 Azure CLI の使用](../xplat-cli-azure-resource-manager.md)」を参照してください。Visual Studio によるソリューションのデプロイの詳細については、「[Visual Studio での Azure リソース グループの作成とデプロイ](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)」を参照してください。

## Create resource group

空のリソース グループを作成するには、**[新規]**、**[管理]**、**[リソース グループ]** の順に選択します。

![空のリソース グループの作成](./media/resource-group-portal/create-empty-group.png)

名前と場所を指定し、必要に応じて、サブスクリプションを選択します。

![グループの値の設定](./media/resource-group-portal/set-group-properties.png)

## リソースをデプロイする

リソース グループを作成したら、それにリソースをデプロイできます。デプロイを開始するには、**[新規]** を選択し、デプロイするリソースの種類を選択します。

![deploy resource](./media/resource-group-portal/deploy-resource.png)

デプロイするリソースの種類が表示されない場合は、Marketplace で検索できます。

![search marketplace](./media/resource-group-portal/search-resource.png)

選択したリソースの種類によっては、デプロイ前に設定する必要がある、関連する一連のプロパティがあります。それらのオプションは、リソースの種類によって異なるため、ここでは説明しません。すべての種類で、対象リソース グループを選択する必要があります。次の図は、新しい Web アプリを作成し、先ほど作成したリソース グループにデプロイする方法を示しています。

![リソースグループの作成](./media/resource-group-portal/select-existing-group.png)

また、リソースをデプロイするときに新しいリソース グループを作成することもできます。サブスクリプションで既存のリソース グループのいずれかを選択する代わりに、**[新規]** を選択し、リソース グループに名前を付けます。

![新しいリソース グループの作成](./media/resource-group-portal/select-new-group.png)

デプロイが開始されます。この処理には数分かかります。デプロイが完了すると、通知が表示されます。

![view notification](./media/resource-group-portal/view-notification.png)

リソースのデプロイ後、場合によっては、リソースをグループに追加する必要があるか判断します。リソース グループのブレードにある **[追加]** コマンドを使用して、リソース グループにリソースを追加することができます。

![リソースの追加](./media/resource-group-portal/add-resource.png)

## テンプレートをエクスポートする

リソース グループを設定したら、リソース グループのリソース マネージャー テンプレートを表示できます。テンプレートのエクスポートには、2 つの利点があります。

1. ソリューションの将来のデプロイを簡単に自動化できます。すべてのインフラストラクチャがテンプレートに定義されているためです。

2. ソリューションを表す JavaScript Object Notation (JSON) を見ることでテンプレートの構文に詳しくなります。

> [AZURE.NOTE] テンプレートのエクスポート機能はプレビューの段階にあり、現在のところ、一部の種類のリソースでは、テンプレートをエクスポートできません。テンプレートのエクスポートを試行したとき、一部のリソースがエクスポートされなかったというエラーが表示されることがあります。必要に応じて、ダウンロード後、エクスポートされなかったリソースをテンプレート内で手動で定義できます。

手順については、「[既存のリソースから Azure Resource Manager テンプレートをエクスポートする](../resource-manager-export-template/)」を参照してください。

## リソース グループを管理する

**[リソース グループ]** をクリックして、すべてのリソース グループを参照できます。

![リソース グループの参照](./media/resource-group-portal/browse-groups.png)

特定のリソース グループを選択すると、グループ内のすべてのリソースの一覧を含む、そのリソース グループに関する情報が示されたリソース グループのブレードが表示されます。

![リソース グループのサマリ](./media/resource-group-portal/group-summary.png)

概要の下の **[セクションを追加]** を選択すると、さらにグラフや表を [リソース グループ] ブレードに追加できます。

![add section](./media/resource-group-portal/add-section.png)

ブレードに含める情報を選択するためのタイル ギャラリーが表示されます。表示されるタイルの種類は、リソースの種類でフィルター処理されます。別のリソースを選択すると、利用可能なタイルが変化します。

![add section](./media/resource-group-portal/tile-gallery.png)

必要なタイルを使用可能なスペースにドラッグします。

![drag tile](./media/resource-group-portal/drag-tile.png)

ポータルの上部にある **[完了]** を選択すると、新しいビューがブレードに追加されます。

![show tile](./media/resource-group-portal/show-lens.png)

リソース グループにすばやくアクセスするために、ブレードをダッシュボードにピン留めできます。

![リソース グループの固定](./media/resource-group-portal/pin-group.png)

また、ブレードのセクションは、そのセクションの上にある省略記号 (...) を選択すると、ダッシュボードにピン留めできます。さらに、ブレードのセクションのサイズをカスタマイズしたり、完全に削除したりできます。次の図は、[CPU とメモリ] セクションをピン留め、カスタマイズ、または削除する方法を示しています。

![セクションの固定](./media/resource-group-portal/pin-cpu-section.png)

セクションをダッシュボードにピン留めすると、ダッシュボード上に概要が表示されます。

![view dashboard](./media/resource-group-portal/view-startboard.png)

さらに、それを選択すると、データの詳細情報が即時に表示されます。

リソース グループでは、その中に含まれているすべてのリソースのライフサイクルを管理することができるため、リソース グループを削除すると、その中に含まれているすべてのリソースが削除されます。また、リソース グループ内にある個々のリソースを削除することもできます。他のリソース グループのリソースがリンクされている可能性があるため、リソース グループを削除する場合は注意を払う必要があります。リンクされたリソースは削除されませんが、期待どおりに機能しない可能性があります。

![グループの削除](./media/resource-group-portal/delete-group.png)

## リソースへのタグ付け

リソース グループやリソースにタグを適用して、アセットを論理的に整理できます。ポータルを使用してタグを処理する方法の詳細については、「[タグを使用した Azure リソースの整理](../resource-group-using-tags.md)」をご覧ください。

## 保存されているテンプレートをデプロイする

アカウントにテンプレートを保存した場合、後でそれを表示できます。その場合、**[参照]** と **[テンプレート]** を選択します。

![テンプレートの参照](./media/resource-group-portal/browse-templates.png)

テンプレートの独自のコレクションが表示されます。

![テンプレート コレクションの表示](./media/resource-group-portal/show-template-collection.png)


## カスタム テンプレートをデプロイする

デプロイを実行するが、Marketplace 内のテンプレートを使用しない場合は、ソリューションのインフラストラクチャを定義するカスタマイズされたテンプレートを作成できます。テンプレートの詳細については、「[Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates.md)」をご覧ください。

ポータルからカスタマイズしたテンプレートをデプロイするには、**[新規]** を選択し、**[テンプレートのデプロイ]** を検索してオプションに表示します。

![テンプレートのデプロイの検索](./media/resource-group-portal/search-template.png)

使用可能なリソースから **[テンプレートのデプロイ]** を選択します。

![テンプレートのデプロイの選択](./media/resource-group-portal/select-template.png)

テンプレートのデプロイを開始したら、カスタム テンプレートを作成し、デプロイの値を設定できます。

![テンプレートの作成](./media/resource-group-portal/show-custom-template.png)

あるいは、[Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)から既存のテンプレートを選択できます。これらのテンプレートはコミュニティによって提供されています。さまざまな共通シナリオが取り上げられています。あなたがデプロイしようとしているテンプレートと似たようなテンプレートを追加している人がいるかもしれません。自分のシナリオに一致するテンプレートを検索できます。

![クイックスタート テンプレートの選択](./media/resource-group-portal/select-quickstart-template.png)

テンプレートを選択すると、エディターに読み込まれます。

## サブスクリプションとコストを表示する

サブスクリプションと、すべてのリソースの合計コストに関する情報を表示できます。**[サブスクリプション]** を選択し、表示するサブスクリプションを選択します。選択できるサブスクリプションが 1 つのみの場合もあります。

![subscription](./media/resource-group-portal/select-subscription.png)

サブスクリプション ブレードには、バーン レートが表示されます。

![バーン レート](./media/resource-group-portal/burn-rate.png)

また、リソースの種類別のコストの明細が表示されます。

![リソース コスト](./media/resource-group-portal/cost-by-resource.png)

## Azure ダッシュボードのアクセス制御

ポータルのほとんどのタイルに表示される情報へのアクセスは、Azure の[ロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)によって管理されています。ダッシュボードをエコシステムにシームレスに統合するために、発行されるダッシュボードはすべて、Azure リソースとして実装されます。アクセス制御の見地からは、ダッシュボードは仮想マシンやストレージ アカウントと変わりありません。

たとえば次のようになります。Azure サブスクリプションがあり、チームのさまざまなメンバーにサブスクリプションの**所有者**、**共同作業者**、または**閲覧者**ロールが割り当てられているとします。所有者または共同作成者であるユーザーは、サブスクリプション内でダッシュボードを一覧表示、表示、作成、変更、および削除できます。閲覧者であるユーザーは、ダッシュボードの一覧表示と表示はできますが、変更と削除はできません。閲覧者アクセス許可を持つユーザーは、発行されたダッシュボードにローカルで編集を加えることはできますが (たとえば、問題のトラブルシューティングを行う場合)、その変更をサーバーに戻すことはできません。このようなユーザーは、個人用にダッシュボードのプライベート コピーを作成することが可能です。

ダッシュボードの個々のタイルは、表示しているデータの基となるリソースに基づいて、独自のアクセス制御の要件を適用することに注意してください。つまり、個々のタイルのデータを保護しつつ、より広く共有できるようにダッシュボードを設計することができます。

## 次のステップ

- 監査ログの表示については、「[Resource Manager の監査操作](../resource-group-audit.md)」を参照してください。
- デプロイ エラーのトラブルシューティングの詳細については、「[Azure ポータルでのリソース グループのデプロイのトラブルシューティング](../resource-manager-troubleshoot-deployments-portal.md)」を参照してください。

<!---HONumber=AcomDC_0518_2016-->