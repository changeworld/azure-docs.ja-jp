<properties title="Publishing Azure ML Web Services to the Azure Marketplace" pageTitle="Azure Marketplace への Azure ML Web サービスの発行 | Azure" description="Azure Marketplace への Azure ML Web サービスの発行" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/03/2014" ms.author="garye" />

# Azure Marketplace への Azure ML Web サービスの発行 

このドキュメントの内容:

- [はじめに]
- [発行プロセスの概要]
- [Azure Marketplace への発行ガイドライン]
- [Machine Learning 固有のオプション] 

<!--Anchors-->
[はじめに]: #introduction
[発行プロセスの概要]: #overview-of-the-publishing-process
[Azure Marketplace への発行ガイドライン]: #guidelines-for-publishing-to-azure-marketplace
[Machine Learning 固有のオプション]: #machine-learning-specific-options 

## はじめに

Azure Marketplace は、外部の顧客が使用する有料または無料のサービスとして Azure Machine Learning Web サービスを発行する機能を提供します。このドキュメントでは、プロセスの概要と使用開始のためのガイドラインへのリンクを示します。このプロセスを使用することで、他の開発者が Web サービスをアプリケーション内で使用できるようになります。

## 発行プロセスの概要 

Azure ML Web サービスを Azure Marketplace に発行するための手順を次に示します。

1.	Azure ML RRS (要求応答サービス) Web サービスを作成し、発行します。
2.	Azure の管理ポータルから、サービスを運用環境にデプロイします。
3.	発行済みの Web サービスの URL を使用して、Azure Marketplace に発行します。
4.	発行プロセスの概要は次のとおりです。http://msdn.microsoft.com/ja-jp/library/azure/hh580725.aspx 
5.	送信すると、サービスの内容が確認され、顧客が購入するにはこのサービスが承認される必要があります。発行プロセスには数営業日かかります。現在、可能な限り短縮できるように取り組んでいます。今後、最新情報を随時お知らせいたします。

## Azure Marketplace への発行ガイドライン

1.	発行者として登録する必要があります。詳細については、次のリンクを参照してください。<http://msdn.microsoft.com/ja-jp/library/azure/hh563872.aspx>
2.	料金プランなど、サービスについての情報を提供する必要があります。無料サービスまたは有料サービスのいずれを提供するかを決定します。詳細については、次のリンクを参照してください。<http://msdn.microsoft.com/ja-jp/library/azure/hh563873.aspx> 
3.	有料にするには、銀行や税などの、支払いの情報を提供する必要があります。詳細については、次のリンクを参照してください。<http://msdn.microsoft.com/ja-jp/library/azure/hh563873.aspx>

## Machine Learning 固有のオプション


1.	新しいサービスを作成するには、**[データ サービス]** を選択し、**[新しいデータ サービスの作成]** をクリックします。 
 
	![Azure Marketplace][image1]

	<br />

2. **[データ サービス]** タブをクリックし、データ ソースとして **[Web サービス]** をクリックします。

	![Azure Marketplace][image2]

3.	Azure 管理ポータルから、Web サービスの URL と API キーを取得します。
	1.	別のブラウザー ウィンドウまたはタブで、Azure 管理ポータル ([https://manage.windowsazure.com](https://manage.windowsazure.com)) にログインします 
	2.	左側のメニューで **[Machine Learning]** を選択します
	3.	**[Web サービス]** をクリックし、発行する Web サービスをクリックします
	4.	**API キー**を一時的な場所 (たとえば、メモ帳など) にコピーします
	5.	要求/応答サービスの種類の **[API ヘルプ ページ]** をクリックします
	6.	**OData エンドポイント アドレス**を一時的な場所にコピーします

	<br />

3.	[マーケットプレース データ サービスのセットアップ] ダイアログで、OData エンドポイント アドレスを **[サービス URL]** に貼り付けます。

	<br />

4. 認証するには、**[認証スキーム]** として **[ヘッダー]** を選択します。

	- **[ヘッダー名]** に「Authorization」と入力します
	- **[ヘッダー値]** に、「Bearer」(引用符なしで)、スペースの順に入力してから、API キーを貼り付けます
	- **[このサービスは OData]** チェック ボックスをオンにします
	- **[接続テスト]** をクリックして接続をテストします

	<br />

5.	[カテゴリ] で、次のことを確認します。
	- **[Machine Learning]** チェック ボックスがオンになっていること



[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
