<properties
	pageTitle="ストレージ エクスプローラー (プレビュー) の概要 | Microsoft Azure"
	description="ストレージ エクスプローラー (プレビュー) を使用した Azure ストレージ リソースの管理"
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="visual-studio-online"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="06/05/2016"
	ms.author="tarcher" />

# ストレージ エクスプローラー (プレビュー) の概要

## 概要 

Microsoft Azure ストレージ エクスプローラー (プレビュー) は、Windows、OSX、Linux で Azure Storage データを容易に操作できるスタンドアロン アプリです。この記事では、Azure ストレージ アカウントへの接続と管理に関するさまざまな方法を紹介します。

## 前提条件

- [ストレージ エクスプローラー (プレビュー) をダウンロードしてインストールする](http://go.microsoft.com/fwlink/?LinkId=708343)

## ストレージ アカウントまたはサービスに接続する

ストレージ エクスプローラー (プレビュー) には、ストレージ アカウントへの多数の接続方法が用意されています。たとえば、Azure サブスクリプションに関連付けられているストレージ アカウントへの接続、他の Azure サブスクリプションから共有されているストレージ アカウントおよびサービスへの接続、Azure ストレージ エミュレーターを使ったローカル ストレージへの接続と管理などです。

- [Azure サブスクリプションに接続する](#connect-to-an-azure-subscription) - Azure サブスクリプションに属するストレージ リソースを管理します。
- [ローカル ストレージに接続する](#connect-to-local-storage) - Azure ストレージ エミュレーターを使ってローカル ストレージを管理します。 
- [外部ストレージにアタッチする](#attach-or-detach-an-external-storage-account) - ストレージ アカウントのアカウント名とキーを使って、別の Azure サブスクリプションに属するストレージ リソースを管理します。
- [SAS を使ってアカウントをアタッチする](#attach-account-using-sas) - SAS を使って、別の Azure サブスクリプションに属するストレージ リソースを管理します。
- [SAS を使ってサービスをアタッチする](#attach-service-using-sas) - SAS を使って、別の Azure サブスクリプションに属する特定のストレージ サービス (BLOB コンテナー、キュー、またはテーブル) を管理します。

## Azure サブスクリプションに接続する

> [AZURE.NOTE] Azure アカウントを持っていない場合は、[無料試用版にサインアップする](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)か、[Visual Studio サブスクライバー特典を有効](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)にしてください。

1. ストレージ エクスプローラー (プレビュー) を起動します。 

1. ストレージ エクスプローラー (プレビュー) を初めて実行する場合や、これまでにストレージ エクスプローラー (プレビュー) を実行したことはあっても Azure アカウントに接続したことがない場合は、Azure アカウントに接続するための情報バーが表示されます。

	![][0]
	
1. **[Microsoft Azure への接続]** を選択し、ダイアログに従って、少なくとも 1 つのアクティブな Azure サブスクリプションと関連付けられている Microsoft アカウントでサインインします。

Microsoft アカウントで正常にサインインすると、ストレージ エクスプローラー (プレビュー) の左側のウィンドウに、すべてのストレージ アカウント (Microsoft アカウントに関連付けられているすべての Azure サブスクリプションに関連付けられているもの) が表示されます。

### Azure サブスクリプションをフィルター処理する

ストレージ エクスプローラー (プレビュー) では、サインインしている Microsoft アカウントに関連付けられている Azure サブスクリプションをフィルター処理し、左側のウィンドウに表示されるストレージ アカウントを絞り込むことができます。

1. **[設定]** (歯車) アイコンを選択します。

	![][1]

1. 	左側のウィンドウの上部に、サインインしたすべての Microsoft アカウントが含まれたドロップダウン リストが表示されます。

	![][3]
	 
1.	ドロップダウン リストの横にある下向き矢印を選択して、サインインしているすべての Microsoft アカウントのほか、Microsoft アカウントを追加するための (さらにサインインするための) リンクを表示します。

	![][4]

1.	ドロップダウン リストで目的の Microsoft アカウントを選択します。

1. 	左側のウィンドウに、選択した Microsoft アカウントに関連付けられているすべての Azure サブスクリプションが表示されます。各 Azure サブスクリプションの左側にあるチェック ボックスでは、その Azure サブスクリプションに関連付けられているすべてのストレージ アカウントをストレージ エクスプローラー (プレビュー) に一覧表示するかどうかを指定することができます。**[すべてのサブスクリプション]** チェック ボックスをオンまたはオフにすることで、一覧の Azure サブスクリプションがすべて選択された状態と、1 つも選択されていない状態とを切り替えることができます。

	![][2]

1.	管理する Azure サブスクリプションを選択し終えたら、**[適用]** を選択します。左側のウィンドウが更新され、現在の Microsoft アカウントに対して選択された各 Azure サブスクリプションに関連付けられているすべてのストレージ アカウントが一覧表示されます。

### Microsoft アカウントを追加する

次の手順では、新たに別の Microsoft アカウントに接続して、各アカウントの Azure サブスクリプションとストレージ アカウントを表示します。

1.	**[設定]** (歯車) アイコンを選択します。

	![][1]

1. 	左側のウィンドウの上部に、現在選択されているすべての Microsoft アカウントが含まれたドロップダウン リストが表示されます。

	![][3]
	 
1.	ドロップダウン リストの横にある下向き矢印を選択して、サインインしているすべての Microsoft アカウントのほか、Microsoft アカウントを追加するための (さらにサインインするための) リンクを表示します。

	![][4]

1.	**[アカウントの追加]** を選択し、ダイアログに従って、少なくとも 1 つのアクティブな Azure サブスクリプションに関連付けられているアカウントにサインインします。

1.	参照する Azure サブスクリプションのチェック ボックスをオンにします。

	![][2]

1.	**[適用]** を選択します。

### Microsoft アカウントを切り替える

複数の Microsoft アカウントに接続できる場合、左側のウィンドウには、1 つの (現在の) Microsoft アカウントのサブスクリプションに関連付けられているストレージ アカウントのみが表示されます。複数の Microsoft アカウントに接続する場合は、次の手順を実行してアカウントを切り替えることができます。

1.	**[設定]** (歯車) アイコンを選択します。

	![][1]

1. 	左側のウィンドウの上部に、現在選択されているすべての Microsoft アカウントが含まれたドロップダウン リストが表示されます。

	![][3]
	 
1.	ドロップダウン リストの横にある下向き矢印を選択して、サインインしているすべての Microsoft アカウントのほか、Microsoft アカウントを追加するための (さらにサインインするための) リンクを表示します。

	![][4]

1.	目的の Microsoft アカウントを選択します。

1.	参照する Azure サブスクリプションのチェック ボックスをオンにします。

	![][2]

1.	**[適用]** を選択します。
  
## ローカル ストレージに接続する

ストレージ エクスプローラー (プレビュー) では、Azure ストレージ エミュレーターを使ってローカル ストレージを操作することができます。そのため、Azure にデプロイされたストレージ アカウントがなくても、ストレージ関連のコードを記述したり、ストレージをテストしたりできます (ストレージ アカウントが Azure ストレージ エミュレーターによってエミュレートされるため)。

>[AZURE.NOTE] 現時点では、Azure ストレージ エミュレーターは Windows に対してのみサポートされています。

1. ストレージ エクスプローラー (プレビュー) を起動します。 

1. 左側のウィンドウで **[(開発)]** ノードを展開します。

	![][21]

1. Azure ストレージ エミュレーターをインストールしていない場合は、情報バーを使用してインストールするよう求められます。情報バーが表示されている場合は、**[最新バージョンのダウンロード]** を選択し、エミュレーターをインストールします。

	![][22]

1. エミュレーターがインストールされたら、ローカルの BLOB、キュー、テーブルを作成および操作できるようになります。それぞれの種類のストレージ アカウントを操作する方法については、以下の該当するリンクを選択して確認してください。

	- [Azure Blob Storage リソースの管理](./vs-azure-tools-storage-explorer-blobs.md)
	- Azure Queue Storage リソースの管理 - *近日公開予定*
	- Azure Table Storage リソースの管理 - *近日公開予定*

## 外部ストレージ アカウントをアタッチまたはデタッチする

ストレージ エクスプローラー (プレビュー) には、ストレージ アカウントを簡単に共有できるように、外部ストレージ アカウントにアタッチする機能が用意されています。このセクションでは、外部ストレージ アカウントにアタッチ (または外部ストレージ アカウントからデタッチ) する方法について説明します。

### ストレージ アカウントの資格情報を取得する

外部ストレージ アカウントを共有するには、まずそのアカウントの所有者がアカウントの資格情報 (アカウント名とキー) を取得し、その情報を、その (外部) アカウントへのアタッチを望んでいるユーザーと共有する必要があります。ストレージ アカウントの資格情報は、Azure ポータルを使って次の手順で取得できます。

1.	[Azure ポータル](https://portal.azure.com)にサインインします。
1.	**[参照]** を選択します。
1.	**[ストレージ アカウント]** を選択します。
1.	**[ストレージ アカウント]** ブレードで目的のストレージ アカウントを選択します。
1.	選択したストレージ アカウントの **[設定]** ブレードで、**[アクセス キー]** を選択します。

	![][5]
	
1.	**[アクセス キー]** ブレードで、ストレージ アカウントにアタッチするときに使用する**ストレージ アカウント名**と**キー 1** の値をコピーします。

	![][6]

### 外部ストレージ アカウントにアタッチする

1.	ストレージ エクスプローラー (プレビュー) で、**[ストレージ アカウント]** を右クリックし、コンテキスト メニューの **[外部ストレージのアタッチ]** を選択します。

	![][7]
	
1.	セクション「*ストレージ アカウントの資格情報を取得する*」に、ストレージ アカウント名とキー 1 の値を取得する方法が記載されています。これらの値はこの手順で使います。**[外部ストレージのアタッチ]** ダイアログで、**[アカウント名]** ボックスにストレージ アカウント名を入力し、**[アカウント キー]** ボックスにキー 1 の値を入力します。完了したら、**[OK]** を選択します。

	![][8]

	アタッチされると、ストレージ アカウント名に "**(外部)**" というテキストが追加された状態で外部ストレージ アカウントが表示されます。

	![][9]

### 外部ストレージ アカウントからデタッチする

1. 	デタッチする外部ストレージ アカウントを右クリックし、コンテキスト メニューの **[デタッチ]** を選択します。

	![][10]

1.	確認のメッセージ ボックスが表示されたら、**[はい]** を選択して外部ストレージ アカウントからのデタッチを確定します。

	![][12]

## SAS を使ってアカウントをアタッチする

Azure サブスクリプションの管理者は、SAS (Shared Access Signature) を使うことで、Azure サブスクリプションの資格情報を提供しなくてもストレージ アカウントへのアクセスを一時的に許可できます。

これをわかりやすく説明するために、Azure サブスクリプションの管理者である UserA が UserB に特定のアクセス許可を付与し、一時的にストレージ アカウントへのアクセスを許可する場面を考えてみます。

1. UserA は、目的のアクセス許可と一定の期間が設定された SAS (ストレージ アカウントへの接続文字列で構成) を生成します。
1. UserA は、ストレージ アカウントへのアクセスを希望しているユーザー (この例では UserB) と SAS を共有します。  
1. UserB は、ストレージ エクスプローラー (プレビュー) で、提供された SAS を使って UserA に属するアカウントにアタッチします。 

### 共有するアカウント用の SAS を取得する

1.	ストレージ エクスプローラー (プレビュー) を起動します。
1.	左側のウィンドウで、共有するストレージ アカウントを右クリックし、コンテキスト メニューの **[Get Shared Access Signature (Shared Access Signature の取得)]** を選択します。

	![][13]

1. **[Shared Access Signature]** ダイアログで、アカウントに対して期間とアクセス許可を指定し、**[作成]** を選択します。

	![][14]
 
1. 2 つ目の **[Shared Access Signature]** ダイアログが表示され、SAS が示されます。**[接続文字列]** の横にある **[コピー]** を選択してクリップボードにコピーします。**[閉じる]** を選択してダイアログを閉じます。

### SAS を使って共有アカウントにアタッチする

1.	ストレージ エクスプローラー (プレビュー) を起動します。
1.	左側のウィンドウで **[ストレージ アカウント]** を右クリックし、コンテキスト メニューの **[Attach Account using SAS (SAS を使用してアカウントをアタッチ)]** を選択します。![][15]

1. **[Attach account using SAS (SAS を使用してアカウントをアタッチ)]** ダイアログで以下の操作を行います。

	- **アカウント名** - このアカウントに関連付ける名前を入力します。**注:** このアカウント名は、SAS が生成された元のストレージ アカウント名と一致していなくてもかまいません。 
 	- **接続文字列** - 先ほどコピーした接続文字列を貼り付けます。
 	- 完了したら、**[OK]** を選択します。
	
	![][16]

アタッチされると、指定したアカウント名に "(SAS)" というテキストが追加された状態でストレージ アカウントが表示されます。

![][17]

## SAS を使ってサービスをアタッチする

セクション「[SAS を使ってアカウントをアタッチする](#attach-account-using-sas)」では、Azure サブスクリプションの管理者がストレージ アカウントの SAS を生成 (および共有) してストレージ アカウントへの一時的なアクセスを許可する方法が示されています。同様に、SAS は、ストレージ アカウント内の特定のサービス (BLOB コンテナー、キュー、またはテーブル) に対して生成できます。

### 共有するサービスの SAS を生成する

この場合のサービスは、BLOB、コンテナー、キュー、またはテーブルです。次のセクションで、一覧にあるサービスの SAS を生成する方法を説明します。

- [BLOB コンテナーの SAS を取得する](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- キューの SAS を取得する - *近日公開予定*
- テーブルの SAS を取得する - *近日公開予定*

### SAS を使って共有アカウント サービスにアタッチする

1.	ストレージ エクスプローラー (プレビュー) を起動します。
1.	左側のウィンドウで **[ストレージ アカウント]** を右クリックし、コンテキスト メニューの **[Attach Service using SAS (SAS を使用してサービスをアタッチ)]** を選択します。![][18]

1. **[Attach Account using SAS (SAS を使用してアカウントをアタッチ)]** ダイアログで、先ほどコピーした SAS URI を貼り付け、**[OK]** を選択します。

	![][19]

アタッチされると、新たにアタッチされたサービスが **[(Service SAS) ((サービス SAS))]** ノードに表示されます。

![][20]

## ストレージ アカウントを検索する

多数のストレージ アカウントがある場合は、左側のウィンドウの上部にある検索ボックスを使用すると、特定のストレージ アカウントをすばやく見つけることができます。

検索ボックスへの入力中、左側のウィンドウには、その時点で入力してある検索の値に一致するストレージ アカウントのみが表示されます。次のスクリーンショットは、ストレージ アカウント名に "tarcher" というテキストが含まれるすべてのストレージ アカウントを検索したときの例です。

![][11]
	
検索をクリアするには、検索ボックスの **[x]** ボタンを選択します。

## 次のステップ
- [ストレージ エクスプローラー (プレビュー) を使用した Azure Blob Storage リソースの管理](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-gear.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-subscriptions.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-accounts.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/accounts-drop-down.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-context-menu.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-dlg.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-context-menu.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-dlg.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png

<!---HONumber=AcomDC_0608_2016-->