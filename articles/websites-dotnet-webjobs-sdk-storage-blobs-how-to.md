<properties 
	pageTitle="Web ジョブ SDK で Azure BLOB ストレージを使用する方法" 
	description="Web ジョブ SDK で Azure BLOB ストレージを使用する方法について説明します。新しい BLOB がコンテナーに表示されるとプロセスを開始し、 'poison blobs' を操作します。" 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="tdykstra"/>

# Web ジョブ SDK で Azure BLOB ストレージを使用する方法

このガイドでは、Azure BLOB が作成または更新されたときに、プロセスを開始する方法を示す C# コード サンプルを提供します。コード サンプルは [Web ジョブ SDK](websites-dotnet-webjobs-sdk.md) バージョン 1.x を使用します。

BLOB を作成する方法を示すコード サンプルの詳細については、[How to use Azure queue storage with the WebJobs SDK (Web ジョブ SDK で Azure キュー ストレージを使用する方法)](websites-dotnet-webjobs-sdk-storage-queues-how-to.md).  をご覧ください。
		
このガイドは、[ストレージ アカウントを示す接続文字列を使用して Visual Studio に Web ジョブ プロジェクトを作成する方法](websites-dotnet-webjobs-sdk-get-started.md). を理解していることを前提としています。

## 目次

