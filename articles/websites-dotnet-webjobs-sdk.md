<properties 
	pageTitle="Azure Web ジョブ SDK とは" 
	description="Azure Web ジョブ SDK の紹介です。この SDK の特徴、一般的な用途、コード サンプルについて説明します。" 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/12/2014" 
	ms.author="tdykstra"/>

# Azure Web ジョブ SDK とは

この記事では、Web ジョブ SDK の特徴を紹介すると共に、一般的な用途を確認し、コードでの使い方の概要を示します。

## 目次

- [概要](#overview)
- [シナリオ](#scenarios)
- [コード サンプル](#code)
- [Web ジョブ以外での Web ジョブ SDK の使用](#workerrole)
- [Web ジョブ SDK を使用した関数の呼び出し](#nostorage)
- [次のステップ](#nextsteps)

## <a id="overview"></a>概要

[Web ジョブ](web-sites-create-web-jobs.md) は、Web サイトと同じコンテキストでプログラムまたはスクリプトを実行できる Azure Websites の機能の 1 つです。Web ジョブ SDK の目的は、Web ジョブとして実行され、Azure Storage のキュー、BLOB、テーブルや、Service Bus キューと組み合わせて動作するコードを記述するタスクを簡素化することです。

Web ジョブ SDK には次のコンポーネントが含まれています。

* **NuGet パッケージ**。Visual Studio コンソール アプリケーション プロジェクトに追加する NuGet パッケージは、コードで Azure Storage サービスまたは Service Bus キューを操作するのフレームワークを提供します。   
  
* **ダッシュボード**。Web ジョブ SDK の一部は、Azure Websites に含まれており、NuGet パッケージを使用するプログラム向けに豊富な監視と診断の機能を提供します。これらの監視および診断機能を使用するためにコードを記述する必要はありません。

## <a id="scenarios"></a>シナリオ

ここでは、Azure Web ジョブ SDK を使用してより簡単に処理できる標準的なシナリオをいくつか取り上げます。

* 画像処理または CPU 負荷の高い作業。Web サイトの一般的な機能は、画像やビデオをアップロードする機能です。アップロード後にコンテンツを操作する必要が生じ、その作業の間ユーザーを待たせたくない場合がよくあります。

* キューの処理。Web フロントエンドがバックエンド サービスと通信する一般的な方法は、キューを使用することです。Web サイトは、処理を完了する必要がある場合に、メッセージをキューにプッシュします。バックエンド サービスは、キューからメッセージをプルし、処理を完了します。イメージ処理には、キューを使用できます。 たとえば、ユーザーがいくつかのファイルをアップロードした後、それらをバックエンドで取り出して処理できるように、ファイル名をキュー メッセージに格納します。また、キューを使用してサイトの応答性を改善できます。たとえば、SQL データベースに直接書き込む代わりに、キューに書き込んで、完了したことをユーザーに通知します。その間、バックエンド サービスでは、待ち時間の長いリレーショナル データベースの作業を処理できます。画像処理でのキューの処理の例については、[Web ジョブ SDK の使用に関するチュートリアル](websites-dotnet-webjobs-sdk-get-started.md). をご覧ください。

* RSS 情報集約。RSS フィードのリストを維持するサイトがある場合は、フィードからのすべての記事をバックグラウンド プロセスにプルできます。

* ログ ファイルの集計やクリーンアップなどのファイルのメンテナンス。分析ジョブを実行するために、いくつかのサイトによって作成されたログ ファイルや別々の期間に作成されたログ ファイルを組み合わせる作業が必要になる場合があります。また、使用していないログ ファイルをクリーンアップするために毎週実行するタスクをスケジュール設定する場合もあります。

* Azure テーブルへの取り込み。格納したファイルと BLOB を解析して、データをテーブルに格納したいと思っても、その取り込み用の関数を作るには、大量のコードの記述が必要になることがあります (場合によっては数百万行)。Web ジョブ SDK を利用すれば、この機能を簡単に実装できます。また、この SDK なら、テーブルに書き込まれた行の数など、進行状況インジケーターのリアルタイム監視も実現できます。

* [電子メールの送信](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure) など、バックグラウンド スレッドで実行したいその他の時間のかかるタスク。 

## <a id="code"></a> コード サンプル

Azure ストレージと組み合わせて動作する標準的なタスクを処理するコードは、シンプルです。コンソール アプリケーションで、実行するバックグラウンド タスクのメソッドを記述し、Web ジョブ SDK の属性でそれらを装飾します。 `Main`メソッドは記述したメソッドの呼び出しを調整する  `JobHost` オブジェクトを作成します。Web ジョブ SDK フレームワークは、メソッド内で使用した WebJobs SDK 属性に基づいて、そのメソッドを呼び出すタイミングを認識します。 

次に示すのは、キューをポーリングして、受信したキュー メッセージごとに BLOB を作成する単純なプログラムです。

		public static void Main()
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}

		public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
		{
		    writer.WriteLine(inputText);
		}

 `JobHost` オブジェクトは、バックグラウンドの関数セット用のコンテナーです。 `JobHost` オブジェクトは、関数を監視し、それらをトリガーするイベントを待機して、トリガー イベントが発生した時点でその関数を実行します。 `JobHost` メソッドを呼び出すと、コンテナー プロセスを現在のスレッドまたはバックグラウンド スレッドのどちらで実行するかを指定できます。例では、 `RunAndBlock` メソッドは、現在のスレッド上でプロセスを継続的に実行しています。

この例の  `ProcessQueueMessage` メソッドには  `QueueTrigger` 属性があるため、新規キュー メッセージの受信がその関数のトリガーとなります。 `JobHost` オブジェクトは指定されたキュー (このサンプルでは "webjobsqueue") で新規キュー メッセージを待機し、それを見つけた時点で、 `ProcessQueueMessage` を呼び出します。 `QueueTrigger` 属性も  `inputText` パラメーターをキュー メッセージの値にバインドするようフレームワークに通知します。 

<pre class="prettyprint">public static void ProcessQueueMessage([QueueTrigger(&quot;webjobsqueue&quot;)]] <mark>string inputText</mark>, 
    [Blob("containername/blobname")]TextWriter writer)</pre>

さらに、フレームワークは、 `TextWriter` オブジェクトを "containername" というコンテナー内の "blobname" という BLOB にバインドします。

<pre class="prettyprint">public static void ProcessQueueMessage([QueueTrigger(&quot;webjobsqueue&quot;)]] string inputText, 
    <mark>[Blob("containername/blobname")]TextWriter writer</mark>)</pre>

その後、関数はこれらのパラメーターを使用して、キュー メッセージの値を BLOB に書き込みます。

		writer.WriteLine(inputText);

Web ジョブ SDK のトリガー機能とバインダー機能は、Azure Storage キューと Service Bus キューを操作するために記述する必要のあるコードを大幅に簡素化します。キューと BLOB のプロセスを処理するために必要な低レベルのコードは Web ジョブ SDK フレームワークによって実行されます。フレームワークは、まだ存在していないキューの作成、キューを開く、キュー メッセージの読み込み、処理の完了時のキュー メッセージの削除、まだ存在しない BLOB コンテナーの作成、BLOB への書き込みなどを処理します。

Web ジョブ SDK は Azure ストレージを操作するための多数の方法を提供しています。たとえば、 `QueueTrigger` 属性で装飾したパラメーターがバイト配列またはカスタム型の場合は、JSON から自動的に逆シリアル化されます。さらに、 `BlobTrigger` 属性を使用して、新しい BLOB が Azure Storage アカウントで作成されたときに常にプロセスがトリガーされるように指定できます。( `QueueTrigger` は新しいキュー メッセージを数秒で見つけますが、 `BlobTrigger` が新しい BLOB を検出するまで最大 20 分かかる場合がある点にご注意ください。 `BlobTrigger` は  `JobHost` が起動したときは常に BLOB をスキャンし、その後、新しい BLOB を検出するために定期的に Azure ストレージ ログを確認します)。

## <a id="workerrole"></a>Web ジョブ以外での Web ジョブ SDK の使用

Web ジョブ SDK を使用するプログラムは標準的なコンソール アプリケーションであるため、場所を問わず実行できます。Web ジョブとして実行する必要はありません。ローカルの開発用コンピューターでプログラムをテストし、運用段階では、好みに応じてクラウド サービスの worker ロールまたは Windows サービスで実行できます。 

ただし、ダッシュボードは Azure Web サイトのサイト拡張機能としてのみ使用可能です。Web ジョブ以外で実行する際にもダッシュボードを使いたい場合は、Web ジョブ SDK ダッシュボードの接続文字列が参照するストレージ アカウントを使用するように Azure Web サイトを構成できます。そうすれば、そのサイトの Web ジョブ ダッシュボードに、別の場所で実行されているプログラムから関数の実行に関するデータが表示されます。URL https://*{websitename}*.scm.azurewebsites.net/azurejobs/#/functions を使用してダッシュボードにアクセスできます。詳細については、[Getting a dashboard for local development with the WebJobs SDK (Web ジョブ SDK を使用したローカル開発用ダッシュボードへのアクセス)](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx) をご覧ください。ただし、このブログ記事には、既に使用されていない接続文字列名が示されている点にご注意ください。 

## <a id="nostorage"></a>Web ジョブ SDK を使用した関数の呼び出し

Azure Storage のキュー、テーブル、BLOB や、Service Bus キューを直接操作する必要がない場合でも、Web ジョブ SDK にはいくつかの利点があります。

* ダッシュボードから関数を呼び出すことができる。
* ダッシュボードから関数を再生することができる。
* 特定の Web ジョブにリンクされたログ (アプリケーション ログ) や、ログを生成した特定の関数呼び出しにリンクされたログ(`TextWriter` パラメーター ログ) をダッシュボードで確認できる。 

* 詳細については、[関数を手動でトリガーする方法](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#manual) と [ログを記述する方法](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs)  をご覧ください。

## <a id="nextsteps"></a>次のステップ

Web ジョブ SDK の詳細については、[Azure WebJobs Recommended Resources (Azure Web ジョブの推奨リソース)](http://go.microsoft.com/fwlink/?linkid=390226) をご覧ください。



<!--HONumber=42-->
