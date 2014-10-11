<properties linkid="manage-services-storage-net-shared-access-signature-part-2" urlDisplayName="" pageTitle="Create and use a SAS with the Blob Service | Microsoft Azure" metaKeywords="Azure blob, shared access signatures, stored access policy" description="Explore generating and using shared access signatures with the Blob service" metaCanonical="" services="storage" documentationCenter="" title="Part 2: Create and Use a SAS with the Blob Service" solutions="" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# 共有アクセス署名、パート 2:BLOB サービスによる SAS の作成および使用

このチュートリアルの[第 1 部][] では、共有アクセス署名 (SAS) を紹介し、そのベスト プラクティスについて説明しました。第 2 部では、Azure BLOB サービスで共有アクセス署名を生成し、使用する方法を説明します。例は C# で記述され、Azure .NET 用ストレージ クライアント ライブラリを利用しています。説明しているシナリオには、共有アクセス署名を使用する場合の次の側面が含まれます。

-   コンテナーでの共有アクセス署名の生成
-   BLOB での共有アクセス署名の生成
-   保存されているアクセス ポリシーの作成と、コンテナーのリソースでの署名の管理
-   クライアント アプリケーションからの共有アクセス署名のテスト

# このチュートリアルについて

このチュートリアルでは、2 種類のコンソール アプリケーションを作成して、コンテナーおよび BLOB の共有アクセス署名を作成する方法について特に説明します。最初のコンソール アプリケーションで、コンテナーと BLOB に共有アクセス署名が生成されます。 このアプリケーションは、ストレージ アカウント キーを認識しています。2 番目のコンソール アプリケーションはクライアント アプリケーションとして機能するアプリケーションであり、最初のアプリケーションが作成した共有アクセス署名を使って、コンテナー リソースと BLOB リソースにアクセスします。このアプリケーションは、コンテナー リソースと BLOB リソースへのアクセスを認証するためにだけ、共有アクセス署名を使用します。アカウント キーは認識していません。

# パート 1: コンソール アプリケーションの作成と共有アクセス署名の生成

まず、Azure .NET 用ストレージ クライアント ライブラリがインストールされていることを確認します。クライアント ライブラリの最新アセンブリを含む [NuGet パッケージ][]をインストールできます。最新の修正プログラムが確実に含まれるように、この方法をお勧めします。また、[Azure SDK for .NET][] の最新バージョンの一部として、クライアント ライブラリをダウンロードすることもできます。

Visual Studio で、新しい Windows コンソール アプリケーションを作成し、**GenerateSharedAccessSignatures** という名前を付けます。次の方法のいずれかを使用して、**Microsoft.WindowsAzure.Configuration.dll** および **Microsoft.WindowsAzure.Storage.dll** への参照を追加します。

-   NuGet パッケージをインストールする場合は、まず、[Visual Studio 用 NuGet パッケージ マネージャー拡張機能][]をインストールします。Visual Studio で、**[プロジェクト]、[NuGet パッケージの管理]** の順に選択し、オンラインで "**Azure のストレージ**" を検索して、見つかったインストール手順に従います。
-   または、インストールした Azure SDK でアセンブリを探し、そのアセンブリへの参照を追加することもできます。

Program.cs ファイルの先頭に、次の **using** ステートメントを追加します。

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

自分のストレージ アカウントを指定する接続文字列を含む構成設定が含まれるように、app.config ファイルを編集します。編集した app.config ファイルは、次のようになります。

    <configuration>
      <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings> 
    </configuration>

## コンテナーの共有アクセス署名 URI の生成

最初に、新しいコンテナーで共有アクセス署名を生成するメソッドを追加します。この場合、署名は保存されたアクセス ポリシーには関連付けられないため、その有効期限および付与するアクセス許可を示す情報が URI に含まれます。

まず、ストレージ アカウントへのアクセスを認証し、新しいコンテナーを作成するために、次のコードを **Main()** メソッドに追加します。

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
        
        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();
        
        //Insert calls to the methods created below here...
        
        //Require user input before closing the console window.
        Console.ReadLine();
    }

