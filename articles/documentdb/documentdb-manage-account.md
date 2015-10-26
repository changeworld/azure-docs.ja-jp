<properties 
	pageTitle="Azure プレビュー ポータルを使用して DocumentDB アカウントを管理する | Microsoft Azure" 
	description="Azure プレビュー ポータルを使用して DocumentDB アカウントを管理する方法Azure プレビュー ポータルを使用してアカウントを表示、コピー、削除、およびアカウントにアクセスする方法について説明したガイドを紹介します。" 
	keywords="Azure preview portal, documentdb, azure, Microsoft azure"
	services="documentdb" 
	documentationCenter="" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/28/2015" 
	ms.author="stbaro"/>

# Azure プレビュー ポータルで DocumentDB アカウントを管理する方法

Azure プレビュー ポータルで、キーおよび一貫性の設定を操作する方法と、アカウントを削除する方法について説明します。

## <a id="keys"></a>アクセス キーを表示、コピー、および再生成する
DocumentDB アカウントを作成すると、2 つのマスター アクセス キーが生成され、DocumentDB アカウントにアクセスする際の認証に使用できます。2 つのアクセス キーが提供されるので、DocumentDB アカウントを中断することなくキーを再生成できます。

[Microsoft Azure プレビュー ポータル](https://portal.azure.com/)で、**[DocumentDB アカウント]** ブレードの **[キー]** パーツにアクセスし、DocumentDB アカウントにアクセスするときに使用するアクセス キーの表示、コピー、再生成を行います。

![Azure プレビュー ポータルのスクリーン ショット、[キー] ブレード](media/documentdb-manage-account/keys.png)

### Azure プレビュー ポータルでアクセス キーを表示およびコピーする

1.      [Azure プレビュー ポータル](https://portal.azure.com/)で、DocumentDB アカウントにアクセスします。 

2.      **[概要]** レンズで、**[キー]** をクリックします。

3.      **[キー]** ブレードで、コピー対象のキーの右側にある **[コピー]** ボタンをクリックします。

  ![Azure プレビュー ポータルの [キー] ブレードでアクセス キーを表示およびコピーする](./media/documentdb-manage-account/image004.jpg)

### アクセス キーを再生成する

接続のセキュリティを高めるために、DocumentDB アカウントのアクセス キーは定期的に変更する必要があります。片方のアクセス キーで DocumentDB アカウントに接続したまま、もう片方のアクセス キーを再生成できるように、アクセス キーは 2 つ割り当てられます。

> [AZURE.WARNING]アクセス キーを再生成すると、現在のキーに依存するすべてのアプリケーションが影響を受けます。アクセス キーを使用して DocumentDB アカウントにアクセスするすべてのクライアントを更新し、新しいキーが使用されるようにする必要があります。

DocumentDB アカウントを使用するアプリケーションまたはクラウド サービスがある場合、キーを再生成すると、キーを切り替えない限り接続が失われます。次の手順は、キーの切り替えに含まれるプロセスの概要を示します。

1.      DocumentDB アカウントのセカンダリ アクセス キーを参照するように、アプリケーション コードのアクセス キーを更新します。

2.      DocumentDB アカウントのプライマリ アクセス キーを再生成します。[Azure プレビュー ポータル](https://portal.azure.com/)で、DocumentDB アカウントにアクセスします。

3.      [概要] レンズで、**[キー]** をクリックします。

4.      **[キー]** ブレードで、**[プライマリの再生成]** コマンドをクリックしてから **[OK]** をクリックして、新しいキーを再生成することを確認します。

5.      (再生成してから約 5 分後に) 新しいキーが使用できることを確認したら、新しいプライマリ アクセス キーを参照するようにアプリケーション コードのアクセス キーを更新します。

6.      セカンダリ アクセス キーを再生成します。

*新しく生成されたキーを使用して DocumentDB アカウントにアクセスできるようになるまで、数分かかることがあります。*

## <a id="consistency"></a>DocumentDB 整合性の設定を管理する
DocumentDB では、確かな見通しに基づいて整合性、可用性、待機時間のトレードオフを開発者が検討できるようにするため、ユーザーが構成可能な適切に定義された 4 つのデータ整合性レベルがサポートされています。

- **Strong** 整合性では、最後に書き込まれた値が常に読み取り操作で戻されることを保証します。

- **Bounded Staleness** 整合性では、読み取る値が古くなりすぎていないことを保証します。具体的には、読み取る値が最後に書き込まれたバージョンよりも K バージョン以上古くないことを保証します。

- **Session** 整合性は、モノトニックな読み取り (古いデータの次に新しいデータ、再度古いデータという読み取りはしない)、モノトニックな書き込み (順序どおりに書き込まれる)、およびどのクライアントの観点からも最新の書き込みを読み取ることを保証します。

- **Eventual** 整合性は、読み取り操作が常に有効な書き込みの一部を読み取り、最終的にはすべてを読み取ることを保証します。

*既定では、DocumentDB アカウントは Session レベルの整合性でプロビジョニングされます。DocumentDB の整合性設定の詳細については、[整合性レベル](http://go.microsoft.com/fwlink/p/?LinkId=402365)に関するセクションを参照してください。*

### DocumentDB アカウントの既定の整合性を指定するには

1.      [Azure プレビュー ポータル](https://portal.azure.com/)で、DocumentDB アカウントにアクセスします。 

2.      **[構成]** レンズで、**[既定の整合性]** をクリックします。

3.      **[既定の整合性]** ブレードで、DocumentDB アカウントに設定する既定の整合性レベルを選択します。

![既定の整合性セッション](./media/documentdb-manage-account/image005.png)

![制限付きの既定の整合性](./media/documentdb-manage-account/image006.png)

4.      **[保存]** をクリックします。

5.      Azure プレビュー ポータルの通知ハブで、操作の進行状況を監視できます。

*DocumentDB アカウントの既定の整合性に加えた変更が有効になるまで、数分かかることがあります。*

## <a id="delete"></a>Azure プレビュー ポータルで DocumentDB アカウントを削除する方法
使用しなくなった DocumentDB アカウントを Azure プレビュー ポータルで削除するには、**[DocumentDB アカウント]** ブレードの **[削除]** コマンドを使用します。

![Azure プレビュー ポータルで DocumentDB アカウントを削除する方法](./media/documentdb-manage-account/image009.png)

1.      [Azure プレビュー ポータル](https://portal.azure.com/)で、削除する DocumentDB アカウントにアクセスします。 

2.      **[DocumentDB アカウント]** ブレードで、**[削除]** コマンドをクリックします。

3.      確認ブレードで、DocumentDB アカウント名を入力して、そのアカウントを削除することを確認します。

4.      確認ブレードの **[削除]** ボタンをクリックします。

## <a id="next"></a>次のステップ

[DocumentDB アカウントの使用方法](http://go.microsoft.com/fwlink/p/?LinkId=402364)について取り上げます。

DocumentDB の詳細については、[azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409) で Azure DocumentDB に関するドキュメントを参照してください。

 
 

<!---HONumber=Oct15_HO3-->