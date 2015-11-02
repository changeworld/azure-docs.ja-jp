<properties 
	pageTitle="Data Factory SDK を使用して Azure Data Factory を作成、監視、管理する" 
	description="Data Factory SDK を使用して Azure Data Factory をプログラムによって作成、監視、管理する方法について説明します。" 
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
	ms.date="10/06/2015" 
	ms.author="spelluru"/>

# Data Factory .NET SDK を使用して Azure Data Factory を作成、監視、管理する
## 概要
Data Factory .NET SDK を使用して Azure Data Factory をプログラムによって作成、監視、管理できます。この記事には、Data Factory の作成と監視を行うサンプルの .NET コンソール アプリケーションを作成できるチュートリアルが含まれています。Data Factory .NET SDK の詳細については、[Data Factory クラス ライブラリ リファレンス][adf-class-library-reference]に関するページをご覧ください。



## 前提条件

- Visual Studio 2012 または 2013
- [Azure .NET SDK][azure-developer-center] のダウンロードとインストール
- Azure Data Factory の NuGet パッケージをダウンロードしてインストールします。手順はこのチュートリアルにあります。

## チュートリアル
1. Visual Studio 2012 または 2013 を使用して、C# .NET コンソール アプリケーションを作成します。
	<ol type="a">
	<li><b>Visual Studio 2012</b> または <b>Visual Studio 2013</b> を起動します。</li>
	<li><b>[ファイル]</b> をクリックし、<b>[新規作成]</b> をポイントして、<b>[プロジェクト]</b> をクリックします。</li> 
	<li><b>[テンプレート]</b> を展開し、<b>[Visual C#]</b> を選択します。このチュートリアルでは C# を使用しますが、どの .NET 言語でも使用できます。</li> 
	<li>右側にあるプロジェクトの種類の一覧から <b>[コンソール アプリケーション]</b> を選択します。</li>
	<li><b>[名前]</b> に「<b>DataFactoryAPITestApp</b>」と入力します。</li> 
	<li><b>[場所]</b> で <b>[C:\ADFGetStarted]</b> を選択します。</li>
	<li><b>[OK]</b> をクリックしてプロジェクトを作成します。</li>
</ol>
2. <b>[ツール]</b> をクリックし、<b>[NuGet パッケージ マネージャー]</b> にカーソルを合わせ、<b>[パッケージ マネージャー コンソール]</b> をクリックします。
3.	<b>[パッケージ マネージャー コンソール]</b> で、次のコマンドを 1 つずつ入力します。</b> 

		Install-Package Microsoft.Azure.Management.DataFactories
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. 次の **appSetttings** セクションを **App.config** ファイルに追加します。これらは、ヘルパー メソッド **GetAuthorizationHeader** によって使用されます。 

	**SubscriptionId** および **ActiveDirectoryTenantId** の値を Azure サブスクリプションとテナント ID に置き換えます。これらの値は、Azure PowerShell から **Get-AzureAccount** を実行して取得できます (最初に Add-AzureAccount を使用してログインすることが必要になる場合があります)。
 
		<appSettings>
		    <!--CSM Prod related values-->
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		    <add key="AdfClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		    <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		    <!--Make sure to write your own tenenat id and subscription ID here-->
		    <add key="SubscriptionId" value="your subscription ID" />
    		<add key="ActiveDirectoryTenantId" value="your tenant ID" />
		</appSettings>
6. 次の **using** ステートメントをプロジェクト内のソース ファイル (Program.cs) に追加します。

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
		
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Common.Models;
		
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure;
6. **DataPipelineManagementClient** クラスのインスタンスを作成する次のコードを **Main** メソッドに追加します。このオブジェクトを使用して、Data Factory、リンクされたサービス、入力と出力データセット、およびパイプラインを作成します。また、実行時にデータセットのスライスを監視する際にもこのオブジェクトを使用します。    

        // create data factory management client
        string resourceGroupName = "resourcegroupname";
        string dataFactoryName = "APITutorialFactorySP";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

	> [AZURE.NOTE]**resourcegroupname** は Azure リソース グループの名前に置き換えます。リソース グループを作成するには、[New-AzureResourceGroup](https://msdn.microsoft.com/library/Dn654594.aspx) コマンドレットを使用します。

7. **データ ファクトリ**を作成する次のコードを **Main** メソッドに追加します。

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

8. **リンクされたサービス**を作成する次のコードを **Main** メソッドに追加します。

	> [AZURE.NOTE]**ConnectionString** には、Azure ストレージ アカウントの**アカウント名**と**アカウント キー**を使用します。

        // create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new LinkedServiceProperties
                    (
                        new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>")
                    )
                }
            }
        );