次に、コンテナーの共有アクセス署名を生成し、署名 URI を返す新しいメソッドを追加します。

    static string GetContainerSasUri(CloudBlobContainer container)
    {
        //Set the expiry time and permissions for the container.
        //In this case no start time is specified, so the shared access signature becomes valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;
        
        //Generate the shared access signature on the container, setting the constraints directly on the signature.
        string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
        
        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

**Main()** メソッドの末尾で、**Console.ReadLine()** の呼び出しの前に次の行を追加します。これで、**GetContainerSasUri()** を呼び出し、署名 URI をコンソール ウィンドウに書き込みます。

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

コンパイルし、実行して、新しいコンテナーの共有アクセス署名 URI を出力します。URI は、次の URI のようになります。

<https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D>

コードを実行すると、コンテナーで作成した共有アクセス署名が 4 時間だけ有効になります。署名によってクライアントには、コンテナー内の BLOB の一覧を表示し、新しい BLOB をコンテナーに書き込むアクセス許可が付与されます。

## BLOB の共有アクセス署名 URI の生成

次に、同様のコードを作成して、新しい BLOB をコンテナー内に作成し、その共有アクセス署名を生成します。この共有アクセス署名は、保存されているアクセス ポリシーに関連付けられておらず、開始時刻、有効期限、およびアクセス許可情報が URI に含まれています。

新しい BLOB を作成してテキストを書き込んだ後に共有アクセス署名を生成し、署名 URI を返す新しいメソッドを追加します。

    static string GetBlobSasUri(CloudBlobContainer container)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");
        
        //Upload text to the blob. If the blob does not yet exist, it will be created. 
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }
        
        //Set the expiry time and permissions for the blob.
        //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
        //The shared access signature will be valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
        
        //Generate the shared access signature on the blob, setting the constraints directly on the signature.
        string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);
        
        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

**Main()** メソッドの末尾で、**Console.ReadLine()** の呼び出しの前に、**GetBlobSasUri()** を呼び出す次の行を追加し、共有アクセス署名 URI をコンソール ウィンドウに書き込みます。

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();

コンパイルし、実行して、新しい BLOB の共有アクセス署名 URI を出力します。URI は、次の URI のようになります。

<https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D>

## 保存されているアクセス ポリシーのコンテナーでの作成

今度は、保存されているアクセス ポリシーをコンテナーで作成します。これで、関連付けられているすべての共有アクセス署名の制約が定義されます。

前の例で、共有アクセス署名 URI 自体の開始時刻 (暗黙の、または明示的な)、有効期限、およびアクセス許可を指定しました。次の例では、これらを、共有アクセス署名ではなく、保存されているアクセス ポリシーで指定します。こうすると、共有アクセス署名を再発行せずに、これらの制約を変更できます。

共有アクセス署名に 1 つ以上の制約が、保存されているアクセス ポリシーに残りの制約が指定されている可能性があることに注意してください。ただし、開始時刻、有効期限、およびアクセス許可はどちらか一方にだけ指定できます。たとえば、アクセス許可を共有アクセス署名に指定し、保存されているアクセス ポリシーにも指定することはできません。

Add a new method that creates 新しい保存されているアクセス ポリシーを作成し、そのポリシーの名前を返す新しいメソッドを、次のように追加します。

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container, string policyName)
    {
        //Create a new stored access policy and define its constraints.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };
        
        //Get the container's existing permissions.
        BlobContainerPermissions permissions = new BlobContainerPermissions();
        
        //Add the new policy to the container's permissions.
        permissions.SharedAccessPolicies.Clear();
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
    }

**Main()** メソッドの末尾で、**Console.ReadLine()** の呼び出しの前に次の行を追加します。これで、**CreateSharedAccessPolicy()** メソッドを呼び出します。

    //Create an access policy on the container, which may be optionally used to provide constraints for 
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

