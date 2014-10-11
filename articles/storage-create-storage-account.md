<properties linkid="manage-services-how-to-create-a-storage-account" urlDisplayName="How to create" pageTitle="How to create a storage account | Azure" metaKeywords="" description="Learn how to create a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" solutions="" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# <span id="createstorageaccount"></span></a>ストレージ アカウントの作成方法

Azure の BLOB、テーブル、キュー、およびファイル サービスでファイルとデータを保存するには、データを保存するリージョンにストレージ アカウントを作成する必要があります。このトピックでは、Azure 管理ポータルでストレージ アカウントを作成する方法について説明します。

ストレージ アカウントの容量とスループットの詳細については、「[Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット][]」を参照してください。

> [WACOM.NOTE] Azure の仮想マシンの場合、デプロイ場所にまだストレージ アカウントがない状況では、その場所に自動的にストレージ アカウントが作成されます。ストレージ アカウント名は仮想マシン名を基にして付けられます。

## 目次

-   [方法: ストレージ アカウントを作成する][]
-   [次のステップ][]

## <span id="create"></span></a>方法: ストレージ アカウントの作成

1.  [管理ポータル][]にサインインします。

2.  **[新規作成]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

    ![NewStorageAccount][]

3.  **[URL]** ボックスに、ストレージ アカウントの URL で使用するサブドメイン名を入力します。ストレージにあるオブジェクトにアクセスするには、オブジェクトの場所をエンドポイントに追加します。たとえば、BLOB にアクセスする URL は、http://*myaccount*.blob.core.windows.net/*mycontainer*/*myblob\* のようになります。

4.  **[リージョン/アフィニティ グループ]** で、ストレージのリージョンまたはアフィニティ グループを選択します。使用している他の Azure サービスと同じデータ センターにストレージ サービスを配置する場合は、リージョンの代わりにアフィニティ グループを選択します。これによりパフォーマンスが向上し、送信料金も発生しません。

    > [WACOM.NOTE] アフィニティ グループを作成するには、管理ポータルの **[設定]** 領域を開き、**[アフィニティ グループ]** をクリックして、**[アフィニティ グループの追加]** または **[追加]** をクリックします。また、Azure サービス管理 API を使用してアフィニティ グループを作成し管理することもできます。詳細については、「[アフィニティ グループに対する操作][]」を参照してください。

5.  複数の Azure サブスクリプションを所有している場合は、**[サブスクリプション]** フィールドが表示されます。**[サブスクリプション]** ボックスに、ストレージ アカウントを使用する Azure サブスクリプションを入力します。1 つのサブスクリプションに最大 5 個のストレージ アカウントを作成できます。

6.  **[レプリケーション]** で、ストレージ アカウントで使用するレプリケーションのレベルを選択します。

    [WACOM.INCLUDE [storage-replication-options][]]

7.  **[ストレージ アカウントの作成]** をクリックします。

    ストレージ アカウントを作成するには数分かかる場合があります。状態を確認するには、ポータルの下部にある通知を監視します。ストレージ アカウントが作成されると、新しいストレージ アカウントの状態が "**オンライン**" になって、使用できるようになります。

    ![StoragePage][]

## <span id="next"></span></a>次のステップ

-   Azure Storage の詳細については、[azure.com][] および [MSDN][] で Azure Storage のドキュメントを参照してください。

-   [Azure のストレージ チーム ブログ][]を参照してください。

  [Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット]: http://msdn.microsoft.com/en-us/library/dn249410.aspx
  [方法: ストレージ アカウントを作成する]: #create
  [次のステップ]: #next
  [管理ポータル]: https://manage.windowsazure.com
  [NewStorageAccount]: ./media/storage-create-storage-account/storage_NewStorageAccount.png
  [アフィニティ グループに対する操作]: http://msdn.microsoft.com/en-us/library/windowsazure/ee460798.aspx
  [storage-replication-options]: ../includes/storage-replication-options.md
  [StoragePage]: ./media/storage-create-storage-account/Storage_StoragePage.png
  [azure.com]: http://azure.microsoft.com/en-us/documentation/services/storage/
  [MSDN]: http://msdn.microsoft.com/en-us/library/gg433040.aspx
  [Azure のストレージ チーム ブログ]: http://blogs.msdn.com/b/windowsazurestorage/
