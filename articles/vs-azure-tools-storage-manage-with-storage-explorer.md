---
title: ストレージ エクスプローラー (プレビュー) の概要 | Microsoft Docs
description: ストレージ エクスプローラー (プレビュー) を使用した Azure ストレージ リソースの管理
services: storage
documentationcenter: na
author: TomArcher
manager: douge
editor: ''

ms.service: storage
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: tarcher

---
# ストレージ エクスプローラー (プレビュー) の概要
## Overview
Microsoft Azure ストレージ エクスプローラー (プレビュー) は、Windows、OS X、Linux で Azure Storage データを容易に操作できるスタンドアロン アプリです。この記事では、Azure ストレージ アカウントへの接続と管理に関するさまざまな方法を紹介します。

![Microsoft Azure ストレージ エクスプローラー (プレビュー)][15]

## 前提条件
* [ストレージ エクスプローラー (プレビュー) をダウンロードしてインストールする](http://www.storageexplorer.com)

## ストレージ アカウントまたはサービスに接続する
ストレージ エクスプローラー (プレビュー) には、ストレージ アカウントへの多数の接続方法が用意されています。たとえば、Azure サブスクリプションに関連付けられているストレージ アカウントへの接続、他の Azure サブスクリプションから共有されているストレージ アカウントおよびサービスへの接続、Azure ストレージ エミュレーターを使ったローカル ストレージへの接続と管理などです。

* [Azure サブスクリプションに接続する](#connect-to-an-azure-subscription) - Azure サブスクリプションに属するストレージ リソースを管理します。
* [ローカル開発ストレージを操作する](#work-with-local-development-storage) - Azure ストレージ エミュレーターを使ってローカル ストレージを管理します。
* [外部ストレージにアタッチする](#attach-or-detach-an-external-storage-account) - ストレージ アカウントのアカウント名とキーを使って、別の Azure サブスクリプションに属するストレージ リソースを管理します。
* [SAS を使ってストレージ アカウントをアタッチする](#attach-storage-account-using-sas) - SAS を使って、別の Azure サブスクリプションに属するストレージ リソースを管理します。
* [SAS を使ってサービスをアタッチする](#attach-service-using-sas) - SAS を使って、別の Azure サブスクリプションに属する特定のストレージ サービス (BLOB コンテナー、キュー、またはテーブル) を管理します。

## Azure サブスクリプションに接続する
> [!NOTE]
> Azure アカウントを持っていない場合は、[無料試用版にサインアップする](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)か、[Visual Studio サブスクライバー特典を有効](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)にしてください。
> 
> 

1. ストレージ エクスプローラー (プレビュー) で、**[Azure アカウントの設定]** を選択します。
   
    ![Azure account settings][0]
2. 左側のウィンドウに、ログインしている Microsoft アカウントがすべて表示されます。別のアカウントに接続するには、**[アカウントの追加]** を選択し、ダイアログに従って、少なくとも 1 つのアクティブな Azure サブスクリプションと関連付けられている Microsoft アカウントでサインインします。
   
    ![アカウントの追加][1]
3. Microsoft アカウントでのサインインに成功すると、左側のウィンドウに、そのアカウントに関連付けられた Azure サブスクリプションが表示されます。操作する Azure サブスクリプションを選択してから、**[適用]** を選択します (**[すべてのサブスクリプション]** チェック ボックスをオンまたはオフにすることで、一覧の Azure サブスクリプションがすべて選択された状態と、1 つも選択されていない状態を切り替えることができます)。
   
    ![Select Azure subscriptions][3]
4. 左側のウィンドウに、選択した Azure サブスクリプションに関連付けられているストレージ アカウントが表示されます。
   
    ![Selected Azure subscriptions][4]

## ローカル開発ストレージを操作する
ストレージ エクスプローラー (プレビュー) では、Azure ストレージ エミュレーターを使ってローカル ストレージを操作することができます。そのため、Azure にデプロイされたストレージ アカウントがなくても、ストレージ関連のコードを記述したり、ストレージをテストしたりできます (ストレージ アカウントが Azure ストレージ エミュレーターによってエミュレートされるため)。

> [!NOTE]
> 現時点では、Azure ストレージ エミュレーターは Windows に対してのみサポートされています。
> 
> 

1. ストレージ エクスプローラー (プレビュー) の左側のウィンドウで、**[(Local and Attached) ((ローカルおよびアタッチ済み))]**、**[ストレージ アカウント]**、**[(開発)]** ノードの順に展開します。
   
    ![Local development node][21]
2. Azure ストレージ エミュレーターをインストールしていない場合は、情報バーを使用してインストールするよう求められます。情報バーが表示されている場合は、**[最新バージョンのダウンロード]** を選択し、エミュレーターをインストールします。
   
    ![Download Azure Storage Emulator prompt][22]
3. エミュレーターがインストールされたら、ローカルの BLOB、キュー、テーブルを作成および操作できるようになります。それぞれの種類のストレージ アカウントを操作する方法については、以下の該当するリンクを選択して確認してください。
   
   * [Azure Blob Storage リソースの管理](vs-azure-tools-storage-explorer-blobs.md)
   * Azure ファイル共有ストレージ リソースの管理 - "*近日公開予定*"
   * Azure Queue Storage リソースの管理 - "*近日公開予定*"
   * Azure Table Storage リソースの管理 - "*近日公開予定*"

## 外部ストレージ アカウントをアタッチまたはデタッチする
ストレージ エクスプローラー (プレビュー) には、ストレージ アカウントを簡単に共有できるように、外部ストレージ アカウントにアタッチする機能が用意されています。このセクションでは、外部ストレージ アカウントにアタッチ (または外部ストレージ アカウントからデタッチ) する方法について説明します。

### ストレージ アカウントの資格情報を取得する
外部ストレージ アカウントを共有するには、まずそのアカウントの所有者がアカウントの資格情報 (アカウント名とキー) を取得し、その情報を、その (外部) アカウントへのアタッチを望んでいるユーザーと共有する必要があります。ストレージ アカウントの資格情報は、Azure ポータルを使って次の手順で取得できます。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[参照]** を選択します。
3. **[ストレージ アカウント]** を選択します。
4. **[ストレージ アカウント]** ブレードで目的のストレージ アカウントを選択します。
5. 選択したストレージ アカウントの **[設定]** ブレードで、**[アクセス キー]** を選択します。
   
   ![Access Keys option][5]
6. **[アクセス キー]** ブレードで、ストレージ アカウントにアタッチするときに使用する、**[ストレージ アカウント名]** と **[キー 1]** の値をコピーします。
   
   ![アクセス キー][6]

### 外部ストレージ アカウントにアタッチする
外部ストレージ アカウントにアタッチするには、アカウントの名前とキーが必要になります。セクション「*ストレージ アカウントの資格情報を取得する*」に、Azure ポータルからこれらの値を取得する方法が記載されています。ただし、アカウント キーはポータルでは "キー 1" と呼ばれているため、ストレージ エクスプローラー (プレビュー) からアカウント キーを要求された場合は、"キー 1" の値を入力するか、貼り付けます。

1. ストレージ エクスプローラー (プレビュー) で、**[Connect to Azure storage (Azure ストレージに接続)]** を選択します。
   
   ![Connect to Azure storage option][23]
2. **[Connect to Azure storage (Azure ストレージに接続)]** ダイアログで、アカウント キー (Azure ポータルから取得した "キー 1" の値) を指定し、**[次へ]** を選択します。
   
   ![Connect to Azure storage dialog][24]
3. **[外部ストレージのアタッチ]** ダイアログで、**[アカウント名]** ボックスにストレージ アカウント名を入力し、必要な他の設定を指定してから、**[次へ]** を選択します。
   
   ![Attach external storage dialog][8]
4. **[Connection Summary (接続の概要)]** ダイアログで、情報を確認します。設定を変更する場合は、**[戻る]** を選択し、必要な設定をもう一度入力します。完了したら、**[接続]** を選択します。
5. 接続されると、ストレージ アカウント名に "**(外部)**" というテキストが追加された状態で外部ストレージ アカウントが表示されます。
   
   ![Result of connecting to an external storage account][9]

### 外部ストレージ アカウントからデタッチする
1. デタッチする外部ストレージ アカウントを右クリックし、コンテキスト メニューの **[デタッチ]** を選択します。
   
   ![Detach from storage option][10]
2. 確認のメッセージ ボックスが表示されたら、**[はい]** を選択して外部ストレージ アカウントからのデタッチを確定します。

## SAS を使ってストレージ アカウントをアタッチする
Azure サブスクリプションの管理者は、[SAS (Shared Access Signature)](storage/storage-dotnet-shared-access-signature-part-1.md) を使うことで、Azure サブスクリプションの資格情報を提供しなくてもストレージ アカウントへのアクセスを一時的に許可できます。

これをわかりやすく説明するために、Azure サブスクリプションの管理者である UserA が UserB に特定のアクセス許可を付与し、一時的にストレージ アカウントへのアクセスを許可する場面を考えてみます。

1. UserA は、目的のアクセス許可と一定の期間が設定された SAS (ストレージ アカウントへの接続文字列で構成) を生成します。
2. UserA は、ストレージ アカウントへのアクセスを希望しているユーザー (この例では UserB) と SAS を共有します。
3. UserB は、ストレージ エクスプローラー (プレビュー) で、提供された SAS を使って UserA に属するアカウントにアタッチします。

### 共有するアカウント用の SAS を取得する
1. ストレージ エクスプローラー (プレビュー) で、共有するストレージ アカウントを右クリックし、コンテキスト メニューの **[Get Shared Access Signature (Shared Access Signature の取得)]** を選択します。
   
   ![Get SAS context menu option][13]
2. **[Shared Access Signature]** ダイアログで、アカウントに対して期間とアクセス許可を指定し、**[作成]** を選択します。
   
    ![Get SAS dialog][14]
3. 2 つ目の **[Shared Access Signature]** ダイアログが表示され、SAS が示されます。**[接続文字列]** の横にある **[コピー]** を選択してクリップボードにコピーします。**[閉じる]** を選択してダイアログを閉じます。

### SAS を使って共有アカウントにアタッチする
1. ストレージ エクスプローラー (プレビュー) で、**[Connect to Azure storage (Azure ストレージに接続)]** を選択します。
   
   ![Connect to Azure storage option][23]
2. **[Connect to Azure storage (Azure ストレージに接続)]** ダイアログで、接続文字列を指定し、**[次へ]** を選択します。
   
   ![Connect to Azure storage dialog][24]
3. **[Connection Summary (接続の概要)]** ダイアログで、情報を確認します。設定を変更する場合は、**[戻る]** を選択し、必要な設定をもう一度入力します。完了したら、**[接続]** を選択します。
4. アタッチされると、指定したアカウント名に "(SAS)" というテキストが追加された状態でストレージ アカウントが表示されます。
   
   ![Result of attached to an account using SAS][17]

## SAS を使ってサービスをアタッチする
「[SAS を使ってストレージ アカウントをアタッチする](#attach-storage-account-using-sas)」セクションでは、Azure サブスクリプションの管理者がストレージ アカウントの SAS を生成 (および共有) してストレージ アカウントへの一時的なアクセスを許可する方法を示しています。同様に、SAS は、ストレージ アカウント内の特定のサービス (BLOB コンテナー、キュー、またはテーブル) に対して生成できます。

### 共有するサービスの SAS を生成する
この場合のサービスは、BLOB、コンテナー、キュー、またはテーブルです。次のセクションで、一覧にあるサービスの SAS を生成する方法を説明します。

* [BLOB コンテナーの SAS を取得する](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* ファイル共有の SAS を取得する - "*近日公開予定*"
* キューの SAS を取得する - "*近日公開予定*"
* テーブルの SAS を取得する - "*近日公開予定*"

### SAS を使って共有アカウント サービスにアタッチする
1. ストレージ エクスプローラー (プレビュー) で、**[Connect to Azure storage (Azure ストレージに接続)]** を選択します。
   
   ![Connect to Azure storage option][23]
2. **[Connect to Azure Storage (Azure ストレージに接続)]** ダイアログで SAS URI を指定し、**[次へ]** を選択します。
   
   ![Connect to Azure storage dialog][24]
3. **[Connection Summary (接続の概要)]** ダイアログで、情報を確認します。設定を変更する場合は、**[戻る]** を選択し、必要な設定をもう一度入力します。完了したら、**[接続]** を選択します。
4. アタッチされると、新たにアタッチされたサービスが **[(Service SAS) ((サービス SAS))]** ノードに表示されます。
   
   ![Result of attaching to a shared service using SAS][20]

## ストレージ アカウントを検索する
多数のストレージ アカウントがある場合は、左側のウィンドウの上部にある検索ボックスを使用すると、特定のストレージ アカウントをすばやく見つけることができます。

検索ボックスへの入力中、左側のウィンドウには、その時点で入力してある検索の値に一致するストレージ アカウントのみが表示されます。次のスクリーンショットは、ストレージ アカウント名に "tarcher" というテキストが含まれるすべてのストレージ アカウントを検索したときの例です。

![Storage account search][11]

検索をクリアするには、検索ボックスの **[x]** ボタンを選択します。

## 次のステップ
* [ストレージ エクスプローラー (プレビュー) を使用した Azure Blob Storage リソースの管理](vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png

<!---HONumber=AcomDC_0817_2016-->