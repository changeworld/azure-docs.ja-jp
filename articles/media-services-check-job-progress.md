<properties urlDisplayName="Check Job Progress" pageTitle="Media Services でジョブの進行状況をチェックする方法 - Azure" metaKeywords="" description="Learn how to use event handler code to track job progress and send status updates. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Check Job Progress" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





<h1>方法:ジョブの進行状況をチェックする</h1>
この記事は、Azure メディア サービスのプログラミングを紹介するシリーズの一部です。前のトピックは、「[方法: アセットをエンコードする](http://go.microsoft.com/fwlink/?LinkID=301753&clcid=0x409)です。

ジョブを実行する際には、多くの場合、ジョブの進行状況を追跡する手段が必要になります。次のコード例では、StateChanged イベント ハンドラーを定義しています。このイベント ハンドラーはジョブの進行状況を追跡し、状態によってステータスを更新します。このコードでは、LogJobStop メソッドも定義しています。このヘルパー メソッドは、エラー詳細のログ記録を行います。

<pre><code>
private static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);

    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("********************");
            Console.WriteLine("Job is finished.");
            Console.WriteLine("Please wait while local tasks or downloads complete...");
            Console.WriteLine("********************");
            Console.WriteLine();
            Console.WriteLine();
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
        case JobState.Error:
            // Cast sender as a job.
            IJob job = (IJob)sender;
            // Display or log error details as needed.
            LogJobStop(job.Id);
            break;
        default:
            break;
    }
}

private static void LogJobStop(string jobId)
{
    StringBuilder builder = new StringBuilder();
    IJob job = GetJob(jobId);

    builder.AppendLine("\nThe job stopped due to cancellation or an error.");
    builder.AppendLine("***************************");
    builder.AppendLine("Job ID: " + job.Id);
    builder.AppendLine("Job Name: " + job.Name);
    builder.AppendLine("Job State: " + job.State.ToString());
    builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
    builder.AppendLine("Media Services account name: " + _accountName);
    builder.AppendLine("Media Services account location: " + _accountLocation);
    // Log job errors if they exist.  
    if (job.State == JobState.Error)
    {
        builder.Append("Error Details: \n");
        foreach (ITask task in job.Tasks)
        {
            foreach (ErrorDetail detail in task.ErrorDetails)
            {
                builder.AppendLine("  Task Id: " + task.Id);
                builder.AppendLine("    Error Code: " + detail.Code);
                builder.AppendLine("    Error Message: " + detail.Message + "\n");
            }
        }
    }
    builder.AppendLine("***************************\n");
    // Write the output to a local file and to the console. The template 
    // for an error output file is:  JobStop-{JobId}.txt
    string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
    WriteToFile(outputFile, builder.ToString());
    Console.Write(builder.ToString());
}

private static string JobIdAsFileName(string jobID)
{
    return jobID.Replace(":", "_");
}
</code></pre>
<h2>次のステップ</h2>
これで、ジョブを作成して進行状況を追跡する方法を学習できました。次のステップはアセットを保護することです。詳細については、「[How to Protect an Asset with Azure Media Services (Azure メディア サービスでアセットを保護する方法)](http://go.microsoft.com/fwlink/?LinkID=301813&clcid=0x409)」を参照してください。

<!--HONumber=35_1-->
