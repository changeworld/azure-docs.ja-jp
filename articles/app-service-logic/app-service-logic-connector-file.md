<properties
	pageTitle="ロジック アプリ でのファイル コネクタの使用 | Microsoft Azure App Service"
	description="ファイル コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
	authors="rajeshramabathiran"
	manager="erikre"
	editor=""
	services="logic-apps"
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="rajram"/>

# ファイル コネクタの使用開始とロジック アプリへの追加
>[AZURE.NOTE] 本記事は、ロジック アプリの 2014-12-01-preview スキーマ バージョンを対象としています。

ファイル システムに接続して、アップロードやダウンロードなど、ホスト コンピューター上のファイルの操作を行います。ロジック アプリはさまざまなデータ ソースを基にトリガーでき、データの取得と処理のためのコネクタを提供します。このファイル コネクタをビジネス ワークフローに追加し、ロジック アプリ内のそのワークフローの一部としてデータを処理できます。

ファイル コネクタは、ホスト ファイル システムへのハイブリッド接続にハイブリッド接続マネージャーを使用します。

## Logic App 用のファイル コネクタを作成する ##
ファイル コネクタを使用するには、まずファイル コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の手順に従います。

1.	Azure ポータルの左側にある [+ 新規] を使用して Azure Marketplace を開きます。
2.	[Marketplace]、[API Apps] の順に移動し、"ファイル コネクタ" を検索します。
3.	次のように、ファイル コネクタを構成します。![][1]

	- **[名前]** -ファイル コネクタの名前を指定します。
	- **[パッケージの設定]**
		- **[ルート フォルダー]** - ホスト コンピューター上のルート フォルダーのパスを指定します。例: D:\\FileConnectorTest
		- **[Service Bus の接続文字列]** - Service Bus の接続文字列を指定します。Service Bus 名前空間の種類が、Basic ではなく Standard になっていることを確認し、Service Bus Relay を使用できるようにします。Service Bus Relay はハイブリッド接続マネージャーへの接続に使用されます。
	- **[App Service プラン]** - App Service プランを選択または作成します。
	- **[価格レベル]** - コネクタの価格レベルを選択します。
	- **[リソース グループ]** - コネクタを格納するリソース グループを選択するか、作成します。
	- **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
	- **[場所]** - コネクタをデプロイする地域を選択します。

4. [作成] をクリックします。新しいファイル コネクタが作成されます。

## ハイブリッド接続マネージャーの構成 ##
API App のインスタンスを作成したら、このダッシュボードに移動します。[参照]、[API Apps]、[ファイル コネクタの API Apps の選択] の順にクリックします。ここから、ハイブリッド接続マネージャーを構成する必要があります。ハイブリッド接続マネージャーの構成とトラブルシューティングの詳細については、「[ハイブリッド接続マネージャーの使用]」を参照してください。

## Logic App でファイル コネクタを使用する ##
API アプリが作成されたら、Logic App のアクションとしてファイル コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	新しい Logic App を作成し、ファイル コネクタと同じリソース グループを選択します。詳細については、[新しい Logic App の作成]に関するページをご覧ください。

2.	作成した Logic App で [トリガーとアクション] を選択してロジック アプリ デザイナーを開き、フローを構成します。

3.	ファイル コネクタが右側のギャラリーの [API Apps in this resource group (このリソース グループの API Apps)] セクションに表示されます。

4.	[ファイル コネクタ] をクリックし、ファイル コネクタの API アプリをエディターにドロップできます。ファイル コネクタでは、1 つのトリガーと 4 つのアクションが表示されます。![][5]

6.	それぞれに特定のプロパティが表示されます。次の図は、トリガーのプロパティと Get File アクションのプロパティを示しています。![][6]

7. これらを構成すると、フローでトリガーとアクションを使用できるようになります。同様に、他のアクションも構成できます。

> [AZURE.NOTE] ファイル トリガーは、ファイルをフォルダーから正常に読み取った後、そのファイルを削除します。

## ファイル コネクタの REST API ##
ロジック アプリの外部のコネクタを使用するには、そのコネクタで公開されている REST API を活用できます。[参照]、[API App]、[ファイル コネクタ] の順に選択して、この API 定義を表示できます。[概要] セクションの [API の定義] レンズをクリックすると、このコネクタで公開されているすべての API が表示されます。![][7]

API の詳細については、「[ファイル コネクタの API の定義]」を参照してください。

## コネクタでできること
コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure Logic Apps の使用を開始する場合は、「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」を参照してください。App Service で有効期間の短いスターター ロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

「[Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[新しい Logic App の作成]: app-service-logic-create-a-logic-app.md
[ファイル コネクタの API の定義]: https://msdn.microsoft.com/library/dn936296.aspx
[ハイブリッド接続マネージャーの使用]: app-service-logic-hybrid-connection-manager.md

<!---HONumber=AcomDC_0803_2016-->