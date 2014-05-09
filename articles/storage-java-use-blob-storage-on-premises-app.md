<properties linkid="dev-java-how-to-on-premise-application-with-blob-storage" urlDisplayName="イメージ ギャラリーとストレージ" pageTitle="BLOB ストレージを使用する内部設置型アプリケーション (Java) | Azure" metaKeywords="Azure BLOB ストレージ, Azure BLOB Java, Azure BLOB 例, Azure BLOB チュートリアル" description="画像を Azure にアップロードしてブラウザーに表示するコンソール アプリケーションの作成方法について説明します。コード サンプルは Java で記述されています。" metaCanonical="" services="storage" documentationCenter="Java" title="BLOB ストレージを使用する内部設置型アプリケーション" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />

# BLOB ストレージを使用する内部設置型アプリケーション

次の例では、Azure ストレージを使用して画像を Azure に保存する
方法を示しています。ここでは、画像を Azure に
アップロードし、その画像をブラウザーに表示する HTML ファイルを作成する
コンソール アプリケーションのコードを紹介します。

## 目次

-   [前提条件][]
-   [Azure BLOB ストレージを使用してファイルをアップロードするには][]
-   [コンテナーを削除するには][]

## <a name="bkmk_prerequisites"> </a>前提条件

1.  Java Developer Kit (JDK) v1.6 以降がインストールされていること。
2.  Azure SDK がインストールされていること。
3.  Azure Libraries for Java の JAR および該当する
    依存関係 JAR がインストールされ、Java コンパイラで使用される
    ビルド パスに存在すること。Azure Libraries for Java のインストールについては、「[Azure SDK for Java の
    ダウンロード]」を参照してください。
4.  Azure ストレージ アカウントがセットアップされていること。後に示すコードでは、
    ストレージ アカウントのアカウント名とアカウント キーが
    使用されます。ストレージ アカウントの作成については「[How to Create a Storage Account (ストレージ アカウントの作成方法)]」を、
    アカウント キーの取得については「[How to Manage Storage Accounts (ストレージ アカウントの管理方法)]」を
    参照してください。
5. ローカル画像ファイルが作成され、
    c:\myimages\image1.jpg として保存されていること。または、
    例に含まれている FileInputStream コンストラクターを変更して、別の
    画像パスおよびファイル名を使用することもできます。

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <a name="bkmk_uploadfile"> </a>Azure BLOB ストレージを使用してファイルをアップロードするには

ここでは詳細な手順を示していますが、この部分をスキップして、
後に示すコード全体を確認することもできます。

コードの先頭には、Azure コア ストレージ クラス、
Azure BLOB クライアント クラス、Java IO クラス、および 
URISyntaxException クラスの import を含めます。

    import com.microsoft.windowsazure.services.core.storage.;
    import com.microsoft.windowsazure.services.blob.client.;
    import java.io.;
    import java.net.URISyntaxException;

