<properties
	pageTitle="Logic Apps での SFTP コネクタの使用 | Microsoft Azure App Service"
	description="SFTP コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
	authors="anuragdalmia"
	manager="erikre"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="sameerch"/>

# SFTP コネクタの使用開始とロジック アプリへの追加
>[AZURE.NOTE] 本記事は、ロジック アプリの 2014-12-01-preview スキーマ バージョンを対象としています。2015-08-01-preview スキーマ バージョンについては、こちらの [SFTP API](../connectors/create-api-sftp.md) をクリックしてください。

SFTP コネクタを使用して、SFTP サーバー間でデータを移動します。ファイルのダウンロード、アップロード、一覧取得を SFTP サーバー間で行うことができます。

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。この SFTP コネクタをビジネス ワークフローに追加し、ロジック アプリ内のそのワークフローの一部としてデータを処理できます。

## Logic App 用の SFTP コネクタを作成する ##
コネクタは、ロジック アプリ内で作成することも、Azure Marketplace から直接作成することもできます。Marketplace からコネクタを作成するには、次の操作を実行します。

1. Azure のスタート画面で、**[Marketplace]** を選択します。
2. "SFTP コネクタ" を検索して選択し、**[作成]** を選択します。
3. 次のように、SFTP コネクタを構成します。![][1]
	- **[場所]** - コネクタをデプロイする地域を選択します。
	- **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
	- **[リソース グループ]** - コネクタを格納するリソース グループを選択するか、作成します。
	- **[Web ホスティング プラン]** - Web ホスティング プランを選択するか、作成します。
	- **[価格レベル]** - コネクタの価格レベルを選択します。
	- **[名前]** - SFTP コネクタの名前を指定します。
	- **パッケージの設定**
		- **[サーバー アドレス]** - SFTP サーバーの名前または IP アドレスを指定します。
		- **[Accept Any SSH Server HostKey (任意の SSH サーバーのホストキーを受け入れる)]** - サーバーから任意の SSH パブリック ホスト キーのフィンガープリントを受け入れるかどうかを決定します。false に設定すると、ホスト キーは [SSH Server Host Key Finger Print (SSH サーバー ホスト キー フィンガープリント)] のプロパティで指定されたキーと一致します。
		- **[SSH Server HostKey (SSH サーバー ホストキー)]** - SSH サーバーのパブリック ホスト キーのフィンガープリントを指定します (*省略可能*)。
		- **[ルート フォルダー]** - ルート フォルダーのパスを指定します。空白の場合は、既定のルートに設定されます。
		- **[Encrypt Cipher (暗号化)]** - 暗号を指定します (*省略可能*)。
		- **[サーバー ポート]** - SFTP サーバーのポート番号を指定します。
4. [作成] をクリックします。新しい SFTP コネクタが作成されます。

5. [参照]、[API Apps]、[<Name of the API App just created>] の順に選択して、作成した API App を参照します。[セキュリティ] コンポーネントが構成されていないことを確認できます。![][2]
6. [セキュリティ] コンポーネントをクリックし、SFTP のコネクタのセキュリティ (ユーザー名、パスワード、秘密キー、PPK ファイル パスワード) を構成します。[セキュリティ] の下にある [パスワード]、[Privatekey]、[MutliFactor] の承認タブを選択し、必要なプロパティを入力します。![][3] ![][4] ![][5]  
6. セキュリティ構成が保存されたら、同じリソース グループ内に、この SFTP コネクタを使用するロジック アプリを作成できます。

## Logic App で SFTP コネクタを使用する ##
API アプリが作成されたら、Logic App のトリガーやアクションとして SFTP コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	新しいロジック アプリを作成し、SFTP コネクタと同じリソース グループを選択します。![][6]
2.	[トリガーとアクション] を選択して Logic Apps デザイナーを開き、フローを構成します。![][7]
3.	SFTP コネクタが右側のギャラリーの [API Apps in this resource group (このリソース グループの API Apps)] セクションに表示されます。![][8]
4.	[SFTP コネクタ] をクリックして、SFTP コネクタの API アプリをエディターにドロップできます。

