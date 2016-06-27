<properties
	pageTitle="Azure ポータルを使用して DocumentDB アカウントを管理する | Microsoft Azure"
	description="Azure ポータルを使用して DocumentDB アカウントを管理する方法について説明します。Azure ポータルを使用してアカウントを表示、コピー、削除、およびアカウントにアクセスする方法について説明したガイドを紹介します。"
	keywords="Azure ポータル, documentdb, azure, Microsoft azure"
	services="documentdb"
	documentationCenter=""
	authors="AndrewHoh"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="anhoh"/>

# DocumentDB アカウントの管理方法

グローバルでのデータの可用性を確保するために、グローバルな一貫性を設定し、複数のリージョンを管理する方法について説明します。また、キーの使用方法と、Azure ポータルでアカウントを削除する方法についても説明します。

## <a id="consistency"></a>DocumentDB 整合性の設定を管理する

アプリケーションのセマンティクスに応じて、適切な一貫性レベルを選択します。「[一貫性レベルを使用した DocumentDB の可用性とパフォーマンスの最大化][consistency]」を参考にして、DocumentDB で利用できる一貫性レベルについて把握してください。DocumentDB の一貫性、可用性、パフォーマンスは、お使いのデータベース アカウントで利用できるすべての一貫性レベルで保証されます。データベース アカウントに高い一貫性レベルを設定するには、データを 1 つの Azure リージョンに限定し、グローバルで使用できないようにする必要があります。一方で、低い一貫性レベル (制限のある陳腐化、セッション、または最終的) に設定した場合、任意の数の Azure リージョンをデータベース アカウントに関連付けることができます。次に、データベース アカウントの既定の一貫性レベルを選択するための簡単な手順を説明します。

### DocumentDB アカウントの既定の整合性を指定するには

