<properties 
	pageTitle="Stream Analytics ジョブの監視 | Azure" 
	description="プログラムで Stream Analytics ジョブを監視します。" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="05/07/2015" 
	ms.author="jeffstok"/>


# プログラムでの Stream Analytics ジョブの監視
この記事では、Stream Analytics ジョブの監視を有効にする方法を示します。REST API、Azure SDK、または Powershell を介して作成された Stream Analytics ジョブは、既定では監視は有効になっていません。Azure ポータルで、ジョブの [監視] ページに移動し、[有効にする] ボタンをクリックして、手動で監視を有効にできます。または、この記事にある手順に従って、有効にするプロセスを自動化することもできます。監視データは、Stream Analytics ジョブ用の Azure ポータルの [監視] タブに表示されます。

![[ジョブ] タブの監視](./media/stream-analytics-monitor-jobs/stream-analytics-monitor-jobs-tab.png)

## 前提条件
この記事を読み始める前に、次の項目を用意する必要があります。

- Visual Studio 2012 または 2013。
- [Azure .NET SDK](http://azure.microsoft.com/downloads/) のダウンロードとインストール。
- 監視を有効にする必要がある、既存の Stream Analytics ジョブ。

## プロジェクトのセットアップ

1.	Visual Studio C# .Net コンソール アプリケーションを作成します。
2.	パッケージ マネージャー コンソールで、次のコマンドを実行して NuGet パッケージをインストールします。1 つ目は、Azure Stream Analytics 管理用 .NET SDK です。2 つ目は、監視を有効にするために使用される Azure Insights SDK です。最後の 1 つは、認証で使用する Azure Active Directory クライアントです。

    ```
    Install-Package Microsoft.Azure.Management.StreamAnalytics –Pre
    Install-Package Microsoft.Azure.Insights -Pre
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

3.	次の appSettings セクションを App.config ファイルに追加します。

    ```
    <appSettings>
    	<!--CSM Prod related values-->
    	<add key="ActiveDirectoryEndpoint" value="https://login.windows-ppe.net/" />
    	<add key="ResourceManagerEndpoint" value="https://api-current.resources.windows-int.net/" />
    	<add key="WindowsManagementUri" value="https://management.core.windows.net/" />
    	<add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
    	<add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
    	<add key="SubscriptionId" value="<YOUR AZURE SUBSCRIPTION ID>" />
    	<add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
    </appSettings>
	```
*SubscriptionId* および *ActiveDirectoryTenantId* の値を Azure サブスクリプションとテナント ID に置き換えます。次の PowerShell コマンドレットを実行すると、これらの値を取得できます。

    ```
    Get-AzureAccount
    ```
4.	次の using ステートメントをプロジェクト内のソース ファイル (Program.cs) に追加します。 

    ```
        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.Insights;
        using Microsoft.Azure.Management.Insights.Models;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
5.	認証ヘルパー メソッドを追加します。

        public static string GetAuthorizationHeader()
        	{
        		AuthenticationResult result = null;
        		var thread = new Thread(() =>
        		{
        			try
        			{
            			var context = new AuthenticationContext(
                			ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                			ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

            			result = context.AcquireToken(
                			resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                			clientId: ConfigurationManager.AppSettings["AsaClientId"],
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

## 管理クライアントの作成
次のコードは、必要な変数と管理クライアントをセットアップします。

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
    	new TokenCloudCredentials(
    		ConfigurationManager.AppSettings["SubscriptionId"],
    		GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);

## 既存の Stream Analytics ジョブに対する監視の有効化

次のコードは、**既存の** Stream Analytics ジョブに対して監視を有効にします。コードの最初の部分では、Stream Analytics サービスに対して GET 要求を実行して、特定の Stream Analytics ジョブに関する情報を取得します。コードの後半部分では、GET 要求で取得した "Id" プロパティをパラメーターとして Put メソッドが Insights サービスに送信され、Stream Analytics ジョブの監視を有効にします。

> [AZURE.WARNING]- Azure ポータルからまたは以下のコードによりプログラムで別の Stream Analytics ジョブの監視を既に有効にしている場合は、**以前に監視を有効にしたときと同じストレージ アカウント名を指定することをお勧めします。** - ストレージ アカウントは、特定のジョブ自体ではなく、Stream Analytics ジョブを作成したリージョンに関連付けられます。 - 同じリージョン内のすべての Stream Analytics ジョブ (および他のすべての Azure リソース) は、このストレージ アカウントを共有して監視データを格納します。別のストレージ アカウントを指定すると、他の Stream Analytics ジョブまたは他の Azure リソース (あるいはその両方) の監視に意図しない副作用が起きることがあります。- 以下の ```“<YOUR STORAGE ACCOUNT NAME>”``` に置き換えるために使用するストレージ アカウント名は、監視を有効にする Stream Analytics ジョブと同じサブスクリプション内にあるストレージ アカウントにする必要があります。

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
    	PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
    		Properties = new ServiceDiagnosticSettings()
    		{
        		StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
    		}
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## サポートを受ける
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。


## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用](stream-analytics-get-started.md)
- [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--HONumber=54-->