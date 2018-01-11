---
title: "Azure Blob Storage と Visual Studio 接続済みサービスの概要 (ASP.NET Core) | Microsoft Docs"
description: "Visual Studio 接続済みサービスを使用してストレージ アカウントに接続した後、Visual Studio の ASP.NET Core プロジェクトで Azure Blob Storage の使用を開始する方法について説明します"
services: storage
documentationcenter: 
author: camsoper
manager: wpickett
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: casoper
ms.openlocfilehash: 889afa471eeb556662cddf8eb383a905f08b1f01
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Azure Blob ストレージと Visual Studio 接続済みサービスの概要 (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob Storage は、非構造化データをクラウド内にオブジェクト/BLOB として格納するサービスです。 Blob Storage は、ドキュメント、メディア ファイル、アプリケーション インストーラーなど、任意の種類のテキスト データやバイナリ データを格納できます。 Blob Storage は、オブジェクト ストレージとも呼ばれます。

このチュートリアルでは、Azure Blob Storage を使用していくつかの一般的なシナリオの ASP.NET Core コードを記述する方法を示します。 シナリオには、BLOB コンテナーの作成や、BLOB のアップロード、一覧表示、ダウンロード、および削除が含まれます。

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>前提条件

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

## <a name="set-up-the-development-environment"></a>開発環境を設定する

このセクションでは、ASP.NET MVC アプリの作成、接続済みサービスの接続の追加、コントローラーの追加、必要な名前空間ディレクティブの指定など、開発環境の設定方法について説明します。

### <a name="create-an-aspnet-mvc-app-project"></a>ASP.NET MVC アプリ プロジェクトを作成する

1. Visual Studio を開きます。

1. メイン メニューで、**[ファイル]、[新規作成]、[プロジェクト]** の順に選択します。

1. **[新しいプロジェクト]** ダイアログ ボックスで、次の図で強調表示されているようにオプションを指定します。

    ![ASP.NET Core プロジェクトの作成](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. **[OK]**を選択します。

1. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、次の図で強調表示されているようにオプションを指定します。

    ![MVC の指定](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

1. **[OK]**を選択します。

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>接続済みサービスを使用して Azure ストレージ アカウントに接続する

1. **ソリューション エクスプローラー**でプロジェクトを右クリックし、コンテキスト メニューで **[追加]、[接続済みサービス]** の順に選択します。

1. **[接続済みサービスの追加]** ダイアログで **[Azure Storage を使用したクラウド ストレージ]** を選択し、**[構成]** を選択します。

    ![[接続済みサービス] ダイアログ](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. **[Azure Storage]** ダイアログで、このチュートリアルで使用する Azure Storage アカウントを選択します。  新しい Azure Storage アカウントを作成するには、**[新しいストレージ アカウントの作成]** をクリックし、フォームに入力します。  既存のストレージ アカウントを選択するか新しいストレージを作成したら、**[追加]** をクリックします。  Visual Studio によって Azure Storage 用の NuGet パッケージと **appsettings.json** へのストレージ接続文字列がインストールされます。

> [!TIP]
> [Azure ポータル](https://portal.azure.com)でのストレージ アカウントの作成方法については、「[ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)」を参照してください。
>
> Azure ストレージ アカウントは、[Azure PowerShell](../storage/common/storage-powershell-guide-full.md)、[Azure CLI](../storage/common/storage-azure-cli.md)、または [Azure Cloud Shell](../cloud-shell/overview.md) を使用して作成することもできます。


### <a name="create-an-mvc-controller"></a>MVC コントローラーを作成する 

1. **ソリューション エクスプローラー**で**コントローラー**を右クリックし、コンテキスト メニューで **[追加]、[コントローラー]** の順に選択します。

    ![ASP.NET Core MVC アプリケーションへのコントローラーの追加](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. **[スキャフォールディングの追加]** ダイアログ ボックスで **[MVC コントローラー - 空]** を選択し、**[追加]** を選択します。

    ![MVC コントローラーの種類を指定する](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. **[コントローラーの追加]** ダイアログで、コントローラーに *BlobsController* という名前を付けて、**[追加]** を選択します。

    ![MVC コントローラー指定](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. 次の *using* ディレクティブを `BlobsController.cs` ファイルに追加します。

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>ストレージ アカウントに接続してコンテナー参照を取得する

BLOB コンテナーとは、BLOB とフォルダーの入れ子になった階層です。  このドキュメントの残りの手順では、コードを再利用できるように独自のメソッドに配置する必要があるため、BLOB コンテナーへの参照が必要です。

次の手順で、**appsettings.json** 内の接続文字列を使用してストレージ アカウントに接続するメソッドを作成し、コンテナーへの参照を作成します。  **appsettings.json** に設定する接続文字列は、`<storageaccountname>_AzureStorageConnectionString` の形式で名前が付けられます。 

1. `BlobsController.cs` ファイルを開きます。

1. **CloudBlobContainer** を返す **GetCloudBlobContainer** と呼ばれるメソッドを追加します。  必ず `<storageaccountname>_AzureStorageConnectionString` を **Web.config** 内の実際のキーの名前に置き換えてください。
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json");
        IConfigurationRoot Configuration = builder.Build();
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.Parse(Configuration["ConnectionStrings:aspnettutorial_AzureStorageConnectionString"]);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> *test-blob-container* はまだ存在していませんが、このコードによって参照が作成されるため、次の手順で示す `CreateIfNotExists` メソッドを使用してコンテナーを作成できます。

## <a name="create-a-blob-container"></a>BLOB コンテナーを作成する

次の手順では、BLOB コンテナーを作成する方法を説明します。

1. `ActionResult` を返す `CreateBlobContainer` と呼ばれるメソッドを追加します。

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. 目的の BLOB コンテナー名への参照を表す `CloudBlobContainer` オブジェクトを取得します。 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. `CloudBlobContainer.CreateIfNotExists` メソッドを呼び出して、まだ存在しない場合はコンテナーを作成します。 `CloudBlobContainer.CreateIfNotExists` メソッドでは、コンテナーが存在せず、正常に作成された場合は **true** が返されます。 それ以外の場合は、**false** が返されます。    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
    ```

1. `ViewBag` を BLOB コンテナーの名前に変更します。

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    完成した `CreateBlobContainer` メソッドを次に示します。

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを右クリックし、コンテキスト メニューで **[追加]、[新しいフォルダー]** の順に選択します。 新しいフォルダーに *Blobs* という名前を付けます。 

1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを展開し、**[BLOB]** を右クリックし、コンテキスト メニューで **[追加]、[ビュー]** の順に選択します。

1. **[ビューの追加]**  ダイアログで、ビューの名前として **CreateBlobContainer** と入力し、**[追加]** を選択します。

1. `CreateBlobContainer.cshtml` を開き、次のコード スニペットのように変更します。

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. **ソリューション エクスプローラー**で、**[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. `<ul class="nav navbar-nav">` のような順序なしリストを探します。  リストの最後の `<li>` 要素の後ろに、次の HTML を追加して別のナビゲーション メニュー項目を追加します。

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. アプリケーションを実行して **[BLOB コンテナーの作成]** を選択し、次のスクリーン ショットと同様の結果が表示されることを確認します。
  
    ![BLOB コンテナーを作成する](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    前述したように、`CloudBlobContainer.CreateIfNotExists` メソッドは、コンテナーが存在しないために作成された場合にのみ **true** を返します。 そのため、コンテナーが存在するときにアプリを実行した場合、メソッドは **false** を返します。

## <a name="upload-a-blob-into-a-blob-container"></a>BLOB コンテナーに BLOB をアップロードする

[BLOB コンテナーの作成](#create-a-blob-container)が終わったら、そのコンテナーにファイルをアップロードします。 このセクションでは、ローカル ファイルを BLOB コンテナーにアップロードする手順を説明します。 この手順では、*test-blob-container* という名前の BLOB コンテナーがあることを前提としています。 

1. `BlobsController.cs` ファイルを開きます。

1. 文字列を返す `UploadBlob` と呼ばれるメソッドを追加します。

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. `UploadBlob` メソッドの中で、目的の BLOB コンテナー名への参照を表す `CloudBlobContainer` オブジェクトを取得します。 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. 前述のように、Azure Storage ではさまざまな種類の BLOB がサポートされています。 このチュートリアルでは、 ブロック BLOB を使用します。  ブロック BLOB への参照を取得するには、`CloudBlobContainer.GetBlockBlobReference` メソッドを呼び出します。

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > BLOB 名は BLOB を取得するために使用される URL の一部であり、任意の文字列にすることができ、ファイルの名前も使用できます。

1. BLOB の参照を取得したら、BLOB 参照オブジェクトの `UploadFromStream` メソッドを呼び出して、データの任意のストリームを BLOB にアップロードします。 `UploadFromStream` メソッドは、BLOB が存在しない場合は作成し、存在する場合は上書きします  (*&lt;file-to-upload>* を、アップロードするファイルへの完全修飾パスに変更します)。

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
    }
    ```
    
    完成した `UploadBlob` メソッド (アップロードするファイルの完全修飾パス指定あり) を次に示します。

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. **ソリューション エクスプローラー**で、**[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. リストの最後の `<li>` 要素の後ろに、次の HTML を追加して別のナビゲーション メニュー項目を追加します。

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. アプリケーションを実行し、**[BLOB のアップロード]** を選択します。  "success!" という単語が 表示されます。
    
    ![成功の検証](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
「[BLOB コンテナー内の BLOB を一覧表示する](#list-the-blobs-in-a-blob-container)」セクションで、BLOB コンテナー内の BLOB を一覧表示する方法について説明します。    

## <a name="list-the-blobs-in-a-blob-container"></a>BLOB コンテナー内の BLOB を一覧表示する

このセクションでは、BLOB コンテナー内の BLOB を一覧表示する方法について説明します。 サンプル コードでは、セクション「[BLOB コンテナーを作成する](#create-a-blob-container)」で作成された *test-blob-container* を参照します。

1. `BlobsController.cs` ファイルを開きます。

1. `ActionResult` を返す `ListBlobs` と呼ばれるメソッドを追加します。

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. `ListBlobs` メソッドの中で、BLOB コンテナーへの参照を表す `CloudBlobContainer` オブジェクトを取得します。 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. コンテナー内の BLOB を一覧表示するには、`CloudBlobContainer.ListBlobsSegmentedAsync` メソッドを使用します。 `CloudBlobContainer.ListBlobsSegmentedAsync` メソッドは、`CloudBlockBlob`、`CloudPageBlob` または `CloudBlobDirectory` オブジェクトにキャストできる `IListBlobItem` オブジェクトを含む `BlobResultSegment` を返します。 次のコード スニペットでは、BLOB コンテナー内のすべての BLOB を列挙します。 各 BLOB がその型に基づいて適切なオブジェクトにキャストされ、その名前 (または、`CloudBlobDirectory` の場合は URI) が一覧に追加されます。

    ```csharp
    List<string> blobs = new List<string>();
    BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```
    完成した `ListBlobs` メソッドを次に示します。

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
        foreach (IListBlobItem item in resultSegment.Results)
        {
            if (item.GetType() == typeof(CloudBlockBlob))
            {
                CloudBlockBlob blob = (CloudBlockBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudPageBlob))
            {
                CloudPageBlob blob = (CloudPageBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudBlobDirectory))
            {
                CloudBlobDirectory dir = (CloudBlobDirectory)item;
                blobs.Add(dir.Uri.ToString());
            }
        }

        return View(blobs);
    }
    ```

1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを展開し、**[BLOB]** を右クリックし、コンテキスト メニューで **[追加]、[ビュー]** の順に選択します。

1. **[ビューの追加]** ダイアログ ボックスで、ビューの名前として「`ListBlobs`」と入力し、**[追加]** を選択します。

1. `ListBlobs.cshtml` を開き、内容を次のコードに置き換えます。

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
            <li>@item</li>
        }
    </ul>
    ```

1. **ソリューション エクスプローラー**で、**[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. リストの最後の `<li>` 要素の後ろに、次の HTML を追加して別のナビゲーション メニュー項目を追加します。

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. アプリケーションを実行して **[List blobs (BLOB の一覧表示)]** を選択し、次のスクリーン ショットと同様の結果が表示されることを確認します。
  
    ![BLOB の一覧](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>BLOB をダウンロードする

このセクションでは、BLOB をダウンロードし、ローカル ストレージに保存するか、または内容を文字列に読み込む方法を説明します。 サンプル コードでは、セクション「[BLOB コンテナーを作成する](#create-a-blob-container)」で作成された *test-blob-container* を参照します。

1. `BlobsController.cs` ファイルを開きます。

1. 文字列を返す `DownloadBlob` と呼ばれるメソッドを追加します。

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. `DownloadBlob` メソッドの中で、BLOB コンテナーへの参照を表す `CloudBlobContainer` オブジェクトを取得します。
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. `CloudBlobContainer.GetBlockBlobReference` メソッド呼び出して BLOB 参照オブジェクトを取得します。 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. BLOB をダウンロードするには、`CloudBlockBlob.DownloadToStream` メソッドを使用します。 次のコード は、ローカル ファイルに保存されるストリーム オブジェクトに BLOB の内容を転送します (*&lt;local-file-name>* を BLOB がダウンロードされる場所を表す完全修飾ファイル名に変更します)。 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    完成した `ListBlobs` メソッド (作成されるローカル ファイルの完全修飾パス指定あり) を次に示します。
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. **ソリューション エクスプローラー**で、**[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. リストの最後の `<li>` 要素の後ろに、次の HTML を追加して別のナビゲーション メニュー項目を追加します。

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. アプリケーションを実行し、**[BLOB のダウンロード]** を選択して、BLOB をダウンロードします。 `CloudBlobContainer.GetBlockBlobReference` メソッドの呼び出しで指定された BLOB が `File.OpenWrite` メソッドの呼び出しで指定された場所にダウンロードされます。  "success!" というテキストが ブラウザーに表示されます。 

## <a name="delete-blobs"></a>BLOB を削除する

次の手順では、BLOB を削除する方法を説明します。

1. `BlobsController.cs` ファイルを開きます。

1. 文字列を返す `DeleteBlob` と呼ばれるメソッドを追加します。

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. `DeleteBlob` メソッドの中で、BLOB コンテナーへの参照を表す `CloudBlobContainer` オブジェクトを取得します。
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. `CloudBlobContainer.GetBlockBlobReference` メソッド呼び出して BLOB 参照オブジェクトを取得します。 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. BLOB を削除するには、`Delete` メソッドを使用します。

    ```csharp
    blob.DeleteAsync().Wait();
    ```
    
    完成した `DeleteBlob` メソッドは次のようになります。
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. **ソリューション エクスプローラー**で、**[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. リストの最後の `<li>` 要素の後ろに、次の HTML を追加して別のナビゲーション メニュー項目を追加します。

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. アプリケーションを実行し、**[BLOB の削除]** を選択して、`CloudBlobContainer.GetBlockBlobReference` メソッドの呼び出しで指定された BLOB を削除します。  "success!" というテキストが ブラウザーに表示されます。  ブラウザーの **[戻る]** ボタンをクリックし、**[BLOB の一覧表示]** を選択して、削除した BLOB がコンテナーに存在しないことを確認します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ASP.NET Core を使用して、Azure Storage に対して BLOB を格納、一覧表示、および取得する方法について学習しました。  Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。

  * [テーブル ストレージと Visual Studio 接続済みサービスの概要 (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Azure キュー ストレージと Visual Studio 接続済みサービスの概要 (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
