<properties 
	pageTitle=".NET Service Bus API で AMQP 1.0 を使用する方法 - Azure" 
	description="Azure .NET Service Bus API で Advanced Message Queuing Protodol (AMQP) 1.0 を使用する方法について説明します。" 
	services="service-bus" 
	documentationCenter=".net" 
	authors="sethmanheim" 
	manager="timlt" 
	editor="mattshel"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="sethm"/>






# .NET サービス バス API で AMQP 1.0 を使用する方法

## はじめに

Advanced Message Queuing Protocol (AMQP) 1.0 は、堅牢なクロスプラットフォーム メッセージング アプリケーションを作成するために使用できる、効率的で信頼性の高い回線レベルのメッセージング プロトコルです。

サービス バスでの AMQP 1.0 のサポートにより、仲介型メッセージング機能 (キューとトピック発行/サブスクライブ) をさまざまなプラットフォームから効率的なバイナリ プロトコルを使って利用できます。さらに、さまざまな言語、フレームワーク、オペレーティング システムを使って作成されたコンポーネントで構成されたアプリケーションを作成できます。

このガイドでは、Service Bus .NET API を使って .NET アプリケーションからサービス バスの仲介型メッセージング機能 (キューとトピック発行/サブスクライブ) を使用する方法を説明します。標準 Java Message Service (JMS) API を使用して同じ作業を実行する方法が説明されているハウツー ガイドがあります。これら 2 種類のガイドを使用して、AMQP 1.0 を使用したクロスプラットフォームのメッセージングについて学習できます。

## Service Bus の概要

