<properties
   pageTitle="Data Lake Store Java SDK を使用してアプリケーションを開発する | Azure"
   description="Data Lake Store Java SDK を使用してアプリケーションを開発する"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/10/2016"
   ms.author="nitinme"/>

# Java で Azure Data Lake Store の使用を開始する

> [AZURE.SELECTOR]
- [ポータル](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.JS](data-lake-store-manage-use-nodejs.md)

Azure Data Lake Store Java SDK を使用して、Azure Data Lake アカウントを作成し、フォルダーの作成、データ ファイルのアップロードとダウンロード、アカウントの削除などの基本操作を行う方法について説明します。Data Lake の詳細については、[Azure Data Lake Store](data-lake-store-overview.md) に関するページを参照してください。

## 前提条件

* Java Development Kit (JDK) 8 (Java バージョン 1.8 を使用)。
* IntelliJ または別の適切な Java 開発環境。この手順は省略可能ですが、実施することをお勧めします。以下の手順では、IntelliJ を使用します。
* **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* Data Lake Store のパブリック プレビューに対して、**Azure サブスクリプションを有効にする**。[手順](data-lake-store-get-started-portal.md#signup)を参照してください。
* **Azure Active Directory アプリケーションを作成する**。Azure Active Directory を使用した認証には、**対話型**と**非対話型**の 2 とおりの方法があります。どのように認証を行うかで前提条件は異なります。
	* **対話型の認証**を使用する場合: Azure Active Directory で**ネイティブ クライアント アプリケーション**を作成する必要があります。アプリケーションの作成が完了したら、アプリケーションに関連する次の値を取得します。
		- アプリケーションの**クライアント ID** と**リダイレクト URI** を取得します。
		- 委任されたアクセス許可を設定する

	* **対話型の認証**を使用する場合: Azure Active Directory で **Web アプリケーション**を作成する必要があります。アプリケーションの作成が完了したら、アプリケーションに関連する次の値を取得します。
		- アプリケーションの**クライアント ID**、**クライアント シークレット**、**リダイレクト URI** を取得します。
		- 委任されたアクセス許可を設定する
		- Azure Active Directory アプリケーションをロールに割り当てます。ロールは、Azure Active Directory アプリケーションにアクセス許可を付与するスコープのレベルにあります。たとえば、サブスクリプション レベルまたはリソース グループのレベルで、アプリケーションを割り当てることができます。 

	これらの値を取得したりアクセス許可を設定したりロールを割り当てたりする手順については、「[ポータルを利用し、Active Directory のアプリケーションとサービス プリンシパルを作成する](../resource-group-create-service-principal-portal.md)」を参照してください。

## Azure Active Directory を使用して認証する方法

次のコード スニペットは、アプリケーションが独自の資格情報を提供する、**非対話型**認証用のコードを提供します。

このチュートリアルが動作するには、アプリケーションに対して、Azure でリソースを作成するためのアクセス許可を付与する必要があります。このチュートリアルの目的においては、このアプリケーション Contributor に対して、Azure サブスクリプションの未使用で新しい空のリソース グループへのアクセス許可のみを付与することを**強くお勧めします**。

## Java アプリケーションを作成する

1. IntelliJ を開き、**コマンド ライン アプリ** テンプレートを使用して新しい Java プロジェクトを作成します。ウィザードを実行してプロジェクトを作成します。

2. 画面の左側にあるプロジェクトを右クリックし、**[フレームワーク サポートの追加]** をクリックします。**[Maven]** を選択し、**[OK]** をクリックします。

3. 新しく作成された **"pom.xml"** ファイルを開き、**</version>** タグと **</project>** タグの間に、次のテキストのスニペットを追加します。

    >[AZURE.NOTE] この手順は、Maven で Azure Data Lake Store SDK が利用可能になるまでの一時的なものです。この記事は、Maven で SDK が利用可能になると更新されます。この SDK に対する今後のすべての更新は、Maven を介して利用できます。

        <repositories>
        	<repository>
	            <id>adx-snapshots</id>
	            <name>Azure ADX Snapshots</name>
	            <url>http://adxsnapshots.azurewebsites.net/</url>
	            <layout>default</layout>
	            <snapshots>
                	<enabled>true</enabled>
            	</snapshots>
        	</repository>
        	<repository>
	            <id>oss-snapshots</id>
	            <name>Open Source Snapshots</name>
	            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
	            <layout>default</layout>
	            <snapshots>
	                <enabled>true</enabled>
	                <updatePolicy>always</updatePolicy>
	            </snapshots>
        	</repository>
    	</repositories>
    	<dependencies>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-client-authentication</artifactId>
	            <version>1.0.0-SNAPSHOT</version>
	        </dependency>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-mgmt-datalake-store</artifactId>
	            <version>1.0.0-SNAPSHOT</version>
	        </dependency>
    	</dependencies>


4. **[ファイル]** の **[設定]** を開き、**[ビルド]、[実行]、[デプロイ]** の順に移動します。**[Build Tools]**、**[Maven]**、**[インポート]** の順に展開します。**[Import Maven projects automatically]** (Maven プロジェクトを自動的にインポートする) チェック ボックスをオンにします。**[適用]** をクリックし、**[OK]** をクリックします。

5. 左側のウィンドウで、**src**、**main**、**java**、**<パッケージ名>** の順に移動し、**Main.java** を開いて、現状のコードを次のコードに置き換えます。また、**localFolderPath**、**\_adlsAccountName**、**\_resourceGroupName** などのコード スニペットで呼び出されたパラメーターの値を入力し、**CLIENT-ID**、**CLIENT-SECRET**、**TENANT-ID**、および **SUBSCRIPTION-ID** のプレースホルダーを置き換えます。

    このコードでは、Data Lake Store アカウントの作成、ストアでのファイルの作成、ファイルの転結、ファイルのダウンロード、最後にアカウントの削除のプロセスを行います。

        package com.company;
        
        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.*;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.util.ArrayList;
        import java.util.List;
        
        public class Main {
            private static String _adlsAccountName;
            private static String _resourceGroupName;
            private static String _location;
        
            private static String _tenantId;
            private static String _subId;
            private static String _clientId;
            private static String _clientSecret;
        
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
        
            public static void main(String[] args) throws Exception {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
                _resourceGroupName = "<RESOURCE-GROUP-NAME>";
                _location = "East US 2";
        
                _tenantId = "<TENANT-ID>";
                _subId =  "<SUBSCRIPTION-ID>";
                _clientId = "<CLIENT-ID>";
        
                _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
        
                String localFolderPath = "C:\\local_path\"; // TODO: Change this to any unused, new, empty folder on your local machine.
        
                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
                SetupClients(creds);
        
                // Create Data Lake Store account
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Displaying account(s).");
        
                // List Data Lake Store accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.getName());
                }
                WaitForNewline("Account(s) displayed.", "Creating files.");
        
                // Create two files in Data Lake Store: file1.csv and file2.csv
                CreateFile("/file1.csv", "123,abc", true);
                CreateFile("/file2.csv", "456,def", true);
                WaitForNewline("Files created.", "Concatenating files.");
        
                // Concatenate two files in Data Lake Store
                List<String> srcFilePaths = new ArrayList<String>();
                srcFilePaths.add("/file1.csv");
                srcFilePaths.add("/file2.csv");
                ConcatenateFiles(srcFilePaths, "/input.csv");
                WaitForNewline("Files concatenated.", "Downloading file.");
        
                // Download file from Data Lake Store
                DownloadFile("/input.csv", localFolderPath + "input.csv");
                WaitForNewline("File downloaded.", "Deleting file.");
        
                // Delete file from Data Lake Store
                DeleteFile("/input.csv");
                WaitForNewline("File deleted.", "Deleting account.");
        
                // Delete account
                DeleteAccount();
                WaitForNewline("Account deleted.", "DONE.");
            }
        
            //Set up clients
            public static void SetupClients(ServiceClientCredentials creds)
            {
                _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
        
                _adlsClient.setSubscriptionId(_subId);
            }
        
            // Helper function to show status and wait for user input
            public static void WaitForNewline(String reason, String nextAction)
            {
                if (nextAction == null)
                    nextAction = "";
                if (!nextAction.isEmpty())
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                    System.out.println(nextAction);
                }
                else
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                }
            }
        
            // Create Data Lake Store account
            public static void CreateAccount() throws InterruptedException, CloudException, IOException {
                DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
                adlsParameters.setLocation(_location);
        
                _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);
            }
        
            // Create file
            public static void CreateFile(String path) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path);
            }
        
            // Create file with contents
            public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
            }
        
            // Append to file
            public static void AppendToFile(String path, String contents) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().append(_adlsAccountName, path, bytesContents);
            }
        
            // Concatenate files
            public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().concat(_adlsAccountName, destFilePath, srcFilePaths);
            }
        
            // Delete concatenated file
            public static void DeleteFile(String filePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().delete(_adlsAccountName, filePath);
            }
        
            // Get file or directory info
            public static FileStatusProperties GetItemInfo(String path) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().getFileStatus(_adlsAccountName, path).getBody().getFileStatus();
            }
        
            // List files and directories
            public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().listFileStatus(_adlsAccountName, directoryPath).getBody().getFileStatuses().getFileStatus();
            }
        
            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
                InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(_adlsAccountName, srcPath).getBody();
        
                PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());
        
                String fileContents = "";
                if (stream != null) {
                    Writer writer = new StringWriter();
        
                    char[] buffer = new char[1024];
                    try {
                        Reader reader = new BufferedReader(
                                new InputStreamReader(stream, "UTF-8"));
                        int n;
                        while ((n = reader.read(buffer)) != -1) {
                            writer.write(buffer, 0, n);
                        }
                    } finally {
                        stream.close();
                    }
                    fileContents =  writer.toString();
                }
        
                pWriter.println(fileContents);
                pWriter.close();
            }
        
            // Delete account
            public static void DeleteAccount() throws InterruptedException, CloudException, IOException {
                _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
            }
        }


6. アプリケーションを実行します。指示に従って、アプリケーションを実行して完了します。

## 次のステップ

- [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
- [Data Lake Store で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0511_2016-->