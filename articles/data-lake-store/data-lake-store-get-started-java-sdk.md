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
   ms.date="08/18/2016"
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

## Azure Data Lake Store Java SDK

以下に示したのは、Java SDK for Data Lake Store および Java SDK リファレンスのダウンロード場所のリンクです。このチュートリアルを読むにあたって SDK をダウンロードする必要はなく、リファレンス ドキュメントを読む必要もありません。これらのリンクはあくまで参考としてご利用ください。

* Java SDK for Data Lake Store のソース コードは [GitHub](https://github.com/Azure/azure-sdk-for-java) で入手できます。
* Data Lake Store 用 Java SDK リファレンスは、[https://azure.github.io/azure-sdk-for-java/](https://azure.github.io/azure-sdk-for-java/) でご覧いただけます。

## 前提条件

* Java Development Kit (JDK) 8 (Java バージョン 1.8 を使用)。
* IntelliJ または別の適切な Java 開発環境。この手順は省略可能ですが、実施することをお勧めします。以下の手順では、IntelliJ を使用します。
* **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* Data Lake Store パブリック プレビューに対して、**Azure サブスクリプションを有効にする**。[手順](data-lake-store-get-started-portal.md#signup)を参照してください。
* **Azure Active Directory アプリケーションを作成する**Azure Active Directory を使用した認証には、**対話型**と**非対話型**の 2 とおりの方法があります。どのように認証を行うかで前提条件は異なります。
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

このチュートリアルを利用するには、アプリケーションに対して、Azure でリソースを作成するためのアクセス許可を付与する必要があります。このチュートリアルの目的においては、このアプリケーションの共同作成者に対して、Azure サブスクリプション内の未使用で新しい空のリソース グループへのアクセス許可のみを付与することを**強くお勧めします**。

## Java アプリケーションの作成

1. IntelliJ を開き、**コマンド ライン アプリ** テンプレートを使用して新しい Java プロジェクトを作成します。ウィザードを実行してプロジェクトを作成します。

2. **[File (ファイル)]**、**[Project Structure (プロジェクトの構造)]**、**[Modules (モジュール)]** ([Project Settings (プロジェクト設定)] の下)、**[Dependencies (依存関係)]**、**[+]**、**[Library (ライブラリ)]**、**[From Maven (Maven から)]** の順に開きます。

3. 次の Maven パッケージを検索して、プロジェクトに追加します。

    * com.microsoft.azure:azure-mgmt-datalake-store:1.0.0-beta1.2
    * com.microsoft.azure:azure-mgmt-datalake-store-uploader:1.0.0-beta1.2
    * com.microsoft.azure:azure-client-authentication:1.0.0-beta2

4. 左側のウィンドウで、**src**、**main**、**java**、**<パッケージ名>** の順に移動し、**Main.java** を開いて、既存のコード ブロックを次のコードで置き換えます。また、コード スニペットで呼び出された **localFolderPath**、**DATA-LAKE-STORE-NAME**、**RESOURCE-GROUP-NAME** などのパラメーターの値を入力し、自分のサブスクリプションと Azure Active Directory の情報で **CLIENT-ID**、**CLIENT-SECRET**、**TENANT-ID**、**SUBSCRIPTION-ID** のプレースホルダーを置き換えます。この情報を見つける方法については、[サービス プリンシパルの作成に関する Azure のガイド](../resource-group-authenticate-service-principal.md)を参照してください。

    このコードでは、Data Lake Store アカウントの作成、ストアでのファイルの作成、ファイルの転結、ファイルのダウンロード、最後にアカウントの削除のプロセスを行います。

        package com.company;

        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.implementation.DataLakeStoreAccountManagementClientImpl;
        import com.microsoft.azure.management.datalake.store.implementation.DataLakeStoreFileSystemManagementClientImpl;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.azure.management.datalake.store.uploader.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.util.ArrayList;
        import java.util.List;

        public class Main {
            final static String ADLS_ACCOUNT_NAME = <DATA-LAKE-STORE-NAME>;
            final static String RESOURCE_GROUP_NAME = "<RESOURCE-GROUP-NAME>";
            final static String LOCATION = "East US 2";
            final static String TENANT_ID = "<TENANT-ID>";
            final static String SUBSCRIPTION_ID =  "<SUBSCRIPTION-ID>";
            final static String CLIENT_ID = "<CLIENT-ID>";
            final static String CLIENT_SECRET = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.

            private static DataLakeStoreAccountManagementClientImpl _adlsClient;
            private static DataLakeStoreFileSystemManagementClientImpl _adlsFileSystemClient;

            public static void main(String[] args) throws Exception {
                String localFolderPath = "C:\\local_path\"; // TODO: Change this to any unused, new, empty folder on your local machine.

                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(CLIENT_ID, TENANT_ID, CLIENT_SECRET, null);
                SetupClients(creds);

                // Create Data Lake Store account
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Displaying account(s).");

                // List Data Lake Store accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", SUBSCRIPTION_ID));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.accounts().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.name());
                }
                WaitForNewline("Account(s) displayed.", "Uploading file.");

                // Upload a file to Data Lake Store: file1.csv
                UploadFile(localFolderPath + "file1.csv", "/file1.csv");
                WaitForNewline("File uploaded.", "Appending newline.");

                // Append newline to file1.csv
                AppendToFile("/file1.csv", "\r\n");
                WaitForNewline("Newline appended.", "Creating file.");

                // Create a new file in Data Lake Store: file2.csv
                CreateFile("/file2.csv", "456,def", true);
                WaitForNewline("File created.", "Concatenating files.");

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
                _adlsClient.withSubscriptionId(SUBSCRIPTION_ID);
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
                adlsParameters.withLocation(LOCATION);

                _adlsClient.accounts().create(RESOURCE_GROUP_NAME, ADLS_ACCOUNT_NAME, adlsParameters);
            }

            // Create file
            public static void CreateFile(String path) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().create(ADLS_ACCOUNT_NAME, path);
            }

            // Create file with contents
            public static void CreateFile(String path, String contents, boolean force) throws IOException, AdlsErrorException {
                byte[] bytesContents = contents.getBytes();

                _adlsFileSystemClient.fileSystems().create(ADLS_ACCOUNT_NAME, path, bytesContents, force);
            }

            // Append to file
            public static void AppendToFile(String path, String contents) throws IOException, AdlsErrorException {
                byte[] bytesContents = contents.getBytes();

                _adlsFileSystemClient.fileSystems().append(ADLS_ACCOUNT_NAME, path, bytesContents);
            }

            // Concatenate files
            public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().concat(ADLS_ACCOUNT_NAME, destFilePath, srcFilePaths);
            }

            // Delete concatenated file
            public static void DeleteFile(String filePath) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().delete(ADLS_ACCOUNT_NAME, filePath);
            }

            // Get file or directory info
            public static FileStatusProperties GetItemInfo(String path) throws IOException, AdlsErrorException {
                return _adlsFileSystemClient.fileSystems().getFileStatus(ADLS_ACCOUNT_NAME, path).getBody().fileStatus();
            }

            // List files and directories
            public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, AdlsErrorException {
                return _adlsFileSystemClient.fileSystems().listFileStatus(ADLS_ACCOUNT_NAME, directoryPath).getBody().fileStatuses().fileStatus();
            }

            // Upload file
            public static void UploadFile(String srcPath, String destPath) throws Exception {
                UploadParameters parameters = new UploadParameters(srcPath, destPath, ADLS_ACCOUNT_NAME);
                FrontEndAdapter frontend = new DataLakeStoreFrontEndAdapterImpl(ADLS_ACCOUNT_NAME, _adlsFileSystemClient);
                DataLakeStoreUploader uploader = new DataLakeStoreUploader(parameters, frontend);
                uploader.execute();
            }

            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, AdlsErrorException {
                InputStream stream = _adlsFileSystemClient.fileSystems().open(ADLS_ACCOUNT_NAME, srcPath).getBody();

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
                _adlsClient.accounts().delete(RESOURCE_GROUP_NAME, ADLS_ACCOUNT_NAME);
            }
        }


6. アプリケーションを実行します。指示に従って、アプリケーションを実行して完了します。

## 次のステップ

- [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
- [Data Lake Store で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0824_2016-->