<properties 
	pageTitle="Data Factory SDK を使用して Azure Data Factory を作成、監視、管理する" 
	description="プログラムで作成、監視、およびデータの工場出荷時の SDK を使用して Azure のデータのファクトリを管理する方法について説明します。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Data Factory .NET SDK を使用して Azure Data Factory を作成、監視、管理する
## 概要
Data Factory .NET SDK を使用して Azure Data Factory をプログラムによって作成、監視、管理できます。この記事には、Data Factory の作成と監視を行うサンプルの .NET コンソール アプリケーションを作成できるチュートリアルが含まれています。参照してください [データ ファクトリ クラス ライブラリ リファレンス][adf-class-library-reference] データ工場出荷時の .NET SDK に関する詳細です。



## 前提条件

- Visual Studio 2012 または 2013
- ダウンロードしてインストール [Azure .NET SDK][azure-developer-center]
- Azure Data Factory の NuGet パッケージをダウンロードしてインストールします。手順はこのチュートリアルにあります。

## チュートリアル
1. Visual Studio 2012 または 2013 を使用して、C# .NET コンソール アプリケーションを作成します。
	<ol type="a">
	<li>起動 <b>Visual Studio 2012</b> または <b>Visual Studio 2013</b>です。</li>
	<li>クリックして <b>ファイル</b>, 、順にポイント <b>新規</b>, 、] をクリック <b>プロジェクト</b>です。</li> 
	<li>展開 <b>テンプレート</b>, 、選択 <b>Visual c#</b>です。このチュートリアルでは C# を使用しますが、どの .NET 言語でも使用できます。</li> 
	<li>選択 <b>コンソール アプリケーション</b> 、右側のプロジェクトの種類の一覧からです。</li>
	<li>入力 <b>DataFactoryAPITestApp</b> の <b>名前</b>です。</li> 
	<li>選択 <b>C:\ADFGetStarted</b> の <b>場所</b>です。</li>
	<li><b>[OK]</b> をクリックしてプロジェクトを作成します。</li>
</ol>
2. クリックして <b>ツール</b>, 、順にポイント <b>NuGet Package Manager</b>, 、] をクリック <b>パッケージ マネージャー コンソール</b>です。
3.	 <b>パッケージ マネージャー コンソール</b>, 、1 つずつ、次のコマンドを実行します。</b>です。 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre
		Install-Package Microsoft.DataFactories.Runtime –Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. 次の追加 **appSetttings** セクション、 **App.config** ファイルです。これらは、ヘルパー メソッドを使用します。 **GetAuthorizationHeader**です。 

	**SubscriptionId** および **ActiveDirectoryTenantId** の値を Azure サブスクリプションとテナント ID に置き換えます。これらの値を取得するには、実行して **Get AzureAccount** (Add-azureaccount を使用して最初にログインする必要があります)、Azure PowerShell からです。
 
		<appSettings>
		    <!--CSM Prod related values-->
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		    <add key="AdfClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		    <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		    <!--Make sure to write your own tenenat id and subscription ID here-->
		    <add key="SubscriptionId" value="49fb6e5f-3098-4fb2-ba2f-6d6eed843a65" />
    		<add key="ActiveDirectoryTenantId" value="37330244-7828-4a28-99b7-c8c3a437c7ac" />
		</appSettings>
6. 次の追加 **を使用して** ステートメントをプロジェクトのソース ファイル (Program.cs) にします。

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
				
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure; 
6. インスタンスを作成する次のコードを追加する **DataPipelineManagementClient** クラスを **Main** メソッドです。このオブジェクトを使用して、Data Factory、リンクされたサービス、入力テーブルと出力テーブル、パイプラインを作成します。また、実行時にテーブルのスライスも監視します。    

        // create data pipeline management client
        string resourceGroupName = "ADF";
        string dataFactoryName = "APITutorialFactory";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataPipelineManagementClient client = new DataPipelineManagementClient(aadTokenCredentials, resourceManagerUri);
7. 作成する次のコードを追加、 **データ ファクトリ** を **Main** メソッドです。

        // create a data factory
        Console.WriteLine("Creating a data factory");
        client.DataFactories.CreateOrUpdate(resourceGroupName,
            new DataFactoryCreateOrUpdateParameters()
            {
                DataFactory = new DataFactory()
                {
                    Name = dataFactoryName,
                    Location = "westus",
                    Properties = new DataFactoryProperties() { }
                }
            }
        );
8. 作成する次のコードを追加、 **サービス リンク** を **Main** メソッドです。
	> [AZURE.NOTE]**アカウント名****アカウント キー****ConnectionString** 

		// create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new AzureStorageLinkedService()
                    {
                        ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=account key",
                    }
                }
            }
        );