## アクセス ポリシーを使用するコンテナーでの共有アクセス署名 URI の生成

次に、先に作成したコンテナーで別の共有アクセス署名を作成しますが、今度は、前の例で作成したアクセス ポリシーに署名を関連付けます。

コンテナーで別の共有アクセス署名を生成する新しいメソッドを追加します。

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Generate the shared access signature on the container. In this case, all of the constraints for the 
        //shared access signature are specified on the stored access policy.
        string sasContainerToken = container.GetSharedAccessSignature(null, policyName);
        
        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

**Main()** メソッドの末尾で、**Console.ReadLine()** の呼び出しの前に次の行を追加します。これで、**GetContainerSasUriWithPolicy** メソッドを呼び出します。

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

## アクセス ポリシーを使用する BLOB での共有アクセス署名 URI の生成

最後に、同様のメソッドを追加することで、別の BLOB を作成し、アクセス ポリシーに関連付けられた共有アクセス署名を生成します。

BLOB を作成し、共有アクセス署名を生成する新しいメソッドを追加します。

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");
        
        //Upload text to the blob. If the blob does not yet exist, it will be created. 
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a shared access signature. " + 
        "A stored access policy defines the constraints for the signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }
        
        //Generate the shared access signature on the blob.
        string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);
        
        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

**Main()** メソッドの末尾で、**Console.ReadLine()** の呼び出しの前に次の行を追加します。これで、**GetBlobSasUriWithPolicy** メソッドを呼び出します。

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

**Main()** メソッドは、全体として、次のようになります。これを実行して、共有アクセス署名 URI をコンソール ウィンドウに書き込み、それをコピーしてテキスト ファイルに貼り付けます。これは、このチュートリアルの第 2 部で使用します。

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
        
        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();
        
        //Generate a SAS URI for the container, without a stored access policy.
        Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
        Console.WriteLine();
        
        //Generate a SAS URI for a blob within the container, without a stored access policy.
        Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
        Console.WriteLine();
        
        //Create an access policy on the container, which may be optionally used to provide constraints for 
        //shared access signatures on the container and the blob.
        string sharedAccessPolicyName = "tutorialpolicy";
        CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
        
        //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();
        
        //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();
        
        Console.ReadLine();
    }

GenerateSharedAccessSignatures コンソール アプリケーションを実行すると、コンソール ウィンドウに次のような出力が表示されます。これが、チュートリアルの第 2 部で使用する共有アクセス署名です。

![sas-console-output-1][]

# パート 2: コンソール アプリケーションの作成と共有アクセス署名のテスト

前の例で作成した共有アクセス署名をテストするために、署名を使用して、コンテナーと BLOB で操作を実行する、もう 1 つのコンソール アプリケーションを作成します。

チュートリアルの第 1 部の完了後、4 時間を超える時間が経過している場合、有効期限を 4 時間に設定して生成した署名は無効になっています。同様に、保存されているアクセス ポリシーに関連付けられた署名は、10 時間を経過すると、無効になります。これらの期間のいずれか、または両方が経過している場合は、最初のコンソール アプリケーションでコードを実行して、チュートリアルの第 2 部で使用するための新しい共有アクセス署名を生成する必要があります。

Visual Studio で、新しい Windows コンソール アプリケーションを作成し、**ConsumeSharedAccessSignatures** という名前を付けます。前に追加した場合と同様に、**Microsoft.WindowsAzure.Configuration.dll** および **Microsoft.WindowsAzure.Storage.dll** への参照を追加します。

Program.cs ファイルの先頭に、次の **using** ステートメントを追加します。

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

**Main()** メソッドの本文で、次の定数を追加し、その値を、チュートリアルのパート 1 で生成した共有アクセス署名に更新します。

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }

## メソッドの追加と、共有アクセス署名を使用するコンテナー操作の実行

次に、コンテナーで共有アクセス署名を使用する代表的なコンテナー操作をいくつかテストするメソッドを追加します。共有アクセス署名は、署名だけに基づいてコンテナーへのアクセスを認証し、コンテナーに参照を返すために使用されることに注意してください。

