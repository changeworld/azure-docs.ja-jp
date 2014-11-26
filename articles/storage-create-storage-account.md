<properties urlDisplayName="How to create" pageTitle="ストレージ アカウントの作成方法 | Azure" metaKeywords="" description="Learn how to create a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" solutions="" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />


<h1><a id="createstorageaccount"></a>ストレージ アカウントの作成方法</h1>

Azure の BLOB、テーブル、キュー、ファイルの各サービスでファイルとデータを保存するには、データを保存するリージョンにストレージ アカウントを作成する必要があります。このトピックでは、Azure の管理ポータルでストレージ アカウントを作成する方法について説明します。

ストレージ アカウントの容量とスループットの詳細については、「[Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット](http://msdn.microsoft.com/ja-jp/library/dn249410.aspx)」を参照してください。

> [WACOM.NOTE]Azure の仮想マシンの場合、デプロイ場所にまだストレージ アカウントがない状況では、その場所に自動的にストレージ アカウントが作成されます。ストレージ アカウント名は仮想マシン名を基にして付けられます。

##目次##

* [方法: ストレージ アカウントを作成する](#create)
* [次のステップ](#next)

<h2><a id="create"></a>方法: ストレージ アカウントを作成する</h2>

1. [管理ポータル](https://manage.windowsazure.com) にサインインします。

2. **[新規作成]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

	![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3. **[URL]** ボックスに、ストレージ アカウントの URL で使用するサブドメイン名を入力します。ストレージにあるオブジェクトにアクセスするには、オブジェクトの場所をエンドポイントに追加します。たとえば、BLOB にアクセスする URL は、http://*myaccount*.blob.core.windows.net/*mycontainer*/*myblob* のようになります。

4. **[リージョン/アフィニティ グループ]** で、ストレージのリージョンまたはアフィニティ グループを選択します。使用している他の Azure サービスと同じデータ センターにストレージ サービスを配置する場合は、リージョンの代わりにアフィニティ グループを選択します。これによりパフォーマンスが向上し、送信料金も発生しません。

	> [WACOM.NOTE]  アフィニティ グループを作成するには、管理ポータルの <b>[設定]</b> 領域を開き、<b>[アフィニティ グループ]</b> をクリックして、<b>[新しいアフィニティ グループの追加]</b> または <b>[追加]</b> をクリックします。また、Azure サービス管理 API を使用してアフィニティ グループを作成し管理することもできます。詳細については、「<a href="http://msdn.microsoft.com/ja-jp/library/windowsazure/ee460798.aspx">Operations on Affinity Groups (アフィニティ グループに対する操作)</a>」を参照してください。

5. 複数の Azure サブスクリプションを所有している場合は、**[サブスクリプション]** フィールドが表示されます。**[サブスクリプション]** ボックスに、ストレージ アカウントを使用する Azure サブスクリプションを入力します。1 つのサブスクリプションに最大 5 個のストレージ アカウントを作成できます。

6. **[レプリケーション]** で、ストレージ アカウントで使用するレプリケーションのレベルを選択します。

	[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

6. **[ストレージ アカウントの作成]** をクリックします。

	ストレージ アカウントを作成するには数分かかる場合があります。状態を確認するには、ポータルの下部にある通知を監視します。ストレージ アカウントが作成されると、新しいストレージ アカウントの状態が "**オンライン**" になって、使用できるようになります。 

	![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)

<h2><a id="next"></a>次のステップ</h2>

- Azure Storage の詳細については、[azure.com](http://azure.microsoft.com/ja-jp/documentation/services/storage/) および [MSDN](http://msdn.microsoft.com/ja-jp/library/gg433040.aspx) で Azure Storage のドキュメントを参照してください。 

- [Azure のストレージ チーム ブログ](http://blogs.msdn.com/b/windowsazurestorage/).

