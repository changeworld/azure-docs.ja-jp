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
	ms.date="08/15/2016"
	ms.author="anhoh"/>

# DocumentDB アカウントの管理方法

Azure ポータルでの、グローバルな整合性の設定、キーの操作、DocumentDB アカウントの削除の方法について説明します。

## <a id="consistency"></a>DocumentDB 整合性の設定を管理する

アプリケーションのセマンティクスに応じて、適切な一貫性レベルを選択します。「[一貫性レベルを使用した DocumentDB の可用性とパフォーマンスの最大化][consistency]」を参考にして、DocumentDB で利用できる一貫性レベルについて把握してください。DocumentDB の一貫性、可用性、パフォーマンスは、お使いのデータベース アカウントで利用できるすべての一貫性レベルで保証されます。データベース アカウントに高い一貫性レベルを設定するには、データを 1 つの Azure リージョンに限定し、グローバルで使用できないようにする必要があります。一方で、低い一貫性レベル (制限のある陳腐化、セッション、または最終的) に設定した場合、任意の数の Azure リージョンをデータベース アカウントに関連付けることができます。次に、データベース アカウントの既定の一貫性レベルを選択するための簡単な手順を説明します。

### DocumentDB アカウントの既定の整合性を指定するには

1. [Azure ポータル](https://portal.azure.com/)で、DocumentDB アカウントにアクセスします。
2. アカウントのブレードで、**[既定の整合性]** をクリックします。
3. **[既定の整合性]** ブレードで、新しい一貫性レベルを選択し、**[OK]** をクリックします。![既定の整合性セッション][5]

## <a id="keys"></a>アクセス キーを表示、コピー、および再生成する
DocumentDB アカウントを作成すると、2 つのマスター アクセス キーが生成され、DocumentDB アカウントにアクセスする際の認証に使用できます。2 つのアクセス キーが提供されるので、DocumentDB アカウントを中断することなくキーを再生成できます。

[Microsoft Azure ポータル](https://portal.azure.com/)で、**[DocumentDB アカウント]** ブレードの **[キー]** ブレードにアクセスし、DocumentDB アカウントにアクセスするときに使用するアクセス キーの表示、コピー、再生成を行います。

![Azure ポータルのスクリーン ショット、[キー] ブレード](./media/documentdb-manage-account/keys.png)

> [AZURE.NOTE] **[キー]** ブレードには、[データ移行ツール](documentdb-import-data.md)からアカウントに接続する際に使用できるプライマリおよびセカンダリ接続文字列も含まれています。

読み取り専用キーもこのブレードで入手できます。読み取りとクエリは読み取り専用操作ですが、作成、削除、置換はそうではありません。

### Azure ポータルでアクセス キーを表示およびコピーする

**[キー]** ブレードで、コピー対象のキーの右側にある **[コピー]** ボタンをクリックします。

![Azure ポータルの [キー] ブレードでアクセス キーを表示およびコピーする](./media/documentdb-manage-account/copykeys.png)

### アクセス キーを再生成する

接続のセキュリティを高めるために、DocumentDB アカウントのアクセス キーは定期的に変更する必要があります。片方のアクセス キーで DocumentDB アカウントに接続したまま、もう片方のアクセス キーを再生成できるように、アクセス キーは 2 つ割り当てられます。

> [AZURE.WARNING] アクセス キーを再生成すると、現在のキーに依存するすべてのアプリケーションが影響を受けます。アクセス キーを使用して DocumentDB アカウントにアクセスするすべてのクライアントを更新し、新しいキーが使用されるようにする必要があります。

DocumentDB アカウントを使用するアプリケーションまたはクラウド サービスがある場合、キーを再生成すると、キーを切り替えない限り接続が失われます。次の手順は、キーの切り替えに含まれるプロセスの概要を示します。

1. DocumentDB アカウントのセカンダリ アクセス キーを参照するように、アプリケーション コードのアクセス キーを更新します。
2. DocumentDB アカウントのプライマリ アクセス キーを再生成します。[Azure ポータル](https://portal.azure.com/)で、DocumentDB アカウントにアクセスします。
3. **[DocumentDB アカウント]** ブレードで、**[キー]** をクリックします。
4. **[キー]** ブレードで、**[プライマリの再生成]** コマンドをクリックしてから **[OK]** をクリックして、新しいキーを生成することを確認します。![アクセス キーを再生成する](./media/documentdb-manage-account/regenerate-keys.png)

5. (再生成してから約 5 分後に) 新しいキーが使用できることを確認したら、新しいプライマリ アクセス キーを参照するようにアプリケーション コードのアクセス キーを更新します。
6. セカンダリ アクセス キーを再生成します。

![アクセス キーを再生成する](./media/documentdb-manage-account/regenerate-secondary-key.png)


> [AZURE.NOTE] 新しく生成されたキーを使用して DocumentDB アカウントにアクセスできるようになるまで、数分かかることがあります。

## <a id="delete"></a>DocumentDB アカウントを削除する
使用しなくなった DocumentDB アカウントを Azure ポータルで削除するには、**[DocumentDB アカウント]** ブレードの **[アカウントの削除]** コマンドを使用します。

![Azure ポータルで DocumentDB アカウントを削除する方法](./media/documentdb-manage-account/deleteaccount.png)


1. [Azure ポータル](https://portal.azure.com/)で、削除する DocumentDB アカウントにアクセスします。
2. **[DocumentDB アカウント]** ブレードで、**[More (さらに表示)]**、**[アカウントの削除]** の順にクリックします。または、データベースの名前を右クリックしてから、**[アカウントの削除]** をクリックします。
3. 確認ブレードで、DocumentDB アカウント名を入力して、そのアカウントを削除することを確認します。
4. **[削除]** ボタンをクリックします。

![Azure ポータルで DocumentDB アカウントを削除する方法](./media/documentdb-manage-account/delete-account-confirm.png)

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
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/documentdb/

<!---HONumber=AcomDC_0817_2016-->