5.	これで、フローで SFTP コネクタを使用できるようになりました。今後は、SFTP トリガー ("TriggerOnFileAvailable") を使って取得したファイルを、フローの他のアクションで使用できます。

	> [AZURE.IMPORTANT] SFTP トリガー "TriggerOnFileAvailable" は、取得したファイルを処理した後、そのファイルを削除します。

6.	SFTP トリガーの入力プロパティを次のように構成します。

	- **[フォルダー パス]** - ファイルを取得する必要があるフォルダーのパスを指定します。
	- **[ファイルの種類: テキストまたはバイナリ]** - ファイルの種類を選択します。
	- **[ファイル マスク]** -ファイルを取得するために適用するファイル マスクを指定します。「*」を指定すると、指定したフォルダー内のすべてのファイルを取得します。
- **[ファイル マスクの除外]** - ファイルを除外するために適用するファイル マスクを指定します。[ファイル マスク] のプロパティも設定した場合は、先に [ファイル マスクの除外] のプロパティが適用されます。


	![][9] ![][10]

7.	フローで SFTP アクションを使用する方法もほぼ同じです。[ファイルのアップロード] アクションを使用して SFTP サーバーにファイルをアップロードできます。[ファイルのアップロード] アクションの入力プロパティを次のように構成します。

	- **[コンテンツ]** - アップロードするファイルのコンテンツを指定します
	- **[コンテンツ転送エンコード]** - [なし] または [Base64] を指定します。
	- **[ファイル パス]** - アップロードするファイルのファイル パスを指定します。
	- **[上書き]** - ファイルが既に存在する場合に上書きするには、"true" を指定します
	- ****[存在する場合に追加]** - "true" または "false" を指定します。"true" に設定すると、ファイルが存在する場合にファイルにデータが追加されます。"false" に設定すると、ファイルが存在する場合にファイルは上書きされます。
- **[一時フォルダー]** - 指定した場合、アダプターは、ファイルを "一時フォルダー パス" にアップロードします。アップロードが完了すると、ファイルは "フォルダー パス" に移動されます。移動操作がアトミックであるためには、"一時フォルダー パス" が "フォルダー パス" と同じ物理ディスクにある必要があります。一時フォルダーは、"存在する場合に追加" プロパティが無効の場合にのみ使用できます。

	![][11] ![][12]

## コネクタでできること
コネクタが作成されたため、ロジック アプリを使用してコネクタをビジネス ワークフローに追加できます。「[Logic Apps とは](app-service-logic-what-are-logic-apps.md)」を参照してください。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure Logic Apps の使用を開始する場合は、「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appservice=logic)」を参照してください。App Service で有効期間の短いスターター ロジック アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

「[Connectors and API Apps Reference (コネクタと API Apps のリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

パフォーマンス統計をレビューし、コネクタに対するセキュリティを制御することもできます。[組み込みの API Apps とコネクタの管理と監視](app-service-logic-monitor-your-connectors.md)に関するページを参照してください。


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-sftp/img1.PNG
[2]: ./media/app-service-logic-connector-sftp/img2.PNG
[3]: ./media/app-service-logic-connector-sftp/img3.PNG
[4]: ./media/app-service-logic-connector-sftp/img4.PNG
[5]: ./media/app-service-logic-connector-sftp/img5.PNG
[6]: ./media/app-service-logic-connector-sftp/img6.PNG
[7]: ./media/app-service-logic-connector-sftp/img7.png
[8]: ./media/app-service-logic-connector-sftp/img8.png
[9]: ./media/app-service-logic-connector-sftp/img9.PNG
[10]: ./media/app-service-logic-connector-sftp/img10.PNG
[11]: ./media/app-service-logic-connector-sftp/img11.PNG
[12]: ./media/app-service-logic-connector-sftp/img12.PNG

<!---HONumber=AcomDC_0224_2016-->