次のメソッドを Program.cs に追加します。

    static void UseContainerSAS(string sas)
    {
        //Try performing container operations with the SAS provided.

        //Return a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Create a list to store blob URIs returned by a listing operation on the container.
        List<Uri> blobUris = new List<Uri>();

        try
        {
            //Write operation: write a new blob to the container. 
            CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
            string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        try
        {
            //List operation: List the blobs in the container, including the one just added.
            foreach (ICloudBlob blobListing in container.ListBlobs())
            {
                blobUris.Add(blobListing.Uri);
            }
            Console.WriteLine("List operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("List operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        try
        {
            //Read operation: Get a reference to one of the blobs in the container and read it. 
            CloudBlockBlob blob = container.GetBlockBlobReference(blobUris[0].ToString());
            MemoryStream msRead = new MemoryStream();
            msRead.Position = 0;
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                Console.WriteLine(msRead.Length);
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        Console.WriteLine();

        try
        {
            //Delete operation: Delete a blob in the container.
            CloudBlockBlob blob = container.GetBlockBlobReference(blobUris[0].ToString());
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }

**Main()** メソッドを更新して、コンテナーで作成した両方の共有アクセス署名で **UseContainerSAS()** を呼び出します。

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy); 
        
        //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
    }

## メソッドの追加と、共有アクセス署名を使用する BLOB 操作の実行

最後に、BLOB で共有アクセス署名を使用する代表的な BLOB 操作をいくつかテストするメソッドを追加します。ここでは、コンストラクター **CloudBlockBlob(String)** を使用して共有アクセス署名で渡し、BLOB に参照を返します。他の認証は不要で、署名だけに基づいて認証されます。

次のメソッドを Program.cs に追加します。

    static void UseBlobSAS(string sas)
    {
        //Try performing blob operations using the SAS provided.

        //Return a reference to the blob using the SAS URI.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        try
        {
            //Write operation: write a new blob to the container. 
            string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        try
        {
            //Read operation: Read the contents of the blob.
            MemoryStream msRead = new MemoryStream();
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                msRead.Position = 0;
                using (StreamReader reader = new StreamReader(msRead, true))
                {
                    string line;
                    while ((line = reader.ReadLine()) != null)
                    {
                        Console.WriteLine(line);
                    }
                }
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        try
        {
            //Delete operation: Delete the blob.
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }

**Main()** メソッドを更新して、BLOB で作成した両方の共有アクセス署名で **UseBlobSAS()** を呼び出します。

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy); 
        
        //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
    }

コンソール アプリケーションを実行し、出力で、どの署名に対してどの操作が許可されているかを確認します。コンソール ウィンドウでの出力は、次のようになります。

![sas-console-output-2][]

# 次のステップ

[共有アクセス署名、パート 1:SAS モデルについて][第 1 部]

[Microsoft Azure ストレージ リソースへのアクセスの管理][]

[共有アクセス署名によるアクセスの委任 (REST API) に関するページ][]

[テーブルおよびキュー SAS についての MSDN ブログ][]

  [第 1 部]: ../storage-dotnet-shared-access-signature-part-1/
  [NuGet パッケージ]: http://nuget.org/packages/WindowsAzure.Storage/ "NuGet package"
  [Azure SDK for .NET]: http://www.windowsazure.com/en-us/downloads/
  [Visual Studio 用 NuGet パッケージ マネージャー拡張機能]: http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
  [sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
  [sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG
  [Microsoft Azure ストレージ リソースへのアクセスの管理]: http://msdn.microsoft.com/en-us/library/windowsazure/ee393343.aspx
  [共有アクセス署名によるアクセスの委任 (REST API) に関するページ]: http://msdn.microsoft.com/en-us/library/windowsazure/ee395415.aspx
  [テーブルおよびキュー SAS についての MSDN ブログ]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx
