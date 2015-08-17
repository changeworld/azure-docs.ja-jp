<properties 
	pageTitle="ストレージ アクセス キーをローリングした後に Media Services を更新する" 
	description="この記事は、ストレージ アクセス キーをローリングした後に Media Services を更新する際のガイダンスについて説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>

#方法: ストレージ アクセス キーをローリングした後に Media Services を更新する

新しく Azure Media Services アカウントを作成すると、メディア コンテンツの保存に使用する Azure Storage アカウントも選択するよう求めるメッセージが表示されます。Media Services アカウントには複数のストレージ アカウントを追加できます。

新しいストレージ アカウントが作成されると、Azure は、ストレージ アカウントへのアクセス認証に使用される 512 ビット ストレージ アクセス キーを 2 つ生成します。ストレージの接続の安全性を高めるため、ストレージ アクセス キーを定期的に再生成して入れ換えることをお勧めします。片方のアクセス キーでストレージ アカウントに接続したまま、もう片方のアクセス キーを再生成できるように、アクセス キーは 2 つ (プライマリとセカンダリ) 提供されます。この手順は、「アクセス キーのローリング」とも呼ばれます。

Media Services は、1 つのストレージ キー (プライマリまたはセカンダリ) に依存します。特に、アセットのストリーミングやダウンロードに使用されるロケーターはアクセス キーに依存します。ストレージ アクセス キーをローリングする際は、ストリーミング サービスが中断されないようにロケーターも更新する必要があります。

>[AZURE.NOTE]ストレージ キーを再生成した後は、必ず、更新を Media Services と同期するようにしてください。

このトピックでは、適切なストレージ キーを使用するために、ストレージ キーをローリングして Media Services を更新する手順について説明しています。ストレージ アカウントが複数ある場合、この手順はストレージ アカウントごとに実行する必要があることにご注意ください。

>[AZURE.NOTE]このトピックで説明する手順を実稼働アカウントで実行する前に、実稼働前のアカウントでテストするようにしてください。


## 手順 1: セカンダリ ストレージ アクセス キーを再生成する

セカンダリ ストレージ キーの再生成から開始します。既定では、セカンダリ キーは Media Services で使用されません。ストレージ キーをローリングする方法の詳細については、「[方法: ストレージ アクセス キーの表示、コピーおよび再生成](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)」を参照してください。
  
##<a id="step2"></a>手順 2: Media Services を更新して新しいセカンダリ ストレージ キーを使用する

Media Services を更新してセカンダリ ストレージ アクセス キーを使用します。再生成されたストレージ キーを Media Services と同期するには、次の 2 つの方法のどちらかを使用できます。

- Azure ポータルを使用する場合: Media Service アカウントを選択し、ポータル ウィンドウの下部にある [キーの管理] アイコンをクリックします。Media Services で同期するストレージ キーに応じて、同期するキーをプライマリ キーとセカンダリ キーのどちらかに指定します。この例では、セカンダリ キーを使用します。

- Media Services Management REST API を使用します。

	次のコード例では、指定したストレージ キーを Media Services と同期するために、https://endpoint/<subscriptionId>/services/mediaservices/Accounts/<accountName>/StorageAccounts/<storageAccountName>/Key 要求を作成する方法を示します。この例では、セカンダリ ストレージ キーの値が使用されます。詳細については、「[方法: Media Services Management REST API の使用](http://msdn.microsoft.com/library/azure/dn167656.aspx)」を参照してください。
 
		public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
		{
		    var clientCert = GetCertificate(CertThumbprint);
		
		    HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
		        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
		    request.Method = "PUT";
		    request.ContentType = "application/json; charset=utf-8";
		    request.Headers.Add("x-ms-version", "2011-10-01");
		    request.Headers.Add("Accept-Encoding: gzip, deflate");
		    request.ClientCertificates.Add(clientCert);
		
		
		    using (var streamWriter = new StreamWriter(request.GetRequestStream()))
		    {
		        streamWriter.Write(""");
		        streamWriter.Write(storageAccountKey);
		        streamWriter.Write(""");
		        streamWriter.Flush();
		    }
		
		    using (var response = (HttpWebResponse)request.GetResponse())
		    {
		        string jsonResponse;
		        Stream receiveStream = response.GetResponseStream();
		        Encoding encode = Encoding.GetEncoding("utf-8");
		        if (receiveStream != null)
		        {
		            var readStream = new StreamReader(receiveStream, encode);
		            jsonResponse = readStream.ReadToEnd();
		        }
		    }
		}

この後に、既存のロケーター (古いストレージ キーに依存しているロケーター) を更新します。

>[AZURE.NOTE]保留中のジョブに影響しないよう、30 分待ってから、Media Services で操作を行います (新しいロケーターの作成など)。

##手順 3: ロケーターを更新する 

30 分が経過したら、既存のロケーターを更新して、新しいセカンダリ ストレージ キーとの依存関係を確立できます。

ロケーターの有効期限を更新するには、[REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) または [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API を使用します。SAS ロケーターの有効期限を更新すると、URL が変更されることにご注意ください。

##手順 5: プライマリ ストレージ アクセス キーを再生成する

プライマリ ストレージ アクセス キーを再生成します。ストレージ キーをローリングする方法の詳細については、「[方法: ストレージ アクセス キーの表示、コピーおよび再生成](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)」を参照してください。

##手順 6: Media Services を更新して新しいプライマリ ストレージ キーを使用する
	
[手順 2](media-services-roll-storage-access-keys.md#step2) と同じ手順を使用しますが、ここで Media Services アカウントと同期するのは新しいプライマリ ストレージ アクセス キーです。

>[AZURE.NOTE]保留中のジョブに影響しないよう、30 分待ってから、Media Services で操作を行います (新しいロケーターの作成など)。

##手順 7: ロケーターを更新する  

30 分が経過したら、既存のロケーターを更新して、新しいプライマリ ストレージ キーとの依存関係を確立できます。

ロケーターの有効期限を更新するには、[REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) または [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API を使用します。SAS ロケーターの有効期限を更新すると、URL が変更されることにご注意ください。

 
 

<!---HONumber=August15_HO6-->