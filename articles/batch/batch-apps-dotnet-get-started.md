<properties
	pageTitle="チュートリアル - .NET 向け Azure Batch Apps ライブラリの概要"
	description="Azure Batch Apps の基本的な概念と、簡単なシナリオによる開発方法について説明します。"
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="07/21/2015"
	ms.author="yidingz"/>




# .NET 向け Azure Batch Apps ライブラリの概要
このチュートリアルでは、Batch Apps サービスを使って Azure Batch 上で並列計算ワークロードを実行する方法を示します。

Batch Apps は、Batch のワークロードを管理および実行するためのアプリケーション中心の方法を提供する Azure Batch の機能です。Batch Apps SDK を使用すると、パッケージを Batch 対応アプリケーションとして作成し、これらを自分のアカウントにデプロイしたり、他の Batch ユーザーが使用できるようにしたりできます。Batch には、データ管理、ジョブ監視、組み込みの診断とログ記録、およびタスク間依存関係のサポートも用意されています。さらに、ジョブの管理、ログの表示、出力のダウンロードを行える管理ポータルも用意されているため、自分でクライアントを記述する必要はありません。

Batch Apps シナリオでは、Batch Apps Cloud SDK を使用して、コードを記述します。コードでは、ジョブを並列タスクに分割し、これらのタスク間の依存関係を記述し、各タスクを実行する方法を指定します。このコードは、Batch アカウントにデプロイされます。クライアントは、ジョブの種類と REST API への入力ファイルを指定するだけで、ジョブを実行できます。