9. 次のコードを作成する追加 **テーブルの入力呼び出し力** を **Main** メソッドです。 

	注意して、 **FolderPath** に設定されている入力の blob の **adftutorial/** 場所 **adftutorial** 、blob ストレージ内のコンテナーの名前を指定します。このコンテナーが、Azure blob ストレージに存在しない場合は、この名前のコンテナーを作成: **adftutorial** と、コンテナーにテキスト ファイルをアップロードします。
	
	設定されている出力 blob の FolderPath が: **adftutorial/apifactoryoutput {スライス}/** 場所 **スライス** の値に基づいて動的に計算が **SliceStart** (各スライスの日付と時刻を開始します)。

 
        // create input and output tables
        Console.WriteLine("Creating input and output tables");
        string Table_Source = "TableBlobSource";
        string Table_Destination = "TableBlobDestination";

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Source,
                    Properties = new TableProperties()
                    {
                        Location = new AzureBlobLocation()
                        {
                            FolderPath = "adftutorial/",
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                            WaitOnExternal = new WaitOnExternal()
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Destination,
                    Properties = new TableProperties()
                    {
                        Location = new AzureBlobLocation()
                        {
                            FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                            PartitionedBy = new Collection<Partition>()
                            {
                                new Partition()
                                {
                                    Name = "Slice",
                                    Value = new DateTimePartitionValue()
                                    {
                                        Date = "SliceStart",
                                        Format = "yyyyMMdd-HH"
                                    }
                                }
                            },
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });
10. 次のコードを追加 **を作成し、パイプラインがアクティブになります。** を、 **Main** メソッドです。このパイプラインが、 **CopyActivity** を受け取る **BlobSource** をソースとしてと **BlobSink** シンクとして。 

        // create a pipeline
        Console.WriteLine("Creating a pipeline");
        DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
        DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
        string PipelineName = "PipelineBlobSample";

        client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new PipelineCreateOrUpdateParameters()
            {
                Pipeline = new Pipeline()
                {
                    Name = PipelineName,
                    Properties = new PipelineProperties()
                    {
                        Description = "Demo Pipeline for data transfer between blobs",

                        // Initial value for pipeline's active period. With this, you won't need to set slice status
                        Start = PipelineActivePeriodStartTime,
                        End = PipelineActivePeriodEndTime,

                        Activities = new List<BaseActivity>()
                        {
                            new CopyActivity()
                            {
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput()
                                    {
                                        Name = Table_Source,
                                    }
                                },
                        
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Table_Destination, 
                                    }
                                },
                     
                                Transformation = new CopyActivityProperties()
                                {
                                    Source = new BlobSource()
                                    {
                                        BlobColumnSeparators = ",",
                                    },
                            
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    },

                                },

                                Policy = new ActivityPolicy()
                                {
                                    ExecutionPriorityOrder = ExecutionPriorityOrder.NewestFirst,
                                    Concurrency = 1,
                                    Retry = 2,
                                    Timeout = TimeSpan.FromMinutes(10),
                                }
                            }

                        },
                    }
                }
            });

11. 使用される、次のヘルパー メソッドを追加、 **Main** メソッドを **プログラム** クラスです。このメソッドを提供できるダイアログ ボックスが表示されます **ユーザー名** と **パスワード** に使用する Azure ポータルにログインします。
 
		public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AdfClientId"],
                        redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                        promptBehavior: PromptBehavior.Always);
                }
                catch (Exception threadEx)
                {
                    Console.WriteLine(threadEx.Message);
                }
            });

            thread.SetApartmentState(ApartmentState.STA);
            thread.Name = "AcquireTokenThread";
            thread.Start();
            thread.Join();

            if (result != null)
            {
                return result.AccessToken;
            }

            throw new InvalidOperationException("Failed to acquire token");
        }  
 
13. 次のコードを追加、 **Main** メソッドを出力テーブルのデータ スライスの状態を取得します。この例で予想されるスライスのみが存在します。
 
        // Pulling status within a timeout threshold
        DateTime start = DateTime.Now;
        bool done = false;

        while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
        {
            Console.WriteLine("Pulling the slice status");
            // wait before the next status check
            Thread.Sleep(1000 * 12);

            var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString());

            foreach (DataSlice slice in datalistResponse.DataSlices)
            {
                if (slice.Status == DataSliceStatus.Failed || slice.Status == DataSliceStatus.Ready)
                {
                    Console.WriteLine("Slice execution is done with status: {0}", slice.Status);
                    done = true;
                    break;
                }
                else
                {
                    Console.WriteLine("Slice status is: {0}", slice.Status);
                }
            }
        }

14. 実行のデータ スライス スライスの詳細を取得するには、次のコードを追加、 **Main** メソッドです。

        Console.WriteLine("Getting run details of a data slice");

        var datasliceRunListResponse = client.DataSliceRuns.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString());

        foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
        {
            Console.WriteLine("Status: \t\t{0}", run.Status);
            Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
            Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
            Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
            Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
            Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
            Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
        }

        Console.WriteLine("\nPress any key to exit.");
        Console.ReadKey();
    {

15. コンソール アプリケーションをビルドします。クリックして **ビルド** ] をクリックし、メニューに **ソリューションのビルド**です。
16. Azure BLOB ストレージ内の adftutorial コンテナーに少なくとも 1 つのファイルが存在することを確認します。存在しない場合は、以下の内容を記述した Emp.txt ファイルをメモ帳で作成し、これを adftutorial コンテナーにアップロードします。

        John, Doe
		Jane, Doe
	 
17. クリックして、サンプルを実行 **デバッグ** ]-> [ **デバッグの開始]** メニュー。
18. データのファクトリにいることを確認するには、Azure プレビュー ポータルを使用して: **APITutorialFactory** は、次のアイテムを作成します。 
	- サービスのリンク: **LinkedService_AzureStorage** 
	- テーブル: **TableBlobSource** と **TableBlobDestination**です。
	- パイプライン: **PipelineBlobSample** 
18. 出力ファイルが作成されることを確認、 **apifactoryoutput** フォルダーで、 **adftutorial** コンテナーです。


## 関連項目

記事 | 説明
------ | ---------------
[Azure のデータの工場出荷時の開発者向けリファレンス][developer-reference] | .NET クラス ライブラリ、コマンドレット、JSON スクリプト、関数などの包括的な参照の内容を開発者向けリファレンスには. 


[data-factory-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/downloads/

<!---HONumber=GIT-SubDir--> 