9. **入力と出力データセット**を作成する次のコードを **Main** メソッドに追加します。 

	入力 BLOB の **FolderPath** を **adftutorial/** に設定します。この **adftutorial** は BLOB ストレージ内のコンテナーの名前です。Azure BLOB ストレージにこのコンテナーが存在しない場合は、**adftutorial** という名前のコンテナーを作成し、このコンテナーにテキスト ファイルをアップロードします。
	
	出力 BLOB の FolderPath を **adftutorial/apifactoryoutput/{Slice}** に設定します。この **Slice** は **SliceStart** (各スライスの開始日時) の値に基づいて動的に計算されます。

 
        // create input and output datasets
        Console.WriteLine("Creating input and output datasets");
        string Dataset_Source = "DatasetBlobSource";
        string Dataset_Destination = "DatasetBlobDestination";

        client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
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

        client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Destination,
                    Properties = new DatasetProperties()
                    {

                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
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
                            }
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });

11. **パイプラインを作成してアクティブにする**次のコードを **Main** メソッドに追加します。このパイプラインには、ソースとして **BlobSource**、シンクとして **BlobSink** を使用する **CopyActivity** があります。

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

                        Activities = new List<Activity>()
                        {                                
                            new Activity()
                            {   
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput() {
                                        Name = Dataset_Source
                                    }
                                },
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Dataset_Destination
                                    }
                                },
                                TypeProperties = new CopyActivity()
                                {
                                    Source = new BlobSource(),
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    }
                                }
                            }

                        },
                    }
                }
            });

	

12. **Main** メソッドで使用される次のヘルパー メソッドを **Program** クラスに追加します。このメソッドは、Azure ポータルへのログインに使用する**ユーザー名**と**パスワード**の入力が可能なダイアログ ボックスを表示します。
 
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
 
13. 次のコードを **Main** メソッドに追加して、出力データセットのデータ スライスのステータスを取得します。この例で予想されるスライスのみが存在します。
 
        // Pulling status within a timeout threshold
        DateTime start = DateTime.Now;
        bool done = false;

        while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
        {
            Console.WriteLine("Pulling the slice status");
            // wait before the next status check
            Thread.Sleep(1000 * 12);

            var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
                new DataSliceListParameters()
                {
                    DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                    DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
                });

            foreach (DataSlice slice in datalistResponse.DataSlices)
            {
                if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
                {
                    Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                    done = true;
                    break;
                }
                else
                {
                    Console.WriteLine("Slice status is: {0}", slice.State);
                }
            }
        }

14. **(省略可能)**データ スライスの実行の詳細を取得するために次のコードを **Main** メソッドに追加します。

        Console.WriteLine("Getting run details of a data slice");

		// give it a few minutes for the output slice to be ready
        Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
        Console.ReadKey();

        var datasliceRunListResponse = client.DataSliceRuns.List(
                resourceGroupName,
                dataFactoryName,
                Dataset_Destination,
                new DataSliceRunListParameters()
                {
                    DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                }
            );
        
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

15. ソリューション エクスプローラーで、プロジェクト (**DataFactoryAPITestApp**) を展開し、**[参照]** を右クリックして **[参照の追加]** をクリックします。**System.Configuration** アセンブリのチェック ボックスをオンにして、**[OK]** をクリックします。
16. コンソール アプリケーションをビルドします。メニューから **[ビルド]** をクリックし、**[ソリューションのビルド]** をクリックします。 
16. Azure BLOB ストレージ内の adftutorial コンテナーに少なくとも 1 つのファイルが存在することを確認します。存在しない場合は、以下の内容を記述した Emp.txt ファイルをメモ帳で作成し、これを adftutorial コンテナーにアップロードします。

        John, Doe
		Jane, Doe
	 
17. メニューの **[デバッグ]**、**[デバッグの開始]** の順にクリックして、サンプルを実行します。**[Getting run details of a data slice]** が表示されている場合は、数分待機して、**Enter** を押します。
18. Azure プレビュー ポータルを使用して、データ ファクトリの **APITutorialFactory** が次のアーティファクトで作成されることを確認します。 
	- リンクされたサービス: **LinkedService\_AzureStorage** 
	- データセット: **DatasetBlobSource** と **DatasetBlobDestination**
	- パイプライン: **PipelineBlobSample** 
18. **adftutorial** コンテナーの **apifactoryoutput** フォルダーに出力ファイルが作成されることを確認します。



> [AZURE.NOTE]上記のサンプル コードでは、Azure 資格情報を入力するためのダイアログ ボックスが起動されます。ダイアログ ボックスを使用せずにプログラムでサインインする必要がある場合は、「[Azure リソース マネージャーでのサービス プリンシパルの認証](resource-group-authenticate-service-principal.md#authenticate-service-principal-with-certificate---powershell)」を参照してください。


[data-factory-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/downloads/
 

<!---HONumber=Oct15_HO4-->