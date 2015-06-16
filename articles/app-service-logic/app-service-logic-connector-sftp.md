<properties 
	pageTitle="SFTP コネクタ"
	description="SFTP コネクタの使用"
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
	ms.date="03/31/2015"
	ms.author="adgoda"/>

# ロジック アプリで SFTP コネクタを使用する #

ロジック アプリはさまざまなデータ ソースを基にトリガーでき、フローの一環としてデータの取得と処理のためのコネクタを提供します。 

SFTP コネクタを使用すると、SFTP サーバー間でデータを移動できます。ファイルのダウンロード、アップロード、一覧を SFTP サーバー間で行うことができます。

## ロジック アプリ用の SFTP コネクタを作成する ##
SFTP コネクタを使用するには、まず SFTP コネクタ API アプリのインスタンスを作成する必要があります。そのためには、次の手順に従います。

1.	Azure ポータルの左下にある [+ 新規] を使用して Azure Marketplace を開きます。
2.	[Web + モバイル]、[API アプリ] の順に移動して、"SFTP コネクタ" を検索します。
3.	次のように、SFTP コネクタを構成します。
 
	![][1] 
	- **[場所]** - コネクタをデプロイする地域を選択します。
	- **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
	- **[リソース グループ]** - コネクタを格納するリソース グループを選択するか、作成します。
	- **[Web ホスティング プラン]** - Web ホスティング プランを選択するか、作成します。
	- **[価格レベル]** - コネクタの価格レベルを選択します。
	- **[名前]** - SFTP コネクタの名前を指定します。
	- **パッケージ設定**
		- **[サーバー アドレス]** - SFTP サーバーの名前または IP アドレスを指定します。
		- **[Accept Any SSH Server HostKey (任意の SSH サーバーのホストキーを受け入れる)]** - サーバーから任意の SSH パブリック ホスト キーのフィンガープリントを受け入れるかどうかを決定します。false に設定すると、ホスト キーは [SSH Server Host Key Finger Print (SSH サーバー ホスト キー フィンガープリント)] のプロパティで指定されたキーと一致します。
		- **[SSH Server HostKey (SSH サーバー ホストキー)]** - SSH サーバーのパブリック ホスト キーのフィンガープリントを指定します。
		- **[ルート フォルダー]** - ルート フォルダーのパスを指定します。
		- **[Encrypt Cipher (暗号化)]** - 暗号を指定します。
		- **[サーバー ポート]** - SFTP サーバーのポート番号を指定します。
4. [作成] をクリックします。新しい SFTP コネクタが作成されます。

5. [参照]、[API アプリ]、[<作成した API アプリの名前>] の順に選択して、作成した API アプリを参照します。[セキュリティ] コンポーネントが構成されていないことを確認できます。 

	![][2]
6. [セキュリティ] コンポーネントをクリックし、SFTP のコネクタのセキュリティ (ユーザー名、パスワード、秘密キー、PPK ファイル パスワード) を構成します。 
[セキュリティ] の下にある [パスワード]、[Privatekey]、[MutliFactor] の承認タブを選択し、必要なプロパティを入力します。

	![][3]
	![][4]
	![][5]
6. セキュリティ構成を保存すると、同じリソース グループ内にロジック アプリを作成して SFTP コネクタを使用できます。 

## ロジック アプリで SFTP コネクタを使用する ##
API アプリを作成すると、ロジック アプリのトリガーやアクションとして SFTP コネクタを使用できます。そのためには、次の手順を実行する必要があります。

1.	新しいロジック アプリを作成し、SFTP コネクタと同じリソース グループを選択します。
 	
	![][6]
2.	[トリガーとアクション] を選択してロジック アプリ デザイナーを開き、フローを構成します。 
 	
	![][7]
3.	SFTP コネクタが右側のギャラリーの [API Apps in this resource group (このリソース グループの API アプリ)] セクションに表示されます。
 
	![][8]
4.	[SFTP コネクタ] をクリックして、SFTP コネクタの API アプリをエディターにドロップできます。
 
	
6.	これで、フローで SFTP コネクタを使用できるようになりました。今後は、SFTP トリガー ("TriggerOnFileAvailable") を使って取得したファイルを、フローの他のアクションで使用できます。 

	**注:** SFTP トリガー "TriggerOnFileAvailable" は、ファイルの処理後に取得されたファイルを削除します。

8.	SFTP トリガーの入力プロパティを次のように構成します。

	- **[フォルダー パス]** - ファイルを取得する必要があるフォルダーのパスを指定します。
	- **[The type of the file: text or binary (ファイルの種類: テキストまたはバイナリ)]** - ファイルの種類を選択します。
	- **[ファイル マスク]** - ファイルを取得するために適用するファイル マスクを指定します。'*' は、指定したフォルダー内のすべてのファイルを取得します。
	- **[Exclude File Mask (ファイル マスクの除外)]** - ファイルを除外するために適用するファイルマスクを指定します。[ファイル マスク] も設定すると、Exclude File Mask (ファイル マスクの除外)] が最初に適用されます。

 
	![][9] 
	![][10]

7.	フローで SFTP アクションを使用する方法もほぼ同じです。[ファイルのアップロード] アクションを使用して SFTP サーバーにファイルをアップロードできます。[ファイルのアップロード] アクションの入力プロパティを次のように構成します。

	- **[コンテンツ]** - アップロードするファイルのコンテンツを指定します。
	- **[コンテンツ転送エンコード]** - [なし] または [base64] を指定します。
	- **[ファイル パス]** - アップロードするファイルのファイル パスを指定します。
	- **[上書き]** - ファイルが既に存在する場合は、"true"を 指定して上書きします。
	- **[Append If Exists (存在する場合に追加)]** - "true" または "false" を指定します。"true" に設定すると、ファイルが存在する場合にファイルにデータが追加されます。"false" に設定すると、ファイルが存在する場合にファイルは上書きされます。
	- **[一時フォルダー]** - 指定した場合、アダプターは、ファイルを '一時フォルダー パス' にアップロードします。アップロードが完了すると、ファイルは 'フォルダー パス' に移動されます。移動操作がアトミックであるためには、'一時フォルダー パス' が 'フォルダー パス' と同じ物理ディスクにある必要があります。一時フォルダーは、'[Append If Exists (存在する場合に追加)]' プロパティが無効の場合にのみ使用できます。

	![][11]
	![][12]





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


<!--HONumber=52--> 