StorageSample という名前のクラスを宣言し、左ブラケットを含めます。
{.

    public class StorageSample {

**StorageSample** クラス内に、Azure ストレージ アカウントで指定されている
既定のエンドポイント プロトコル、ストレージ アカウント名、および
ストレージ アクセス キーを格納する文字列変数を
宣言します。プレースホルダーの **your\_account\_name** および 
**your\_account\_key** の値は、それぞれ自分のアカウント名およびアカウント キーに
置き換えてください。

    public static final String storageConnectionString = 
           "DefaultEndpointsProtocol=http;" + 
               "AccountName=your_account_name;" + 
               "AccountKey=your_account_name"; 

main の宣言を追加し、try ブロックと、
必要な左ブラケット { を含めます。

    public static void main(String[] args) 
    {
        try
        {

次の型の変数を宣言します (それぞれの説明は、この例でその変数がどのように
使用されるかを示しています)。

-   CloudStorageAccount: Azure ストレージ アカウント名およびキーを使用して
    アカウント オブジェクトを初期化し、BLOB クライアント オブジェクトを作成する
    ために使用します。
-   CloudBlobClient: BLOB サービスにアクセスするために使用します。
-   CloudBlobContainer: BLOB コンテナーの作成、コンテナー内の BLOB の
    リスト処理、およびコンテナーの削除を行うために使用します。
-   CloudBlockBlob: ローカルの画像ファイルをコンテナーに
    アップロードするために使用します。

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

account 変数に値を割り当てます。

    account = CloudStorageAccount.parse(storageConnectionString);

serviceClient 変数に値を割り当てます。

    serviceClient = account.createCloudBlobClient();

container 変数に値を割り当てます。gettingstarted という名前の
コンテナーへの参照を取得します。

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

コンテナーを作成します。このメソッドでは、コンテナーが存在しなければ作成し、
true を返します。コンテナーが存在する場合は、
false を返します。createIfNotExist の代わりに、create メソッドを
使用することもできます (コンテナーが既に存在する場合はエラーが返されます)。

    container.createIfNotExist();

Set anonymous access for the container.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

ブロック BLOB への参照を取得します。これは、Azure ストレージ内の 
BLOB を表します。

    blob = container.getBlockBlobReference("image1.jpg");

File コンストラクターを使用して、アップロードする (ローカルで作成済みの) 
ファイルへの参照を取得します (このファイルは、コードを実行する前に作成して
おいてください)。

    File fileReference = new File ("c:\\myimages\\image1.jpg");

CloudBlockBlob.upload メソッドの呼び出しを通じて、ローカル ファイルを
アップロードします。CloudBlockBlob.upload メソッドの 1 つ目のパラメーターは、
Azure ストレージにアップロードするローカル ファイルを表す 
FileInputStream オブジェクトです。2 つ目のパラメーターは、ファイルのサイズ 
(バイト単位) です。

    blob.upload(new FileInputStream(fileReference), fileReference.length());

MakeHTMLPage という名前のヘルパー関数を呼び出して、&lt;image&gt; 要素を 
1 つ含む基本的な HTML ページを作成します。画像のソースは、
Azure ストレージ アカウントにある BLOB に設定されます (MakeHTMLPage の
コードについては、後で説明します)。

    MakeHTMLPage(container);

ステータス メッセージと、作成された HTML ページに関する情報を出力します。

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

右ブラケット } を挿入して try ブロックを閉じます。

次の例外を処理します。

-   FileNotFoundException: FileInputStream コンストラクター
    または FileOutputStream コンストラクターからスローされる可能性があります。
-   StorageException: Azure クライアント ストレージ ライブラリから
    スローされる可能性があります。
-   URISyntaxException: ListBlobItem.getUri メソッドからスローされる
    可能性があります。
-   Exception: 汎用的な例外処理です。

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

右ブラケット } を挿入して main を閉じます。

基本的な HTML ページを作成する MakeHTMLPage という名前のメソッドを作成します。この
メソッドには CloudBlobContainer 型のパラメーターがあり、
アップロードされた BLOB のリストを反復処理するために使用されます。このメソッドは、
FileOutputStream コンストラクターからスローされる可能性のある 
FileNotFoundException 型の例外と、ListBlobItem.getUri メソッドから
スローされる可能性のある URISyntaxException 型の例外をスローします。左ブラケット 
{ を含めます。

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

index.html という名前のローカル ファイルを作成します。

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

このローカル ファイルへの書き込みにより、&lt;html&gt;、&lt;header&gt;、および &lt;body&gt; の各要素を追加します。

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

アップロードされた BLOB のリストに対して反復処理を行います。BLOB ごとに、HTML 
ページ内に &lt;img&gt; 要素を作成し、その src 属性を
Azure ストレージ アカウントに存在する BLOB の URI に設定します。
このサンプルで追加した画像は 1 つだけですが、複数の画像を追加した場合は、
このコードですべての画像が反復処理されます。

この例では、わかりやすくするために、アップロードされた各 BLOB が画像であると想定しています。画像以外の 
BLOB をアップロードした場合や、ブロック BLOB ではなくページ BLOB をアップロードした場合は、
必要に応じてコードを調整してください。

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

&lt;body&gt; 要素および &lt;html&gt; 要素を閉じます。

    stream.println("</body>");
    stream.println("</html>");

ローカル ファイルを閉じます。

    stream.close();

右ブラケット } を挿入して MakeHTMLPage を閉じます。

右ブラケット } を挿入して StorageSample を閉じます。

この例の完全なコードを次に示します。プレースホルダーの 
your\_account\_name および 
your\_account\_key の値は、それぞれ自分のアカウント名およびアカウント キーを
使用するように変更してください。

    import com.microsoft.windowsazure.services.core.storage.;
    import com.microsoft.windowsazure.services.blob.client.;
    import java.io.;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor 
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString = 
                "DefaultEndpointsProtocol=http;" + 
                   "AccountName=your_account_name;" + 
                   "AccountKey=your_account_name"; 

        public static void main(String[] args) 
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;
                
                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExist();
                
                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");
                File fileReference = new File ("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            }
            catch (FileNotFoundException fileNotFoundException)
            {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (URISyntaxException uriSyntaxException)
            {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

この例のコードでは、ローカルの画像ファイルを Azure ストレージにアップロード
するほか、index.html という名前のローカル ファイルも作成しています。このファイルを
ブラウザーで開くと、アップロードされた画像を表示できます。

コードにはアカウント名とアカウント キーが含まれるため、ソース コードは
安全に保管してください。

## <a name="bkmk_deletecontainer"> </a>コンテナーを削除するには

ストレージには課金されるため、この例を試した後、
gettingstarted コンテナーを削除することが望ましい場合が
あります。コンテナーを削除するには、CloudBlobContainer.delete 
メソッドを使用します。

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

CloudBlobContainer.delete メソッドを呼び出す場合も、
CloudStorageAccount、ClodBlobClient、
および CloudBlobContainer オブジェクトを初期化するプロセスは、
createIfNotExist メソッドの場合と同じです。gettingstarted という名前のコンテナーを削除する
完全なコード例を次に示します。

    import com.microsoft.windowsazure.services.core.storage.;
    import com.microsoft.windowsazure.services.blob.client.;

    public class DeleteContainer {

        public static final String storageConnectionString = 
                "DefaultEndpointsProtocol=http;" + 
                   "AccountName=your_account_name;" + 
                   "AccountKey=your_account_key"; 

        public static void main(String[] args) 
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                
                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();
                
                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

他の BLOB ストレージ クラスおよびメソッドの概要については、「[How to 
Use the Blob Storage Service from Java (Java から BLOB ストレージ サービスを使用する方法)]」を参照してください。

  [前提条件]: #bkmk_prerequisites
  [Azure BLOB ストレージを使用してファイルをアップロードするには]: #bkmk_uploadfile
  [コンテナーを削除するには]: #bkmk_deletecontainer
  [Azure SDK for Java のダウンロード]: http://www.windowsazure.com/ja-jp/develop/java/
  [ストレージ アカウントの作成方法]: http://www.windowsazure.com/ja-jp/manage/services/storage/how-to-create-a-storage-account/
  [How to Manage Storage Accounts (ストレージ アカウントの管理方法)]: http://www.windowsazure.com/ja-jp/manage/services/storage/how-to-manage-a-storage-account/
  [How to Use the Blob Storage Service from Java (Java から BLOB ストレージ サービスを使用する方法)]: http://www.windowsazure.com/ja-jp/develop/java/how-to-guides/blob-storage/