>[AZURE.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。数分で無料試用版のアカウントを作成することができます。詳細については、[Azure の無料評価版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。NuGet を使用すると、<a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/">Batch Apps Cloud</a> アセンブリと <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/">Batch Apps Client</a> アセンブリの両方を取得できます。Visual Studio でプロジェクトを作成した後、**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。アプリケーションをクラウド対応にするプロジェクト テンプレートとアプリケーションをデプロイする機能が含まれた Batch Apps 用 Visual Studio 拡張機能は、<a href="https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a">ここ</a>からダウンロードすることもできます。また、Visual Studio の [拡張機能と更新プログラム] で "**Batch Apps**" を検索することもできます。また、<a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">MSDN でエンド ツー エンドのサンプル</a>を見つけることもできます。
>

###Azure Batch Apps の基礎
Batch は、既存のコンピューティング集中型アプリケーションに対応するように設計されています。Batch は、既存のアプリケーション コードを使用して、動的な仮想化された汎用環境でアプリケーションを実行します。アプリケーションを Batch Apps に対応させるには、いくつかの操作を行う必要があります。

1.	既存のアプリケーションの実行可能ファイル (従来のサーバー ファームまたはクラスターで実行するのと同じ実行可能ファイル) と必要なすべてのサポート ファイルを含む zip ファイルを作成します。この zip ファイルを、管理ポータルまたは REST API を使用して Batch アカウントにアップロードします。
2.	ワークロードをアプリケーションにディスパッチする "クラウド アセンブリ" の zip ファイルを作成し、管理ポータルまたは REST API を使用してアップロードします。クラウド アセンブリには、Cloud SDK 用の 2 つのコンポーネントが含まれています。
	1.	ジョブ スプリッター - ジョブを個別に処理できるタスクに分割します。たとえば、アニメーション シナリオで、ジョブ スプリッターは、ムービー ジョブを受け取って個々のフレームに分割します。
	2.	タスク プロセッサ - 特定のタスクに対してアプリケーション実行可能ファイルを呼び出します。たとえば、アニメーション シナリオで、タスク プロセッサは、レンダリング プログラムを起動して、現在のタスクで指定された 1 つのフレームをレンダリングします。
3.	Azure で有効になっているアプリケーションにジョブを送信する方法を提供します。これには、アプリケーションの UI のプラグインや Web ポータルを使用できます。また、実行パイプラインの一部としての自動サービスを使用することもできます。例については、MSDN の<a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">サンプル</a>をご覧ください。



###Batch Apps の主要な概念
Batch Apps のプログラミングと使用モデルに含まれる主要な概念を次に示します。

####ジョブ
**ジョブ**は、ユーザーによって送信される 1 つの処理です。ユーザーは、ジョブを送信するときに、ジョブの種類、ジョブの設定、ジョブに必要なデータを指定します。これらの詳細は、ユーザーの代わりに、有効になっている実装から提供できます。また、場合によっては、ユーザーがクライアントを介してこの情報を明示的に提供することができます。ジョブでは結果が返されます。各ジョブは、プライマリ出力とオプションのプレビュー出力を持ちます。ジョブは、必要に応じて追加の出力を返すことができます。

####タスク
**タスク**は、ジョブの一部として実行される 1 つの処理です。ユーザーがジョブを送信すると、ジョブはより小さなタスクに分割されます。サービスによってこれらの個々のタスクが処理された後、タスクの結果から全体的なジョブの出力が生成されます。タスクの特性は、ジョブの種類に依存します。ジョブ スプリッターは、アプリケーションが対象としている処理に関する情報に基づいて、ジョブをどのようにタスクに分類するかを定義します。タスクの出力も個別にダウンロードできます。たとえば、この機能は、ユーザーがアニメーション ジョブから個々のタスクをダウンロードできるようにする場合に便利です。

####マージ タスク
**マージ タスク**は、個々のタスクの結果から最終的なジョブの結果を生成する特殊なタスクです。たとえば、ムービー レンダリング ジョブの場合、マージ タスクは、レンダリングされたフレームからムービーを生成したり、すべてのレンダリングされたフレームを 1 つの zip ファイルに圧縮したりします。実際の "マージ" 操作が必要でない場合でも、すべてのジョブはマージ タスクを持ちます。

####ファイル
**ファイル**は、ジョブへの入力として使用されるデータです。ジョブには、入力ファイルを関連付けないことも、1 つまたは複数の入力ファイルを関連付けることもできます。また、同じファイルを複数のジョブで使用することもできます。たとえば、ムービー レンダリング ジョブに対しては、テクスチャやモデルを含むファイルを使用します。データ分析ジョブに対しては、一式の観測値または測定値を含むファイルを使用します。

###クラウド アプリケーションを有効にする
アプリケーションには、アプリケーションのすべての詳細を含む静的フィールドまたはプロパティを含める必要があります。ここでは、アプリケーションの名前とアプリケーションによって処理されるジョブの種類を指定します。これは、Visual Studio ギャラリーからダウンロードできる SDK に含まれるテンプレートを使用するときに提供されます。

並列ワークロードのためのクラウド アプリケーション宣言の例を次に示します。

	public static class ApplicationDefinition
	{
	    public static readonly CloudApplication App = new ParallelCloudApplication
	    {
	        ApplicationName = "ApplicationName",
	        JobType = "ApplicationJobType",
	        JobSplitterType = typeof(MyJobSplitter),
	        TaskProcessorType = typeof(MyTaskProcessor),
	    };
	}

####ジョブ スプリッターとタスク プロセッサの実装
並列性がきわめて高いワークロードに対しては、ジョブ スプリッターとタスク プロセッサを実装する必要があります。

####JobSplitter.SplitIntoTasks の実装
SplitIntoTasks 実装では、タスクのシーケンスを返す必要があります。各タスクは、計算ノードによる処理のキューに配置されるそれぞれの処理を表します。各タスクは自己完結型である必要があり、タスク プロセッサが必要とするすべての情報を使ってセットアップする必要があります。

ジョブ スプリッターで指定されたタスクは TaskSpecifier オブジェクトとして表されます。TaskSpecifier にはいくつかのプロパティがあります。これらのプロパティは、タスクを返す前に設定できます。


-	TaskIndex は無視されますが、タスク プロセッサはこれを使用できます。TaskIndex を使用して、タスク プロセッサにインデックスを渡すことができます。インデックスを渡す必要がない場合は、このプロパティを設定する必要はありません。
-	Parameters は、既定では空のコレクションです。これに項目を追加したり、新しいコレクションで置き換えたりすることができます。WithJobParameters または WithAllJobParameters メソッドを使用してジョブ パラメーター コレクションからエントリをコピーすることができます。  


RequiredFiles は、既定では空のコレクションです。これに項目を追加したり、新しいコレクションで置き換えたりすることができます。RequiringJobFiles メソッドまたは RequiringAllJobFiles メソッドを使用して、ジョブ ファイル コレクションからエントリをコピーすることができます。

他の特定のタスクに依存するタスクを指定できます。そのためには、TaskSpecifier.DependsOn プロパティを設定し、依存するタスクの ID を渡します。

	new TaskSpecifier {
	    DependsOn = TaskDependency.OnId(5)
	}

タスクは、依存先のタスクの出力が使用可能になるまで実行されません。

また、タスクのグループ全体の処理を別のグループが完全に完了するまで開始しないように指定することもできます。その場合は、TaskSpecifier.Stage プロパティを設定できます。特定の Stage 値が指定されたタスクは、それよりも Stage 値が低いすべてのタスクが完了するまで処理を開始しません。たとえば、Stage 3 のタスクは、Stage 0、1、または 2 のタスクがすべて終了するまで処理を開始しません。Stage 値は 0 から始まる必要があり、欠番があってはいけません。SplitIntoTasks は、Stage 値の順序でタスクを返す必要があります。たとえば、Stage 1 のタスクの後に Stage 0 のタスクを返すとエラーになります。

すべての並列ジョブは、マージ タスクと呼ばれる特殊なタスクで終了します。マージ タスクの役割は、並列処理タスクの結果から最終結果を生成することです。マージ タスクは、Batch Apps によって自動的に作成されます。

まれに、パラメーターをカスタマイズする場合など、マージ タスクを明示的に制御する必要がある場合があります。その場合、IsMerge プロパティを true に設定した TaskSpecifier を返すことによって、マージ タスクを指定できます。これにより、自動マージ タスクが上書きされます。明示的なマージ タスクを作成する場合は、次の条件が適用されます。

-	明示的なマージ タスクは 1 つだけ作成できます。
-	明示的なマージ タスクは、シーケンスの最後のタスクである必要があります。
-	明示的なマージ タスクでは IsMerge プロパティが true に設定されている必要があります。他のすべてのタスクでは、IsMerge が false に設定されている必要があります。  


ただし、通常はマージ タスクを明示的に作成する必要はありません。

次のコードに、SplitIntoTasks の単純な実装を示します。

	protected override IEnumerable<TaskSpecifier> SplitIntoTasks(
	    IJob job,
	    JobSplitSettings settings)
	{
	    int start = Int32.Parse(job.Parameters["startIndex"]);
	    int end = Int32.Parse(job.Parameters["endIndex"]);
	    int count = (end - start) + 1;

	    // Processing tasks
	    for (int i = 0; i < count; ++i)
	    {
	        yield return new TaskSpecifier
	        {
	            TaskIndex = start + i,
	        }.RequiringAllJobFiles();
	    }
	}
####ParallelTaskProcessor.RunExternalTaskProcess の実装

ジョブ スプリッターから返される非マージ タスクのそれぞれに対して、RunExternalTaskProcess が呼び出されます。これにより、適切な引数を使用してアプリケーションが呼び出され、出力のコレクションが返されます。このコレクションは、後で使用するために保持しておく必要があります。

次に、application.exe という名前のプログラムを呼び出す方法を示します。絶対ファイル パスの作成には、ExecutablePath のヘルパー メソッドを使用できます。

Cloud SDK の ExternalProcess クラスは、アプリケーションの実行可能ファイルを実行するのに便利なヘルパー ロジックを提供します。ExternalProcess は、キャンセル操作、終了コードの例外への変換、標準出力と標準エラーのキャプチャ、および環境変数の設定を処理します。さらに、必要に応じて、直接 .NET Process クラスを使用してプログラムを実行することもできます。

RunExternalTaskProcess メソッドは、出力ファイルの一覧が含まれた TaskProcessResult を返します。これには、最低限、マージに必要なすべてのファイルが含まれている必要があります。また、このメソッドは、必要に応じて、ログ ファイル、プレビュー ファイル、および中間ファイル (たとえば、タスクが失敗した場合の診断用) を返すことができます。このメソッドはファイルの内容ではなくファイル パスを返すことに注意してください。

それぞれのファイルは、格納している出力の種類で識別する必要があります。出力の種類には、出力 (最終的なジョブ出力の一部)、プレビュー ファイル、ログ ファイル、または中間ファイルがあります。これらの値は、TaskOutputFileKind 列挙型から取得します。次のコードは、1 つのタスク出力を返し、プレビューやログを返しません。TaskProcessResult.FromExternalProcessResult メソッドを使用すると、終了コード、プロセッサ出力、およびコマンド ライン プログラムからの出力ファイルをキャプチャする一般的なシナリオを簡略化できます。

次のコードに、ParallelTaskProcessor.RunExternalTaskProcess の単純な実装を示します。

	protected override TaskProcessResult RunExternalTaskProcess(
	    ITask task,
	    TaskExecutionSettings settings)
	{
	    var inputFile = LocalPath(task.RequiredFiles[0].Name);
	    var outputFile = LocalPath(task.TaskId.ToString() + ".jpg");

	    var exePath = ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("-in:{0} -out:{1}", inputFile, outputFile);

	    var result = new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath,
	        CancellationToken = settings.CancellationToken
	    }.Run();

	    return TaskProcessResult.FromExternalProcessResult(result, outputFile);
	}
####ParallelTaskProcessor.RunExternalMergeProcess の実装

これは、マージ タスク用に呼び出されます。このメソッドは、アプリケーションを呼び出して、前のタスクの出力をアプリケーションに適した方法で結合し、結合した出力を返します。

RunExternalMergeProcess の実装は RunExternalTaskProcess によく似ていますが、次の点が異なります。

-	RunExternalMergeProcess は、ユーザーの入力ファイルではなく前のタスクの出力を消費します。そのため、処理するファイルの名前を Task.RequiredFiles コレクションから取得する代わりに、タスク ID に基づいて指定する必要があります。
-	RunExternalMergeProcess は、JobOutput ファイル (および必要に応じて JobPreview ファイル) を返します。


次のコードに、ParallelTaskProcessor.RunExternalMergeProcess の単純な実装を示します。

	protected override JobResult RunExternalMergeProcess(
	    ITask mergeTask,
	    TaskExecutionSettings settings)
	{
	    var outputFile =  "output.zip";

	    var exePath =  ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("a -application {0} *.jpg", outputFile);

	    new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath
	    }.Run();

	    return new JobResult {
	        OutputFile = outputFile
	    };
	}

###Batch Apps へのジョブの送信
ジョブは、実行する必要があるワークロードを記述します。ジョブには、ファイルの内容を除いたワークロードに関するすべての情報を含める必要があります。たとえば、クライアントからジョブ スプリッターを介してタスクに至るまでの処理の構成設定をジョブに含めます。MSDN で提供されているサンプルには、ジョブを送信する方法や、Web ポータルとコマンド ライン クライアントを含む複数のクライアントを提供する方法が示されています。

<!---HONumber=August15_HO6-->