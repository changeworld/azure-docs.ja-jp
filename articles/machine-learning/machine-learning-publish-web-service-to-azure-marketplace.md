<properties 
	pageTitle="Azure Marketplace に Machine Learning Web service を発行する | Microsoft Azure" 
	description="Azure Marketplace に Azure Machine Learning Web サービスを発行する方法" 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/12/2015" 
	ms.author="luisca"/>

# Azure Marketplace への Azure Machine Learning Web サービスの発行 

Azure Marketplace は、外部の顧客が使用する有料または無料のサービスとして Azure Machine Learning Web サービスを発行する機能を提供します。この記事では、プロセスの概要と使用開始のためのガイドラインへのリンクを示します。このプロセスを使用することで、他の開発者が Web サービスをアプリケーション内で使用できるようになります。


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 発行プロセスの概要 

Azure Machine Learning Web サービスを Azure Marketplace に発行するための手順を次に示します。

1. Machine Learning Request-Response サービス (RRS) を作成して発行する
2. サービスを運用環境にデプロイし、API キーと OData のエンドポイント情報を取得する
3. 発行済みの Web サービスの URL を使用して、[Azure Marketplace (データ マーケット)](https://publish.windowsazure.com/workspace/) に発行します。 
4. 送信すると、プランの内容が確認され、顧客が購入するにはこのサービスが承認される必要があります。発行プロセスには数営業日かかります。 

## チュートリアル
###手順 1: Machine Learning Request-Response サービス (RRS) を作成して発行する###
 まだ完了していない場合は、こちらの[チュートリアル](machine-learning-walkthrough-5-publish-web-service.md)をご覧ください。

###手順 2: サービスを運用環境にデプロイし、API キーと OData のエンドポイント情報を取得する###
1. [[Microsoft Azure 管理ポータル]](http://manage.windowsazure.com)で、左側のナビゲーション バーから **[MACHINE LEARNING]** オプションを選択し、ワークスペースを選択します。 

2. **[WEB サービス]** タブをクリックし、マーケットプレースに発行する Web サービスを選択します。

	![Azure Marketplace][workspace]

3. マーケットプレースで使用するエンドポイントを選択します。追加のエンドポイントを作成しない場合は、**[既定]** のエンドポイントを選択できます。

4. エンドポイントをクリックすると、**[API キー]** が表示されます。この情報は後の手順 3 で必要になりますので、コピーしておきます。

	![Azure Marketplace][apikey]

5. **[要求/応答]** メソッドをクリックします。この時点で、マーケットプレースへのバッチ実行サービスの公開はサポートされていません。[要求/応答] メソッドの API ヘルプ ページが表示されます。

6. **[OData エンドポイント アドレス]** をコピーします。この情報は後の手順 3 で必要になります。

	![Azure Marketplace][odata]




サービスを運用環境にデプロイします。



###手順 3: 発行済みの Web サービスの URL を使用して、Azure Marketplace (データ マーケット) に発行する###

1.  [Azure Marketplace (データ マーケット)](http://datamarket.azure.com/home) に移動します。 
2.  ページの上部にある **[発行]** リンクをクリックします。[[Microsoft Azure 発行ポータル]](https://publish.windowsazure.com) が表示されます。
3.  **[発行者]** セクションをクリックし、発行者として登録します。
4.	新しいプランを作成するには、**[Data Services]** を選択し、**[新しいデータ サービスの作成]** をクリックします。 
 
	![Azure Marketplace][image1]

	<br />


5.	**[プラン]** で、料金プランなどのサービスに関する情報を入力します。無料サービスまたは有料サービスのいずれを提供するかを決定します。有料にするには、銀行や税などの支払い情報を入力します。

6.	**[マーケティング]** で、プランのタイトルや説明などプランに関する情報を入力します。

7.	**[料金]** で、国固有のプランの価格を設定するか、システムによるサービスの "自動料金設定" を使用します。

8. **[データ サービス]** タブをクリックし、**[データ ソース]** として **[Web サービス]** をクリックします。

	![Azure Marketplace][image2]

9.	上記の手順 2 で説明したように、Microsoft Azure 管理ポータルから、Web サービスの URL と API キーを取得します。

10.	[マーケットプレース データ サービスのセットアップ] ダイアログで、OData エンドポイント アドレスを **[サービス URL]** テキスト ボックスに貼り付けます。

11. **認証**するには、**[認証スキーム]** として **[ヘッダー]** を選択します。

	- **[ヘッダー名]** に「Authorization」と入力します。
	- **[ヘッダー値]** に「Bearer」と入力し、**スペース** キーを押した後、API キーを貼り付けます。
	- **[このサービスは OData]** チェック ボックスをオンにします。
	- **[接続テスト]** をクリックして接続をテストします。

12.	**[カテゴリ]** で、**[Machine Learning]** が選択されていることを確認します。

13. プランに関するメタデータをすべて入力したら、**[発行]** をクリックし、**[Push to Staging]** をクリックします。この時点で、修正する必要のある問題が残っている場合は通知されます。

14. すべての問題が解決されたことを確認したら、**[Request approval to push to Production]** をクリックします。発行プロセスには数営業日かかります。


[image1]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]: ./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]: ./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]: ./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png
 

<!---HONumber=July15_HO4-->