このガイドは、"queue1" という名前のキューが含まれているサービス バス名前空間が既にあることを前提としています。まだない場合は、[Azure 管理ポータル](http://manage.windowsazure.com)を使用して名前空間とキューを作成できます。サービス バス 名前空間とキューの作成方法の詳細については、「[サービス バス キューの使用方法](https://azure.microsoft.com/develop/net/how-to-guides/service-bus-queues/)」をご覧ください。

## Service Bus SDK のダウンロード

AMQP 1.0 のサポートは、Service Bus SDK バージョン 2.1 以降で使用できます。最新の SDK は、NuGet ([http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/)) からダウンロードできます。

## .NET アプリケーションのコーディング

既定では、Service Bus .NET クライアント ライブラリは SOAP ベースの専用プロトコルを使ってサービス バス サービスと通信します。既定のプロトコルの代わりに AMQP 1.0 を使用するには、次のセクションの説明に従って、サービス バス接続文字列を明示的に構成する必要があります。AMQP 1.0 を使用する際、アプリケーション コードはこの変更以外には基本的に変更されません。

現在のリリースでは、AMQP を使用する際にサポートされていない API 機能がいくつかあります。サポートされていない機能については、後ほど「サポートされていない機能と制限」で紹介します。そのほか、AMQP を使用すると意味が変わる詳細な構成設定もいくつかあります。それらの設定は、この短いガイドでは使用しませんが、「[Service Bus AMQP: 開発者ガイド](http://msdn.microsoft.com/library/jj841071.aspx)」で詳しく説明されています。

### App.config による構成

アプリケーションの設定は、App.config 構成ファイルを使って保存することをお勧めします。Service Bus アプリケーションでは、App.config を使って Service Bus の **ConnectionString** を保存できます。このサンプル アプリケーションでは、App.config を使って、使用する Service Bus メッセージング エンティティの名前も保存します。

App.config ファイルの例を以下に示します。

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	  	<appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
        	     value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
	    	<add key="EntityName" value="queue1" />
  		</appSettings>
	</configuration>

### Service Bus 接続文字列の構成

**Microsoft.ServiceBus.ConnectionString** 設定の値は、Service Bus への接続を構成するために使用される Service Bus 接続文字列です。その形式は次のとおりです。

	Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

[namespace] と [SAS key] は、Azure 管理ポータルから取得します。詳細については、「[Service Bus キューの使用方法][]」をご覧ください。

AMQP を使用するときには、接続文字列の末尾に ";TransportType=Amqp" を追加します。これで、AMQP 1.0 を使ってサービス バスに接続するようにクライアント ライブラリに通知されます。

### エンティティ名の構成

このサンプル アプリケーションでは、App.config ファイルの **appSettings** セクションにある "EntityName" の設定を使って、メッセージ交換に使用するキューの名前を構成します。

### Service Bus キューを使用するシンプルな .NET アプリケーション

Service Bus キューとの間でメッセージを送受信する例を次に示します。

	// SimpleSenderReceiver.cs
	
	using System;
	using System.Configuration;
	using System.Threading;
	using Microsoft.ServiceBus.Messaging;
	
	namespace SimpleSenderReceiver
	{
	    class SimpleSenderReceiver
	    {
	        private static string connectionString;
	        private static string entityName;
	        private static Boolean runReceiver = true;
	        private MessagingFactory factory;
	        private MessageSender sender;
	        private MessageReceiver receiver;
	        private MessageListener messageListener;
	        private Thread listenerThread;
	
	        static void Main(string[] args)
	        {
	            try
	            {
	                if ((args.Length > 0) && args[0].ToLower().Equals("sendonly"))
	                    runReceiver = false;
	                
	                string ConnectionStringKey = "Microsoft.ServiceBus.ConnectionString";
	                string entityNameKey = "EntityName";
	                entityName = ConfigurationManager.AppSettings[entityNameKey];
	                connectionString = ConfigurationManager.AppSettings[ConnectionStringKey];
	                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
	
	                Console.WriteLine("Press [enter] to send a message. " +
	                    "Type 'exit' + [enter] to quit.");
	
	                while (true)
	                {
	                    string s = Console.ReadLine();
	                    if (s.ToLower().Equals("exit"))
	                    {
	                        simpleSenderReceiver.Close();
	                        System.Environment.Exit(0);
	                    }
	                    else
	                        simpleSenderReceiver.SendMessage();
	                }
	            }
	            catch (Exception e)
	            {
	                Console.WriteLine("Caught exception: " + e.Message);
	            }
	        }
	
	        public SimpleSenderReceiver()
	        {
	            factory = MessagingFactory.CreateFromConnectionString(connectionString);
	            sender = factory.CreateMessageSender(entityName);
	
	            if (runReceiver)
	            {
	                receiver = factory.CreateMessageReceiver(entityName);
	                messageListener = new MessageListener(receiver);
	                listenerThread = new Thread(messageListener.Listen);
	                listenerThread.Start();
	            }
	        }
	
	        public void Close()
	        {
	            messageListener.RequestStop();
	            listenerThread.Join();
	            factory.Close();
	        }
	
	        private void SendMessage()
	        {
	            BrokeredMessage message = 
	                new BrokeredMessage("Test AMQP message from .NET");
	            sender.Send(message);
	            Console.WriteLine("Sent message with MessageID = " 
	                + message.MessageId);
	        }
	
	    }
	
	    public class MessageListener
	    {
	        private MessageReceiver messageReceiver;
	        public MessageListener(MessageReceiver receiver)
	        {
	            messageReceiver = receiver;
	        }
	
	        public void Listen()
	        {
	            while (!_shouldStop)
	            {
	                try
	                {
	                    BrokeredMessage message = 
	                        messageReceiver.Receive(new TimeSpan(0, 0, 10));
	                    if (message != null)
	                    {
	                        Console.WriteLine("Received message with MessageID = " + 
	                            message.MessageId);
	                        message.Complete();
	                    }
	                }
	                catch (Exception e)
	                {
	                    Console.WriteLine("Caught exception: " + e.Message);
	                    break;
	                }
	            }
	        }
	
	        public void RequestStop()
	        {
	            _shouldStop = true;
	        }
	
	        private volatile bool _shouldStop;
	    }
	}

### アプリケーションの実行

このアプリケーションを実行すると、次の形式の出力が生成されます。

	> SimpleSenderReceiver.exe
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	
	Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
	Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
	
	Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
	Received message with MessageID = d00e2e088f06416da7956b58310f7a06
	
	Received message with MessageID = f27f79ec124548c196fd0db8544bca49
	Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
	exit

## JMS と .NET の間のクロスプラットフォーム メッセージング

これまでに、.NET を使ってサービス バスにメッセージを送信する方法と、.NET を使ってそれらのメッセージを受信する方法を紹介しました。しかし、AMQP 1.0 の主な利点の 1 つは、さまざまな言語で書かれたコンポーネントからアプリケーションを作成して、高い信頼性と完全な忠実度でメッセージ交換を行えることにあります。

上で説明したサンプル .NET アプリケーションと、Java 版のガイド ([サービス バスと AMQP 1.0 で Java Message Service (JMS) API を使用する方法に関するページ](http://aka.ms/ll1fm3)) に含まれている同様の Java アプリケーションを使用すると、.NET と Java の間でメッセージを交換できます。 

サービス バスと AMQP 1.0 を使ったクロスプラットフォーム メッセージングの詳細については、「[Service Bus AMQP 1.0 開発者ガイド](http://msdn.microsoft.com/library/jj841071.aspx)」をご覧ください。

### JMS から .NET

JMS から .NET のメッセージングを試してみるには、次の手順を実行します。

* .NET サンプル アプリケーションを、コマンド ライン引数を使わずに起動します。
* Java サンプル アプリケーションを、"sendonly" コマンド ライン引数を使って起動します。このモードでは、アプリケーションはキューからメッセージを受信せずに送信のみを行います。
* Java アプリケーションのコンソールで **Enter** キーを何度か押します。メッセージが送信されます。
* それらのメッセージが .NET アプリケーションによって受信されます。

### JMS アプリケーションの出力

	> java SimpleSenderReceiver sendonly
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Sent message with JMSMessageID = ID:4364096528752411591
	Sent message with JMSMessageID = ID:459252991689389983
	Sent message with JMSMessageID = ID:1565011046230456854
	exit

### .NET アプリケーションの出力

	> SimpleSenderReceiver.exe	
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Received message with MessageID = 4364096528752411591
	Received message with MessageID = 459252991689389983
	Received message with MessageID = 1565011046230456854
	exit

## .NET から JMS

.NET から JMS のメッセージングを試してみるには、次の手順を実行します。

* .NET サンプル アプリケーションを、"sendonly" コマンド ライン引数を使って起動します。このモードでは、アプリケーションはキューからメッセージを受信せずに送信のみを行います。
* Java サンプル アプリケーションを、コマンド ライン引数を使わずに起動します。
* .NET アプリケーションのコンソールで **Enter** キーを何度か押します。メッセージが送信されます。
* それらのメッセージが Java アプリケーションによって受信されます。

#### .NET アプリケーションの出力

	> SimpleSenderReceiver.exe sendonly
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3	
	Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
	Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
	exit


#### JMS アプリケーションの出力

	> java SimpleSenderReceiver	
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
	Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
	Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
	exit

<h2>サポートされていない機能と制限</h2>

.NET Service Bus API の次の機能は、現在のところ AMQP の使用時はサポートされていません。

* トランザクション
* 転送先を経由した送信
* メッセージ シーケンス番号による受信
* メッセージとセッションの参照
* セッションの状態
* バッチ ベースの API
* 受信のスケールアウト
* サブスクリプション ルールの実行時間の操作
* セッション ロックの更新
* いくつかの動作の小さな違い

詳細については、「[Service Bus AMQP 1.0 開発者ガイド](http://msdn.microsoft.com/library/jj841071.aspx)」をご覧ください。このトピックには、サポートされていない API の詳細なリストが含まれています。

<h2>まとめ</h2>

このガイドでは、AMQP 1.0 と Service Bus .NET API を使って .NET からサービス バスの仲介型メッセージング機能 (キューとトピック発行/サブスクライブ) を使用する方法を説明しました。

Service Bus AMQP 1.0 は、Java、C、Python、PHP など、その他の言語からも使用できます。Service Bus で AMQP 1.0 を使用すると、これらの言語を使って作成されたコンポーネントで信頼性と正確性の高いメッセージ交換を実現できます。詳細については、「[Service Bus AMQP: 開発者ガイド](http://msdn.microsoft.com/library/jj841071.aspx)」をご覧ください。

<h2>詳細情報</h2>

* [Service Bus での AMQP 1.0 サポート](http://aka.ms/pgr3dp)
* [サービス バスと AMQP 1.0 で Java Message Service (JMS) API を使用する方法](http://aka.ms/ll1fm3)
* [Service Bus AMQP 1.0: 開発者ガイド](http://msdn.microsoft.com/library/jj841071.aspx)
* [Service Bus キューの使用方法](http://azure.microsoft.com/develop/net/how-to-guides/service-bus-queues/)

[Service Bus キューの使用方法]: http://azure.microsoft.com/develop/net/how-to-guides/service-bus-queues/

<!--HONumber=47-->
