<properties linkid="manage-services-storage-using-storage-with-windows-store-apps" urlDisplayName="Windows ストア アプリケーションの Azure ストレージ" pageTitle="Windows ストア アプリケーションでの Azure ストレージの使用 | Azure" metaKeywords="" description="Azure BLOB、キュー、テーブルを使用して Windows ストア アプリケーションのデータを格納する方法を説明します。" metaCanonical="" services="storage" documentationCenter="" title="Windows ストア アプリケーションでの Azure ストレージの使用方法" authors="" solutions="" manager="" editor="" />





# Windows ストア アプリケーションでの Azure ストレージの使用方法

このガイドでは、Azure ストレージを利用する Windows ストア アプリケーションの開発に着手する方法を紹介します。

## 必要なツールのダウンロード##

- [Visual Studio 2012](http://msdn.microsoft.com/ja-jp/library/windows/apps/br211384) では、Windows ストア アプリケーションのビルド、デバッグ、ローカライズ、パッケージ化、および展開を簡単に実行できます。
- [Windows ランタイム用 Azure のストレージ クライアント ライブラリ](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/05/windows-azure-storage-client-library-for-windows-runtime.aspx)には、Azure ストレージとの通信に使用される型が含まれており、Windows ランタイム コンポーネントにパッケージ化されています。
- [Windows ストア アプリケーション用 WCF Data Services ツール](http://www.microsoft.com/ja-jp/download/details.aspx?id=30714)は、Visual Studio 2012 の [サービス参照の追加] 機能を拡張し、Windows ストア アプリケーション用のクライアント側 OData サポートを追加します。

## アプリケーションの開発##

<h3>開発の準備</h3>

Visual Studio 2012 で、新しい Windows ストア アプリケーション プロジェクトを作成します。

![store-apps-storage-vs-project][store-apps-storage-vs-project]

次に、Azure ストレージ クライアント ライブラリへの参照を追加します。そのためには、**[参照]** を右クリックし、**[参照の追加]** を選択して、ダウンロードした Windows ランタイム用ストレージ クライアント ライブラリに移動します。

![store-apps-storage-choose-library][store-apps-storage-choose-library]

<h3>BLOB およびキュー サービスでのライブラリの使用</h3>

この時点で、アプリケーションは、BLOB およびキュー サービスと通信できる状態になっています。Azure ストレージの型を直接参照できるようにするために、次の **using** ステートメントを追加します。

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    
次に、ページにボタンを追加します。次のコードをボタンの **Click** イベントに追加し、イベント ハンドラー メソッドを [async キーワード](http://msdn.microsoft.com/ja-jp/library/vstudio/hh156513.aspx)で変更します。
    
    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();
    
このコードでは、2 つの文字列変数 *accountName* および *accountKey* があると想定しています。これらは、ストレージ アカウント名と、そのアカウントに関連付けられているアカウント キーを表します。

アプリケーションをビルドし、実行します。ボタンをクリックすると、*container1* というコンテナーがアカウントに存在するかどうかが最初にチェックされ、存在しない場合は作成されます。

<h3>ライブラリとテーブル サービスの使用</h3>

テーブル サービスとの通信に使用される型は、Windows ストア アプリケーション用の WCF Data Services ライブラリに依存します。ここでは、パッケージ マネージャー コンソールを使用して、必要な WCF ライブラリへの参照を追加します。

![store-apps-storage-package-manager][store-apps-storage-package-manager]

次のコマンドを使用して、パッケージ マネージャーにコンピューター上の場所を指定します。
    
    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

このコマンドにより、必要なすべての参照が自動的にプロジェクトに追加されます。パッケージ マネージャー コンソールを使用しない場合は、「[Managing NuGet Packages Using the Dialog (ダイアログを使用した NuGet パッケージの管理)](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog)」の説明に従って、UI 上でローカル コンピューターの WCF Data Services NuGet フォルダーをパッケージ ソースの一覧に追加してから、参照を追加することもできます。

WCF Data Services NuGet パッケージを参照したら、ボタンの **Click** イベントのコードを変更します。
    
    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();
    
このコードでは、アカウントに *table1* という名前のテーブルが存在するかどうかを確認し、存在しない場合は作成します。

ダウンロードした同じパッケージに含まれている Microsoft.WindowsAzure.Storage.Table.dll への参照を追加することもできます。このライブラリには、リフレクション ベースのシリアル化や汎用クエリなどの追加機能が含まれます。ただし、このライブラリは JavaScript をサポートしていないことに注意してください。



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png

