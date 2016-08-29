<properties
	pageTitle="DocumentDB のグローバル データベース レプリケーション | Microsoft Azure"
	description="Azure ポータルを使用して DocumentDB アカウントのグローバル レプリケーションを管理する方法について説明します。"
	services="documentdb"
    keywords="グローバル データベース, レプリケーション"
	documentationCenter=""
	authors="mimig1"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/15/2016"
	ms.author="mimig"/>

# Azure ポータルを使用して DocumentDB グローバル データベース レプリケーションを実行する方法

Azure DocumentDB のグローバルなデータの可用性を確保するために、Azure ポータルを使用して、複数のリージョンのデータをレプリケートする方法について説明します。

DocumentDB におけるグローバル データベース レプリケーションの動作の詳細については、「[DocumentDB を使用したデータのグローバル分散](documentdb-distribute-data-globally.md)」を参照してください。グローバル データベース レプリケーションをプログラムで実行する方法については、「[複数リージョンの DocumentDB アカウント使用した開発](documentdb-developing-with-multiple-regions.md)」を参照してください。

> [AZURE.NOTE] DocumentDB データベースのグローバル配布は、広く利用可能な機能であり、新しく作成した DocumentDB アカウントで自動的に有効になります。弊社は既存のすべてのアカウントでグローバル分散が有効になるように取り組んでいます。ただし当面は、ご使用のアカウントでグローバル分散の有効化を希望される場合は、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)いただければすぐに有効にいたします。

## <a id="addregion"></a>グローバル データベース リージョンを追加する

DocumentDB は、ほとんどの [Azure リージョン][azureregions]で利用できます。データベース アカウントの既定の一貫性レベルを選択すると、選択した既定の一貫性レベルとグローバル配信の必要性に応じて、1 つまたは複数のリージョンを関連付けることができます。

1. [Azure ポータル](https://portal.azure.com/)で、ジャンプバーの **[DocumentDB アカウント]** をクリックします。
2. **[DocumentDB アカウント]** ブレードで、変更するデータベース アカウントを選択します。
3. アカウントのブレードで、メニューから **[リージョンの追加/削除]** をクリックします。![DocumentDB アカウントでのリージョンの追加 > [リージョンの追加/削除]][1]

5. **[リージョンの追加/削除]** ブレードで、追加または削除するリージョンを選択した後、**[OK]** をクリックします。リージョンを追加するには費用が必要になります。詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/documentdb/)や「[DocumentDB を使用したデータのグローバル分散](documentdb-distribute-data-globally.md)」の記事を参照してください。

    ![地図でリージョンをクリックして、リージョンを追加又は削除する][2]

### グローバル データベース リージョンの選択

複数のリージョンを構成する場合、「[ビジネス継続性と障害復旧 (BCDR): Azure のペアになっているリージョン][bcdr]」に記載されているリージョン ペアに基づいてリージョンを選択することをお勧めします。

具体的には、複数のリージョンを構成する場合、ペアになっているリージョンの各列から同じ数のリージョンを選択してください (+/-1 が奇数/偶数に対応します)。たとえば、米国の 4 か所のリージョンにデプロイする場合は、左側の列から 2 か所、右側の列から 2 か所の米国リージョンを選択します。この場合、適切なリージョン セットは米国西部、米国東部、米国中北部、および米国中南部となります。

障害復旧の際に 2 か所のリージョンしか構成できない場合、この指示に従うことが重要です。リージョンが 3 か所以上の場合もこの指示に従うことをお勧めしますが、選択したリージョンの一部がこのペア指定に基づいている場合には、この指示に従わなくても問題ありません。

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **DocumentDB Account** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][3]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][4]
-->

## <a id="next"></a>次のステップ

「[DocumentDB の一貫性レベル](documentdb-consistency-levels.md)」を読んで、グローバルにレプリケートされたアカウントの整合性を管理する方法について確認します。

DocumentDB におけるグローバル データベース レプリケーションの動作の詳細については、「[DocumentDB を使用したデータのグローバル分散](documentdb-distribute-data-globally.md)」を参照してください。複数のリージョンでのデータのレプリケーションをプログラムで実行する方法については、「[複数リージョンの DocumentDB アカウント使用した開発](documentdb-developing-with-multiple-regions.md)」を参照してください。

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-account-blade.png
[2]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[4]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/documentdb/

<!---HONumber=AcomDC_0817_2016-->