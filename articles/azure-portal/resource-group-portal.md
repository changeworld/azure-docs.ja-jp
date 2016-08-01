<properties 
	pageTitle="Azure ポータルを使用した Azure リソースの管理 | Microsoft Azure" 
	description="Azure ポータルと Azure Resource Manager を使用してリソースを管理します。ダッシュボードとタイルを使用してリソースを監視する方法について説明します。" 
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
	ms.date="06/10/2016" 
	ms.author="tomfitz"/>


# ポータルを使用した Azure リソースの管理

> [AZURE.SELECTOR]
- [ポータル](azure-portal/resource-group-portal.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [.NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)
- [Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
- [ノード](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
- [Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
- [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

このトピックでは、[Azure ポータル](https://portal.azure.com)と [Azure Resource Manager](../resource-group-overview.md) を使用して Azure リソースを管理する方法について説明します。ポータルを使用したリソースのデプロイについては、「[Deploy resources with Resource Manager templates and Azure portal (Resource Manager テンプレートと Azure ポータルを使用したリソースのデプロイ)](../resource-group-template-deploy-portal.md)」を参照してください。

現時点では、すべてのサービスでポータルまたはリソース マネージャーがサポートされているわけではありません。それらがサポートされていないサービスの場合、[クラシック ポータル](https://manage.windowsazure.com)を使用する必要があります。各サービスの状態については、[Azure ポータルで利用できるサービスの表](https://azure.microsoft.com/features/azure-portal/availability/)を参照してください。

<a id="access-control-for-azure-dashboards" />
## リソースを監視するためのダッシュボードのカスタマイズ

ポータルには、リソースの監視と管理に使用できるダッシュボードが用意されています。このダッシュボードは自由にカスタマイズでき、また複数のダッシュボードを作成することで、サブスクリプションのさまざまな側面を容易に表示することができます。

![dashboard](./media/resource-group-portal/dashboard.png)

> [AZURE.TIP] ダッシュボードの操作をマスターするには、「[Build Custom Dashboards in the Microsoft Azure Portal (Microsoft Azure ポータルでカスタム ダッシュボードを作成する)](https://channel9.msdn.com/Blogs/trevor-cloud/azure-portal-dashboards)」のビデオをご覧いただくのが最も近道です。

### Azure ダッシュボードの共有とアクセス制御
構成したダッシュボードは、発行して組織内の他のユーザーと共有することができます。ポータルのタイルに表示される情報へのアクセスは、Azure の[ロール ベースのアクセス制御](../active-directory/role-based-access-control-configure.md)によって管理されます。発行したすべてのダッシュボードは Azure リソースとして実装されます。アクセス制御の見地からは、ダッシュボードは仮想マシンやストレージ アカウントと変わりありません。

たとえば次のようになります。Azure サブスクリプションがあり、チームのさまざまなメンバーに、サブスクリプションの**所有者**、**共同作業者**、または**閲覧者**ロールが割り当てられているとします。所有者または共同作成者であるユーザーは、サブスクリプション内でダッシュボードを一覧表示、表示、作成、変更、および削除できます。閲覧者であるユーザーは、ダッシュボードの一覧表示と表示はできますが、変更と削除はできません。閲覧者アクセス許可を持つユーザーは、発行されたダッシュボードにローカルで編集を加えることはできますが (たとえば、問題のトラブルシューティングを行う場合)、その変更をサーバーに戻すことはできません。このようなユーザーは、個人用にダッシュボードのプライベート コピーを作成することが可能です。

ダッシュボードの個々のタイルには、表示されているリソースに基づいて、独自のアクセス制御の要件が適用されます。つまり、広く共有しながらも個々のタイルのデータを保護するようにダッシュボードを設計することができます。

## リソース グループの管理

1. 自分のサブスクリプションのすべてのリソース グループを表示するには、**[リソース グループ]** を選択します。

    ![リソース グループの参照](./media/resource-group-portal/browse-groups.png)

2. 管理対象となる特定のリソース グループを選択します。グループ内のすべてのリソースの一覧を含む、そのリソース グループに関する情報が示されたリソース グループのブレードが表示されます。

    ![リソース グループのサマリ](./media/resource-group-portal/group-summary.png)

    いずれかのリソースを選択すると、そのリソースについての詳細情報が表示されます。

3. リソースのブレード内で概要の下の **[セクションを追加]** を選択すると、さらにグラフや表を追加できます。

    ![add section](./media/resource-group-portal/add-section.png)

4. ブレードに含める情報を選択するためのタイル ギャラリーが表示されます。表示されるタイルの種類は、リソースの種類でフィルター処理されます。別のリソースを選択すると、利用可能なタイルが変化します。

    ![add section](./media/resource-group-portal/tile-gallery.png)

5. 必要なタイルを使用可能なスペースにドラッグします。

    ![drag tile](./media/resource-group-portal/drag-tile.png)

6. ポータルの上部にある **[完了]** を選択すると、新しいビューがブレードに追加されます。

    ![show tile](./media/resource-group-portal/show-lens.png)

7. リソース グループにすばやくアクセスするために、ブレードをダッシュボードにピン留めできます。

    ![リソース グループの固定](./media/resource-group-portal/pin-group.png)

    また、ブレードのセクションは、そのセクションの上にある省略記号 (...) を選択すると、ダッシュボードにピン留めできます。さらに、ブレードのセクションのサイズをカスタマイズしたり、完全に削除したりできます。次の図は、[CPU とメモリ] セクションをピン留め、カスタマイズ、または削除する方法を示しています。

    ![セクションの固定](./media/resource-group-portal/pin-cpu-section.png)

8. セクションをダッシュボードにピン留めすると、ダッシュボード上に概要が表示されます。

    ![view dashboard](./media/resource-group-portal/view-startboard.png)

さらに、それを選択すると、データの詳細情報が即時に表示されます。

## リソースへのタグ付け

リソース グループやリソースにタグを適用して、アセットを論理的に整理できます。ポータルを使用してタグを処理する方法の詳細については、「[タグを使用した Azure リソースの整理](../resource-group-using-tags.md)」をご覧ください。

## サブスクリプションとコストを表示する

サブスクリプションと、すべてのリソースの合計コストに関する情報を表示できます。**[サブスクリプション]** を選択し、表示するサブスクリプションを選択します。選択できるサブスクリプションが 1 つのみの場合もあります。

![サブスクリプション](./media/resource-group-portal/select-subscription.png)

サブスクリプション ブレードには、バーン レートが表示されます。

![バーン レート](./media/resource-group-portal/burn-rate.png)

また、リソースの種類別のコストの明細が表示されます。

![リソース コスト](./media/resource-group-portal/cost-by-resource.png)

## テンプレートをエクスポートする

リソース グループを設定したら、リソース グループのリソース マネージャー テンプレートを表示できます。テンプレートのエクスポートには、2 つの利点があります。

1. ソリューションの将来のデプロイを簡単に自動化できます。すべてのインフラストラクチャがテンプレートに定義されているためです。

2. ソリューションを表す JavaScript Object Notation (JSON) を見ることでテンプレートの構文に詳しくなります。

手順については、「[既存のリソースから Azure Resource Manager テンプレートをエクスポートする](../resource-manager-export-template.md)」を参照してください。

## リソース グループまたはリソースを削除する

リソース グループを削除すると、そこに含まれているすべてのリソースが削除されます。また、リソース グループ内にある個々のリソースを削除することもできます。他のリソース グループのリソースがリンクされている可能性があるため、リソース グループを削除する場合は注意を払う必要があります。リンクされたリソースは削除されませんが、期待どおりに機能しない可能性があります。

![グループの削除](./media/resource-group-portal/delete-group.png)


## 次のステップ

- 監査ログの表示については、「[Resource Manager の監査操作](../resource-group-audit.md)」を参照してください。
- デプロイ エラーのトラブルシューティングの詳細については、「[Azure ポータルでのリソース グループのデプロイのトラブルシューティング](../resource-manager-troubleshoot-deployments-portal.md)」を参照してください。
- ポータルを使用したリソースのデプロイについては、「[Deploy resources with Resource Manager templates and Azure portal (Resource Manager テンプレートと Azure ポータルを使用したリソースのデプロイ)](../resource-group-template-deploy-portal.md)」を参照してください。

<!---HONumber=AcomDC_0720_2016-->