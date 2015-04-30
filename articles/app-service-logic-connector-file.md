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
	ms.date="04/09/2015"
	ms.author="andalmia"/>

# ロジック アプリで File コネクタを使用する #

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。 

File コネクタを使用すると、ホスト コンピューターのファイル システムからファイルをアップロード、ダウンロード、削除できます。

## ロジック アプリ用の File コネクタを作成する ##
File コネクタを使用するには、まず File コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の手順に従います。

1.	Azure ポータルの左下にある [+ 新規] を使用して Azure Marketplace を開きます。
2.	[Web + モバイル]、[Azure Marketplace] の順に移動して、"File コネクタ" を検索します。
3.	次のように、File コネクタを構成します。
 
	![][1]
 
	- **[名前]** - File コネクタの名前を指定します。
	- **[パッケージ設定]**
		- **[ルート フォルダー]** - ホスト  コンピューター上のルート フォルダーのパスを指定します。例: D:\FileConnectorTest
		- **[Service Bus の接続文字列]** - Service Bus の接続文字列を指定します。サービス バスの名前空間の種類が、基本ではなく標準になっていることを確認します。
	- **[App Service プラン]** - App Service プランを選択または作成します
	- **[価格レベル]** - コネクタの価格レベルを選択します。
	- **[リソース グループ]** - コネクタを格納するリソース グループを選択するか、作成します。
	- **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
	- **[場所]** - コネクタをデプロイする地域を選択します。

4. [作成] をクリックします。新しい File コネクタが作成されます。
5. API アプリのインスタンスを作成したら、このダッシュボードに移動します。[参照]、[API アプリ]、[Select your File Connector API App (File コネクタの API アプリの選択)] の順にクリックします。
6. File コネクタ ダッシュボードで、[Hybrid Connection (ハイブリッド接続)] レンズに [On-Premise Setup Incomplete (オンプレミス セットアップが不完全)] と表示されているのを確認できます。ハイブリッドのセットアップを完了するには、ファイル システムに接続する必要があるホスト コンピューターからこのダッシュ ボードを開きます。次に、[Hybrid Connection (ハイブリッド接続)] レンズをクリックします。[Hybrid Connection (ハイブリッド接続)] ブレードが開いたら、[Download and configure (ダウンロードして構成)] リンクをクリックして On-premises Hybrid Connection Manager をインストールします。

	![][2]

7. インストーラーが起動し、[Relay Listen Connection String (リレー リッスン接続文字列)] の入力が求められます。[Hybrid Connection (ハイブリッド接続)] ブレードから [Primary Configuration String (プライマリ構成文字列)] をコピーして貼り付けます。[インストール] をクリックします。

	![][3]

8. ハイブリッド インストールが成功したことを確認するには、File コネクタ ダッシュボードをもう一度開きます。ハイブリッド接続の状態が「接続済み」と表示されます。

	![][4]

## ロジック アプリで File コネクタを使用する ##
API アプリを作成すると、ロジック アプリのアクションとして File コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	新しいロジック アプリを作成し、File コネクタと同じリソース グループを選択します。詳細については、「[Create a new Logic App (新しいロジック アプリの作成)]」をご覧ください。  	
	
2.	作成したロジック アプリで [トリガーとアクション] を選択してロジック アプリ デザイナーを開き、フローを構成します。  	
	
3.	File コネクタが右側のギャラリーの [API Apps in this resource group (このリソース グループの API アプリ)] セクションに表示されます。
 	
4.	[File コネクタ] をクリックして、File コネクタの API アプリをエディターにドロップできます。File コネクタは、1 つのトリガーと 4 つのアクションを表示します。
 
	![][5]

6.	それぞれに特定のプロパティが表示されます。次の図は、トリガーのプロパティと Get File アクションのプロパティを示しています。
 
	![][6]

7. これらを構成すると、フローでトリガーとアクションを使用できるようになります。同様に、他のアクションも構成できます。

8. ロジック アプリの外部のコネクタを使用するには、そのコネクタで公開されている REST API を活用できます。[参照]、[API アプリ]、[File コネクタ] の順に選択して、この API 定義を表示できます。[概要] セクションの [API Definition (API 定義)] レンズをクリックすると、このコネクタで公開されているすべての API が表示されます。

	![][7]

9. API の詳細については、「[File コネクタの API の定義]」をご覧ください。

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[2]: ./media/app-service-logic-connector-file/img2.PNG
[3]: ./media/app-service-logic-connector-file/img3.PNG
[4]: ./media/app-service-logic-connector-file/img4.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Create a new Logic App (新しいロジック アプリの作成)]: app-service-logic-create-a-logic-app.md
[File コネクタの API の定義]: https://msdn.microsoft.com/en-US/library/dn936296.aspx

<!--HONumber=52-->