-   [BLOB が作成または更新されたときに、関数をトリガーする方法](#trigger)
	- 拡張子を持つ BLOB 名の 1 つのプレースホルダー
	- 別の BLOB 名と拡張子のプレースホルダー
-   [BlobTrigger が連携する型](#types)
-   [文字列にバインドすることによってテキスト BLOB のコンテンツを取得](#string)
-   [ICloudBlobStreamBinder を使用して BLOB の内容をシリアル化](#icbsb)
-   [有害な BLOB の処理方法](#poison)
-   [BLOB のポーリング アルゴリズム](#polling)
-   [BLOB の配信確認メッセージ](#receipts)
-   [キューの記事で扱う関連トピック](#queues)
-   [次のステップ](#nextsteps)

## <a id="trigger"></a> BLOB が作成または更新されたときに、関数をトリガーする方法

このセクションでは、 `BlobTrigger` 属性を使用する方法について説明します。 

### 拡張子を持つ BLOB 名の 1 つのプレースホルダー  

次のコード サンプルは、 *input* コンテナーに表示されるテキスト BLOB を  *output* コンテナーにコピーします。

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("output/{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

属性のコンストラクターは、コンテナー名と BLOB 名のプレースホルダーを指定する文字列パラメーターを受け取ります。この例では、 *Blob1.txt* という名前の BLOB が  *input* コンテナーに作成されると、関数は  *Blob1.txt* という名前の BLOB を  *output* コンテナーに作成します。 

次のコード サンプルに示すように、BLOB 名のプレースホルダーを使用して名前のパターンを指定できます。

		public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
		    [Blob("output/copy-{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

このコードは「original-」で始まる名前を持つ BLOB のみをコピーします。たとえば、 *input* コンテナーの  *original-Blob1.txt* は、 *output* コンテナーの  *copy-Blob1.txt* にコピーされます。

名前に中括弧が付いた BLOB 名の名前のパターンを指定する必要がある場合は、中括弧を二重にします。たとえば、このような名前を持つ  *images* コンテナー内で BLOB を見つけるには次のようにします。

		{20140101}-soundfile.mp3

パターンにこれを使用します。

		images/{{20140101}}-{name}

この例では、 *name* プレースホルダー値は  *soundfile.mp3* です。 

### 別の BLOB 名と拡張子のプレースホルダー

次のコード サンプルでは、 *input* コンテナーに表示される BLOB を  *output* コンテナーにコピーするため、ファイル拡張子を変更します。コードは  *input* BLOB の拡張子をログに記録し、 *output* BLOB の拡張子を *.txt* に設定します。

		public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
		    [Blob("output/{name}.txt")] out string output,
		    string name,
		    string ext,
		    TextWriter logger)
		{
		    logger.WriteLine("Blob name:" + name);
		    logger.WriteLine("Blob extension:" + ext);
		    output = input.ReadToEnd();
		}

## <a id="types"></a> BLOB にバインドすることができる型

次の型には  `BlobTrigger` 属性を使用できます。

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* [ICloudBlobStreamBinder] によって逆シリアル化されるその他の型(#icbsb) 

Azure のストレージ アカウントを直接操作する場合は、 `CloudStorageAccount` パラメーターをメソッド シグネチャに追加することもできます。

## <a id="string"></a> 文字列にバインドすることによってテキスト BLOB のコンテンツを取得

テキスト BLOB の場合は、 `BlobTrigger` を  `string` パラメーターに適用できます。次のコード サンプルでは、テキスト BLOB を  `logMessage` という名前の  `string` パラメーターにバインドします。関数は、このパラメーターを使用して BLOB の内容を Web ジョブ SDK のダッシュボードに書き込みます。 
 
		public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
		    string name, 
		    TextWriter logger)
		{
		     logger.WriteLine("Blob name: {0}", name);
		     logger.WriteLine("Content:");
		     logger.WriteLine(logMessage);
		}

## <a id="icbsb"></a> ICloudBlobStreamBinder を使用して BLOB の内容をシリアル化

次のコード サンプルでは、 `ICloudBlobStreamBinder` を実装するクラスを使用して  `BlobTrigger` 属性を有効化して BLOB を  `WebImage` 型にバインドします。

		public static void WaterMark(
		    [BlobTrigger("images3/{name}")] WebImage input,
		    [Blob("images3-watermarked/{name}")] out WebImage output)
		{
		    output = input.AddTextWatermark("WebJobs SDK", 
		        horizontalAlign: "Center", verticalAlign: "Middle",
		        fontSize: 48, opacity: 50);
		}
		public static void Resize(
		    [BlobTrigger("images3-watermarked/{name}")] WebImage input,
		    [Blob("images3-resized/{name}")] out WebImage output)
		{
		    var width = 180;
		    var height = Convert.ToInt32(input.Height * 180 / input.Width);
		    output = input.Resize(width, height);
		}

 `WebImage` バインド コードは、 `ICloudBlobStreamBinder` から派生した  `WebImageBinder` クラスにあります。

		public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
		{
		    public Task<WebImage> ReadFromStreamAsync(Stream input, 
		        System.Threading.CancellationToken cancellationToken)
		    {
		        return Task.FromResult<WebImage>(new WebImage(input));
		    }
		    public Task WriteToStreamAsync(WebImage value, Stream output,
		        System.Threading.CancellationToken cancellationToken)
		    {
		        var bytes = value.GetBytes();
		        return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
		    }
		}

## <a id="poison"></a> 有害な BLOB の処理方法

 `BlobTrigger` 関数が失敗し、その障害が一時的なエラーが原因で発生した場合は、SDK が再度呼び出します。障害が BLOB の内容が原因で発生する場合は、関数は BLOB を処理するたびに失敗します。既定では、SDK は特定の BLOB に対して最大 5 回まで関数を呼び出します。5 回目の処理に失敗すると、SDK はメッセージを  *webjobs-blobtrigger-poison* という名前のキューに追加します。

再試行回数の最大値の設定は変更可能です。同様の [MaxDequeueCount](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#configqueue) 設定が、有害 BLOB の処理と有害キュー メッセージの処理にも使用されます。 

有害 BLOB のキュー メッセージは次のプロパティを含む JSON オブジェクトです。

* FunctionId (*{WebJob name}*.Functions.*{Function name}* 形式。例: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" または "PageBlob")
* ContainerName
* BlobName
* ETag (BLOB のバージョン識別子。例: "0x8D1DC6E70A277EF")

次のコード サンプルでは、 `CopyBlob` 関数は、呼び出されるたびに障害が発生する原因となるコードを含んでいます。SDK が再試行の最大回数の呼び出しを行うと、メッセージが有害 BLOB キューに作成され、そのメッセージは  `LogPoisonBlob` 関数によって処理されます。 

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("textblobs/output-{name}")] out string output)
		{
		    throw new Exception("Exception for testing poison blob handling");
		    output = input.ReadToEnd();
		}
		
		public static void LogPoisonBlob(
		[QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
		    TextWriter logger)
		{
		    logger.WriteLine("FunctionId: {0}", message.FunctionId);
		    logger.WriteLine("BlobType: {0}", message.BlobType);
		    logger.WriteLine("ContainerName: {0}", message.ContainerName);
		    logger.WriteLine("BlobName: {0}", message.BlobName);
		    logger.WriteLine("ETag: {0}", message.ETag);
		}

SDK は、JSON メッセージを自動的に逆シリアル化します。ここでは、 `PoisonBlobMessage` クラスです。 

		public class PoisonBlobMessage
		{
		    public string FunctionId { get; set; }
		    public string BlobType { get; set; }
		    public string ContainerName { get; set; }
		    public string BlobName { get; set; }
		    public string ETag { get; set; }
		}

### <a id="polling"></a> BLOB のポーリング アルゴリズム

Web ジョブ SDKは、アプリケーション起動時の  `BlobTrigger` 属性によって指定されたすべてのコンテナーをスキャンします。大容量のストレージ アカウントではこのスキャンには時間がかかる場合があります。そのため、新しい BLOB を見つけ、 `BlobTrigger` 関数が実行されるのに時間がかかる場合があります。

アプリケーションの起動後に、新しい BLOB または変更された BLOB を検出するために、SDK は定期的に BLOB ストレージのログから読み取ります。BLOB のログはバッファーされ、10 分程度ごとにのみ物理的に書き込まれます。そのため、対応する  `BlobTrigger` 関数が実行される前に BLOB が作成または更新されると、長い遅延が発生する場合があります。 

 `Blob` 属性を使用して作成する BLOB には例外があります。Web ジョブ SDK は新しい BLOB を作成すると、新しい BLOB を一致するすべての  `BlobTrigger` 関数に即座に渡します。そのため、一連の BLOB の入力と出力がある場合は、SDK はそれらを効率的に処理できます。しかし、他の方法で作成または更新された BLOB を低待機時間での BLOB 処理関数で実行する場合は、 `BlobTrigger` ではなく、 `QueueTrigger` を使用することを推奨します。

### <a id="receipts"></a> BLOB の配信確認メッセージ

Web ジョブ SDK で、 `BlobTrigger` 関数が同一の新しい BLOB または更新された BLOB に対して複数回呼び出されることはありません。これは特定の BLOB が処理されているかどうかを判断するために、 *blob receipts* を維持することによって実行されます。

BLOB の配信確認メッセージは、AzureWebJobsStorage 接続文字列によって指定された Azure のストレージ アカウントの  *azure-webjobs-hosts* という名前のコンテナーに格納されます。BLOB の配信確認メッセージには、次の情報が含まれています。

* BLOB に呼び出された関数 ("*{WebJob name}*.Functions.*{Function name}*"。例: "WebJob1.Functions.CopyBlob")
* コンテナー名
* BLOB の種類 ("BlockBlob" または "PageBlob")
* BLOB 名
* ETag (BLOB のバージョン識別子。例: "0x8D1DC6E70A277EF")

BLOB の再処理を強制する場合は、 *azure-webjobs-hosts* コンテナーから、その BLOB の配信確認メッセージを手動で削除できます。

## <a id="queues"></a>キューの記事で扱う関連トピック

キューのメッセージによってトリガーされる BLOB を処理する方法、または BLOB 処理に固有ではない Web ジョブ SDK シナリオの詳細については、[Web ジョブ SDK を使用して Azure キュー ストレージを使用する方法](websites-dotnet-webjobs-sdk-storage-queues-how-to.md).  をご覧ください。

この記事で取り上げている関連のトピックは、次のとおりです。

* Async 関数
* 複数のインスタンス
* 正常なシャットダウン
* 関数本体での Web ジョブ SDK 属性の使用
* コード内での SDK 接続文字列の設定
* コード内での Web ジョブ SDK コンストラクターのパラメーター値の設定
* 有害な BLOB の処理のための  `MaxDequeueCount` の構成
* 手動による関数のトリガー
* ログの記述

## <a id="nextsteps"></a> 次のステップ

このガイドでは、Azure BLOB を操作するための一般的なシナリオの処理方法を示すコード サンプルを提供しました。Azure Web ジョブと Web ジョブ SDK の使用方法の詳細については、[Azure Web Jobs Recommended Resources (Azure Web ジョブの推奨リソース)](http://go.microsoft.com/fwlink/?linkid=390226) をご覧ください。


<!--HONumber=42-->