1. [Azure ポータル](https://portal.azure.com/)で、DocumentDB アカウントにアクセスします。
2. アカウント ブレードで、**[設定]** ブレードをまだ開いていない場合は、**[すべての設定]** をクリックします。![既定の整合性セッション][5]

3. **[すべての設定]** ブレードで、**[機能]** の **[既定の整合性]** エントリをクリックします。![既定の整合性セッション][6]

4. **[既定の一貫性]** ブレードで、新しい一貫性レベルを選択し、**[保存]** をクリックします。
5. Azure ポータルの通知ハブで、操作の進行状況を監視できます。

> [AZURE.NOTE] DocumentDB アカウントの既定の整合性に加えた変更が有効になるまで、数分かかることがあります。

## <a id="addregion"></a>領域を追加する

DocumentDB は、ほとんどの [Azure リージョン][azureregions]で利用できます。データベース アカウントの既定の一貫性レベルを選択すると、選択した既定の一貫性レベルとグローバル配信の必要性に応じて、1 つまたは複数のリージョンを関連付けることができます。

> [AZURE.NOTE] 現時点では、2016 年 6 月 13 日以降に作成された新しい DocumentDB アカウントに新しいリージョンを追加できます。複数リージョン アカウントを作成するには、Marketplace で [Azure DocumentDB - Multi-region database Account (Azure DocumentDB - 複数リージョン データベース アカウント)] を選択します。6 月 13 日よりも前に作成されたアカウントについては、今後間もなくグローバルな可用性に対応する予定です。

1. [Azure ポータル](https://portal.azure.com/)で、ジャンプバーの **[DocumentDB アカウント]** をクリックします。
2. **[DocumentDB アカウント]** ブレードで、変更するデータベース アカウントを選択します。
3. アカウント ブレードで、**[すべての設定]** ブレードをまだ開いていない場合は、**[すべての設定]** をクリックします。
4. **[すべての設定]** ブレードの **[リージョンの追加/削除]** をクリックします。![Add regions under DocumentDB Account > Settings > Add/Remove Regions][1]
5. **[リージョンの追加/削除]** ブレードで、追加または削除するリージョンを選択した後、**[OK]** をクリックします。リージョンを追加するには費用がかかります。詳しくは、価格に関するページをご覧ください。

    ![Click on the regions in the map to add or remove them][2]

### リージョンを選択する

複数のリージョンを構成する場合、「[ビジネス継続性と障害復旧 (BCDR): Azure のペアになっているリージョン][bcdr]」に記載されているリージョン ペアに基づいてリージョンを選択することをお勧めします。

具体的には、複数のリージョンを構成する場合、ペアになっているリージョンの各列から同じ数のリージョンを選択してください (+/-1 が奇数/偶数に対応します)。たとえば、米国の 4 か所のリージョンにデプロイする場合は、左側の列から 2 か所、右側の列から 2 か所の米国リージョンを選択します。この場合、適切なリージョン セットは米国西部、米国東部、米国中北部、および米国中南部となります。

障害復旧シナリオで 2 か所のリージョンしか構成できない場合、この指示に従うことが重要です。リージョンが 3 か所以上の場合もこの指示に従うことをお勧めしますが、選択したリージョンの一部がこのペア指定に基づいている場合には、この指示に従わなくても問題ありません。

## <a id="selectwriteregion"></a>書き込みリージョンを選択する

DocumentDB データベース アカウントに関連付けられているすべてのリージョンで読み取り (単一項目の読み取りと複数項目の改ページ読み取り) およびクエリに対応できますが、書き込み要求 (挿入、アップサート、置換、削除) をアクティブに受け取るのは 1 つのリージョンのみです。アクティブな書き込みリージョンを設定するには、次の操作を行います。


1. **[DocumentDB アカウント]** ブレードで、変更するデータベース アカウントを選択します。
2. アカウント ブレードで、**[すべての設定]** ブレードをまだ開いていない場合は、**[すべての設定]** をクリックします。
3. **[すべての設定]** ブレードの **[Write Region Priority (書き込みリージョンの優先度)]** をクリックします。![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][3]
4. リージョンをクリックしてドラッグし、リージョンの一覧を並べ替えます。リージョンの一覧の先頭にあるリージョンが、アクティブな書き込みリージョンになります。![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][4]

## <a id="keys"></a>アクセス キーを表示、コピー、および再生成する
DocumentDB アカウントを作成すると、2 つのマスター アクセス キーが生成され、DocumentDB アカウントにアクセスする際の認証に使用できます。2 つのアクセス キーが提供されるので、DocumentDB アカウントを中断することなくキーを再生成できます。

[Microsoft Azure ポータル](https://portal.azure.com/)で、**[DocumentDB アカウント]** ブレードの **[要点]** バーから **[キー]** ブレードにアクセスして、DocumentDB アカウントにアクセスするときに使用するアクセス キーの表示、コピー、再生成を行います。

![Azure ポータルのスクリーン ショット、[キー] ブレード](./media/documentdb-manage-account/keys.png)

別の方法として、**[すべての設定]** ブレードから **[キー]** エントリにアクセスすることもできます。

![[すべての設定] ブレード、[キー] ブレード](./media/documentdb-manage-account/allsettingskeys.png)

> [AZURE.NOTE] **[キー]** ブレードには、[データ移行ツール](documentdb-import-data.md)からアカウントに接続する際に使用できるプライマリおよびセカンダリ接続文字列も含まれています。

また、ユーザーに DocumentDB への読み取り専用アクセスを提供する読み取り専用キーも含まれています。読み取りとクエリは読み取り専用操作ですが、作成、削除、置換はそうではありません。

### Azure ポータルでアクセス キーを表示およびコピーする

1. [Azure ポータル](https://portal.azure.com/)で、DocumentDB アカウントにアクセスします。 
2. **[DocumentDB アカウント]** ブレードの **[要点]** バーで、**[キー]** をクリックします。
3. **[キー]** ブレードで、コピー対象のキーの右側にある **[コピー]** ボタンをクリックします。![Azure ポータルの [キー] ブレードでアクセス キーを表示およびコピーする](./media/documentdb-manage-account/copykeys.png)

### アクセス キーを再生成する

接続のセキュリティを高めるために、DocumentDB アカウントのアクセス キーは定期的に変更する必要があります。片方のアクセス キーで DocumentDB アカウントに接続したまま、もう片方のアクセス キーを再生成できるように、アクセス キーは 2 つ割り当てられます。

> [AZURE.WARNING] アクセス キーを再生成すると、現在のキーに依存するすべてのアプリケーションが影響を受けます。アクセス キーを使用して DocumentDB アカウントにアクセスするすべてのクライアントを更新し、新しいキーが使用されるようにする必要があります。

DocumentDB アカウントを使用するアプリケーションまたはクラウド サービスがある場合、キーを再生成すると、キーを切り替えない限り接続が失われます。次の手順は、キーの切り替えに含まれるプロセスの概要を示します。

1. DocumentDB アカウントのセカンダリ アクセス キーを参照するように、アプリケーション コードのアクセス キーを更新します。
2. DocumentDB アカウントのプライマリ アクセス キーを再生成します。[Azure ポータル](https://portal.azure.com/)で、DocumentDB アカウントにアクセスします。
3. **[DocumentDB アカウント]** ブレードの **[要点]** バーで、**[キー]** をクリックします。
4. **[キー]** ブレードで、**[プライマリの再生成]** コマンドをクリックしてから **[OK]** をクリックして、新しいキーを再生成することを確認します。
5. (再生成してから約 5 分後に) 新しいキーが使用できることを確認したら、新しいプライマリ アクセス キーを参照するようにアプリケーション コードのアクセス キーを更新します。
6. セカンダリ アクセス キーを再生成します。

> [AZURE.NOTE] 新しく生成されたキーを使用して DocumentDB アカウントにアクセスできるようになるまで、数分かかることがあります。

## <a id="delete"></a>DocumentDB アカウントを削除する
使用しなくなった DocumentDB アカウントを Azure ポータルで削除するには、**[DocumentDB アカウント]** ブレードの **[削除]** コマンドを使用します。

![Azure ポータルで DocumentDB アカウントを削除する方法](./media/documentdb-manage-account/deleteaccountconfirmation.png)


1. [Azure ポータル](https://portal.azure.com/)で、削除する DocumentDB アカウントにアクセスします。
2. **[DocumentDB アカウント]** ブレードの **[アカウントの削除]** をクリックします。
3. 確認ブレードで、DocumentDB アカウント名を入力して、そのアカウントを削除することを確認します。
4. 確認ブレードで **[削除]** ボタンをクリックします。

## <a id="next"></a>次のステップ

[DocumentDB アカウントの使用方法](http://go.microsoft.com/fwlink/p/?LinkId=402364)について取り上げます。

DocumentDB の詳細については、[azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409) で Azure DocumentDB に関するドキュメントを参照してください。


<!--Image references-->
[1]: ./media/documentdb-manage-account/documentdb_add_region-1.png
[2]: ./media/documentdb-manage-account/documentdb_add_region-2.png
[3]: ./media/documentdb-manage-account/documentdb_change_write_region-1.png
[4]: ./media/documentdb-manage-account/documentdb_change_write_region-2.png
[5]: ./media/documentdb-manage-account/documentdb_change_consistency-1.png
[6]: ./media/documentdb-manage-account/chooseandsaveconsistency.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/ja-JP/regions/#services
[offers]: https://azure.microsoft.com/ja-JP/pricing/details/documentdb/

<!---HONumber=AcomDC_0615_2016-->