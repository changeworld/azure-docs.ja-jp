<properties
    pageTitle="Azure ストレージ アカウントの一覧"
    description="Azure Toolkit for Eclipse を使用した、ストレージ アカウントの設定の管理"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/19/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn205108.aspx -->

# Azure ストレージ アカウントの一覧 #

Azure ストレージ アカウントは、JDK、アプリケーション サーバー、および任意のコンポーネントのためだけでなく、キャッシュを使用する際に、状態を格納するために使用されるダウンロードの場所を提供します。Eclipse では、Eclipse ワークスペース内のプロジェクトで使用可能な既知のストレージ アカウントの一覧が保持されます。Eclipse で一覧の管理に使用される **[ストレージ アカウント]** ダイアログ ボックスを開くには、 **[Window]** をクリックして **[Preferences]** をクリックし、**[Azure]** を展開して、 **[ストレージ アカウント]** をクリックします。

次に **[ストレージ アカウント]** ダイアログ ボックスを示します。

![][ic719496]

このダイアログ ボックスは、ストレージ アカウントを使用する次のようなダイアログ ボックスの **[アカウント]** リンクからも開くことができます。

* **[サーバー構成]** ダイアログ ボックスの **[JDK]** タブ。
* **[サーバー構成]** ダイアログ ボックスの **[サーバー]** タブ。
* **[Add Component]** ダイアログ ボックス。
* **[キャッシュ]** プロパティ ダイアログ ボックス。

## 発行設定ファイルを使用して、ストレージ アカウントをインポートするには ##

1. **[ストレージ アカウント]** ダイアログ ボックスで、**[Import from PUBLISH-SETTINGS file]** をクリックします。
2. (ローカル コンピューターへ既に発行設定ファイルを保存している場合、この手順はスキップしてください。) **[Import Subscription Information]** ダイアログ ボックスの **[Download PUBLISH-SETTINGS File]** をクリックします。まだ Azure アカウントにログインしていない場合は、ログインするように求められます。その後、Azure 発行設定ファイルを保存するよう求めるメッセージが表示されます。(結果としてログオン ページに表示される手順は無視することができます。それらは、Azure ポータルによって提供される、Visual Studio ユーザー向けの手順です。) ファイルをローカル コンピューターに保存します。
3. 引き続き **[Import Subscription Information]** ダイアログ ボックスで **[参照]** ボタンをクリックし、前にローカルへ保存した発行設定ファイルを選択して **[開く]** をクリックします。
4. **[OK]** をクリックして、**[Import Subscription Information]** ダイアログ ボックスを閉じます。

## 新しいストレージ アカウントを作成するには ##

1. **[ストレージ アカウント]** ダイアログ ボックスで、**[追加]** をクリックします。
2. **[ストレージ アカウントの追加]** ダイアログ ボックスで、**[新規]** をクリックします。
3. **[新規ストレージ アカウント]** ダイアログ ボックスで、次の値を指定します。
    * ストレージ アカウント名。
    * ストレージ アカウントの場所。
    * ストレージ アカウントの説明。
    * ストレージ アカウントが属しているサブスクリプション。
4. **[OK]** をクリックして、**[新規ストレージ アカウント]** ダイアログ ボックスを閉じます。

ストレージ アカウントの作成には数分かかる場合があります。作成後、**[OK]** をクリックして、**[ストレージ アカウントの追加]** ダイアログ ボックスを閉じると、使用可能なストレージ アカウントの一覧に新しいストレージ アカウントが追加されます。

## 既存のストレージ アカウントを一覧に追加するには ##

1. Azure ストレージ アカウントをまだ保有していない場合は、上述の「**新しいストレージ アカウントを作成するには**」セクションの手順に従って、ストレージ アカウントを作成します。(または、[Microsoft Azure 管理ポータル][]で新しいストレージ アカウントを作成できます。)
2. **[ストレージ アカウント]** ダイアログ ボックスで、**[追加]** をクリックします。
3. **[ストレージ アカウントの追加]** ダイアログ ボックスで、**[名前]** と **[アクセス キー]** の値を入力します。アカウント名とアクセス キーは、既存の Azure ストレージ アカウントのものである必要があります。[Microsoft Azure 管理ポータル][]の「**ストレージ**」セクションで、ストレージ アカウントの名称とキーを確認します。**[ストレージ アカウントの追加]** ダイアログ ボックスは、次のようになります。

    ![][ic719497]

4. **[OK]** をクリックして、**[ストレージ アカウントの追加]** ダイアログ ボックスを閉じます。

## 新しいアクセス キーを使用するためにストレージ アカウントを変更するには ##

1. **[ストレージ アカウント]** ダイアログ ボックスで、編集するストレージ アカウントをクリックし、**[編集]** をクリックします。
2. **[Edit Storage Account Access Key]** ダイアログ ボックスで、**[アクセス キー]** の値を変更します。
3. **[OK]** をクリックして、**[Edit Storage Account Access Key]** ダイアログ ボックスを閉じます。

## Eclipse で保持されている一覧からストレージ アカウントを削除するには ##

1. **[ストレージ アカウント]** ダイアログ ボックスで、編集するストレージ アカウントをクリックし、**[削除]** をクリックします。
2. ストレージ アカウントの削除を求めるメッセージが表示されたら、**[OK]** をクリックします。

>[AZURE.NOTE]**[ストレージ アカウント]** ダイアログ ボックスでのストレージ アカウントの削除は、Eclipse 内で表示できるストレージ アカウントの一覧からのみアカウントを削除します。ストレージ アカウントは、Azure サブスクリプションからは削除されません。また、ストレージ アカウントは、Eclipse でサブスクリプションの詳細を再読み込みした後に、再度表示される場合があります。

## 関連項目 ##

[Azure Toolkit for Eclipse][]

[Azure Toolkit for Eclipse のインストール][]

[Creating a Hello World Application for Azure in Eclipse (Eclipse での Azure 用 Hello World アプリケーションの作成)][]

Java での Azure の使用方法の詳細については、「[Azure Java Developer Center][]」を参照してください。

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Microsoft Azure 管理ポータル]: http://go.microsoft.com/fwlink/?LinkID=512959
[Creating a Hello World Application for Azure in Eclipse (Eclipse での Azure 用 Hello World アプリケーションの作成)]: http://go.microsoft.com/fwlink/?LinkID=699533
[Azure Toolkit for Eclipse のインストール]: http://go.microsoft.com/fwlink/?LinkId=699546
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic719496]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719496.png
[ic719497]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719497.png

<!---HONumber=AcomDC_1210_2015-->