<properties 
	pageTitle="Media Services アカウントの作成 - Azure" 
	description="Azure で新しい Media Services アカウントを作成する方法について説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


#Media Services アカウントの作成方法

この記事は、[メディア サービスのビデオ オンデマンド ワークフロー](media-services-video-on-demand-workflow.md) および[メディア サービスのライブ ストリーミングのワークフロー](media-services-live-streaming-workflow.md) シリーズの一部です。  

Azure 管理ポータルには、Azure Media Services アカウントをすばやく作成する方法が用意されています。アカウントを使用してメディア サービスにアクセスすると、Azure でメディア コンテンツを保存、暗号化、エンコード、管理、ストリーミングができます。Media Services アカウントを作成するときは、同時に Media Services アカウントと同じリージョンにストレージ アカウントも作成して関連付けます (または既存のストレージ アカウントを使用します)。 

このトピックでは、簡易作成の方法を使って新しい Media Services アカウントを作成し、それをストレージ アカウントに関連付ける方法について説明します。 

##<a id="concepts"></a>概念
メディア サービスにアクセスするには、関連付けられた次の 2 つのアカウントが必要です。

-   **Media Services アカウント**。アカウントを使用して、Azure で利用可能なクラウド ベースのメディア サービスにアクセスできます。Media Services アカウントには実際のメディア コンテンツは保存されません。代わりに、メディア コンテンツに関するメタデータとメディア処理ジョブがアカウントに保存されます。アカウントを作成するときに、利用可能な Media Services リージョンを選択します。選択したリージョンに、アカウントのメタデータ レコードを保存するデータ センターが配置されます。 

    > [AZURE.NOTE]
    > 利用可能な Media Services リージョンは、**北ヨーロッパ**、**西ヨーロッパ**、**米国西部**、**米国東部**、**東南アジア**、**東アジア**、**西日本**、**東日本**です。メディア サービスは、アフィニティ グループを使用しません。 
-   **関連付けられたストレージ アカウント**。ストレージ アカウントは、Media Services アカウントに関連付けられた Azure ストレージ アカウントです。ストレージ アカウントにはメディア ファイルの BLOB ストレージが用意されます。また、Media Services アカウントと同じリージョンに配置する必要があります。Media Services アカウントを作成するときに、同じリージョンにある既存のストレージ アカウントを選択したり、同じリージョンに新しいストレージ アカウントを作成したりできます。Media Services アカウントを削除しても、関連付けられたストレージ アカウントにある BLOB は削除されません。 

##<a id="quick"></a>方法: 簡易作成による Media Services アカウントの作成

1. [管理ポータル][]で、**[新規]**、**[Media Services]**、**[簡易作成]** の順にクリックします。
   
	![Media Services Quick Create](./media/media-services-create-account/wams-QuickCreate.png)

2. **[名前]** ボックスに新しいアカウントの名前を入力します。Media Services アカウント名は、使用できる文字が小文字または数字だけで、空白を含めることはできず、長さは 3 ～ 24 文字です。 

3. **[リージョン]** ボックスで、Media Services アカウントのメタデータ レコードを保存するリージョンを選択します。ドロップダウン リストには利用可能な Media Services リージョンだけが表示されます。 

4. **[ストレージ アカウント]** ボックスで、Media Services アカウントのメディア コンテンツの BLOB ストレージとなるストレージ アカウントを選択します。Media Services アカウントと同じリージョンにある既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。新しいストレージ アカウントは同じリージョンに作成されます。 

5. 新しいストレージ アカウントを作成した場合は、**[新しいストレージ アカウント名]** ボックスにストレージ アカウントの名前を入力します。ストレージ アカウントの命名規則は、Media Services アカウントと同じです。

6. フォームの下部にある **[簡易作成]** をクリックします。

	処理の状態はウィンドウの下部にあるメッセージ領域で監視できます。

	**[メディア サービス]** ページが開き、新しいアカウントが表示されます。状態が "有効" に変わると、アカウントが正常に作成されています。

	![Media Services Page](./media/media-services-create-account/wams-mediaservices-page.png)

	アカウント名をダブルクリックすると、既定で [クイック スタート] ページが表示されます。このページでは、ポータルの別のページでも実行できる管理タスクをいくつか実行できます。たとえば、ビデオ ファイルのアップロードは、このページから実行したり、[コンテンツ] ページから実行したりできます。

	さらに、Azure Media Services SDK を使用して、ビデオのアップロード、エンコード、発行を実行するコードを表示できます。[コードを記述] にあるリンクをクリックして、コードをコピーし、それをアプリケーションで使用できます。 


<!-- Reusable paths. -->

<!-- Anchors. -->
  [概念]: #concepts
  [開始する前に]: #begin
  [方法: 簡易作成による Media Services アカウントの作成]: #quick

<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  
  [管理ポータル]: http://manage.windowsazure.com/



<!--HONumber=52-->