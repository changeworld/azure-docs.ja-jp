<properties
	pageTitle="File コネクタ"
	description="File コネクタの使用"
	authors="anuragdalmia"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="andalmia"/>

# ロジック アプリで File コネクタを使用する #

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。

File コネクタを使用すると、ホスト コンピューターのファイル システムからファイルをアップロード、ダウンロード、削除できます。ホスト コンピューターへのハイブリッド接続にはハイブリッド接続マネージャーを使用します。

## Logic App 用の File コネクタを作成する ##
File コネクタを使用するには、まず File コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の手順に従います。

1.	Azure ポータルの左側にある [+ 新規] を使用して Azure Marketplace を開きます。
2.	[Marketplace]、[API Apps] の順に移動し、"File コネクタ" を検索します。
3.	次のように、File コネクタを構成します。

	![][1]

	- **[名前]** - File コネクタの名前を指定します。
	- **[パッケージの設定]**
		- **[ルート フォルダー]** - ホスト コンピューター上のルート フォルダーのパスを指定します。例: D:\\FileConnectorTest
		- **[Service Bus の接続文字列]** - Service Bus の接続文字列を指定します。Service Bus 名前空間の種類が、Basic ではなく Standard になっていることを確認し、Service Bus Relay を使用できるようにします。Service Bus Relay はハイブリッド接続マネージャーへの接続に使用されます。
	- **[App Service プラン]** - App Service プランを選択または作成します。
	- **[価格レベル]** - コネクタの価格レベルを選択します。
	- **[リソース グループ]** - コネクタを格納するリソース グループを選択するか、作成します。
	- **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
	- **[場所]** - コネクタをデプロイする地域を選択します。

4. [作成] をクリックします。新しい File コネクタが作成されます。

## ハイブリッド接続マネージャーの構成 ##
API App のインスタンスを作成したら、このダッシュボードに移動します。[参照]、[API Apps]、[File コネクタの API Apps の選択] の順にクリックします。ここから、ハイブリッド接続マネージャーを構成する必要があります。ハイブリッド接続マネージャーの構成とトラブルシューティングの詳細については、「[ハイブリッド接続マネージャーの使用]」を参照してください。

## Logic App で File コネクタを使用する ##
API アプリが作成されたら、Logic App のアクションとして File コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	新しい Logic App を作成し、File コネクタと同じリソース グループを選択します。詳細については、[新しい Logic App の作成]に関するページをご覧ください。

2.	作成した Logic App で [トリガーとアクション] を選択して Logic Apps デザイナーを開き、フローを構成します。

3.	File コネクタが右側のギャラリーの [API Apps in this resource group (このリソース グループの API Apps)] セクションに表示されます。

4.	[File コネクタ] をクリックして、File コネクタの API アプリをエディターにドロップできます。File コネクタでは、1 つのトリガーと 4 つのアクションが表示されます。

	![][5]

6.	それぞれに特定のプロパティが表示されます。次の図は、トリガーのプロパティと Get File アクションのプロパティを示しています。

	![][6]

7. これらを構成すると、フローでトリガーとアクションを使用できるようになります。同様に、他のアクションも構成できます。

> [AZURE.NOTE]ファイル トリガーは、ファイルをフォルダーから正常に読み取った後、そのファイルを削除します。

## File コネクタの REST API ##
ロジック アプリの外部のコネクタを使用するには、そのコネクタで公開されている REST API を活用できます。[参照]、[API App]、[File コネクタ] の順に選択して、この API 定義を表示できます。[概要] セクションの [API Definition (API 定義)] レンズをクリックすると、このコネクタで公開されているすべての API が表示されます。

  ![][7]

API の詳細については、「[File コネクタの API の定義]」を参照してください。

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[新しい Logic App の作成]: app-service-logic-create-a-logic-app.md
[File コネクタの API の定義]: https://msdn.microsoft.com/en-US/library/dn936296.aspx
[ハイブリッド接続マネージャーの使用]: app-service-logic-hybrid-connection-manager.md

<!---HONumber=July15_HO3-->