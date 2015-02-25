<properties urlDisplayName="How to use Notification Hubs with Java" pageTitle="Java から Notification Hubs を使用する方法" metaKeywords="" description="Java バックエンドから Azure Notification Hubs を使用する方法について説明します。" metaCanonical="" services="mobile-services,notification-hubs,push,java" documentationCenter="" title="How to use Notification Hubs with Java" authors="piyushjo" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="java" ms.topic="article" ms.date="11/14/2014" ms.author="piyushjo" />

# Java/PHP から Notification Hubs を使用する方法
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/ja-jp/documentation/articles/notification-hubs-java-backend-how-to/" title="Java" class="current">Java</a><a href="/ja-jp/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a>
</div>

MSDN のトピック「[通知ハブの REST API](http://msdn.microsoft.com/ja-jp/library/dn223264.aspx)」の説明にあるように Notification Hub REST インターフェイスを使用して、Java/PHP/Ruby バックエンドから Notification Hubs のすべての機能にアクセスできます。

このトピックでは、次の方法について説明します。

* Java で Notification Hubs 機能の REST クライアントを記述します。
* 選択したモバイル プラットフォームの「[通知ハブの使用](http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-ios-get-started/) 」に従って、Java のバックエンド部分を実装します。

## <a name="client-interface"></a>クライアント インターフェイス
メイン クライアント インターフェイスは、[.NET Notification Hubs SDK](http://msdn.microsoft.com/ja-jp/library/jj933431.aspx) で利用可能なものと同じメソッドを提供します。これにより、このサイトで現在利用できる、およびインターネットのコミュニティによって随時追加されるすべてのチュートリアルとサンプルを直接変換できます。

「[Java REST wrapper sample (Java REST ラッパー サンプル)]」で利用可能なすべてのコードを検索できます。

たとえば、クライアントを作成する場合:

	new NotificationHub("connection string", "hubname");	

iOS 登録を作成する場合 (Windows、Android、Windows Phone、Kindle Fire でも同様):

	String id = hub.createRegistrationId();
	AppleRegistration reg = new AppleRegistration(id, DEVICETOKEN);
	reg.getTags().add("myTag");
	reg.getTags().add("myOtherTag");
	hub.upsertRegistration(reg);

iOS ネイティブ通知を送信する場合:
	
	Notification n = Notification.createAppleNotifiation("APNS body");
	hub.sendNotification(n);

## <a name="implementation"></a>実装
まだ実施していない場合は、「[通知ハブの使用]」に従って最後のセクションまで進み、バックエンドを実装します。
また、必要があれば「[Java REST wrapper sample (Java REST ラッパー サンプル)]」のコードを使用し、直接「[チュートリアルを完了する](#complete-tutorial) 」セクションに進むこともできます。

REST ラッパーすべての実装の詳細については、[MSDN の記事](http://msdn.microsoft.com/ja-jp/library/dn530746.aspx)を参照してください。このセクションでは、Notification Hubs REST エンドポイントにアクセスするために必要な主要手順の Java 実装について説明します。

1. 接続文字列を解析する
2. 認証トークンを生成する
3. HTTP 呼び出しを実行する

以下に示すスニペットでは、次のコンポーネントを利用します。

* [Apache HttpComponents](http://hc.apache.org/httpcomponents-client-ga/)
* [Apache Commons-Codec](http://commons.apache.org/proper/commons-codec/)
* [Apache Commons-Io](http://commons.apache.org/proper/commons-io/)

### 接続文字列を解析する

接続文字列を解析するコンストラクターを持つクライアントを実装するメイン クラスです。

	public class NotificationHub {

		private static final String APIVERSION = "?api-version=2013-10";
		private static final String CONTENT_LOCATION_HEADER = "Location";
		private String endpoint;
		private String hubPath;
		private String SasKeyName;
		private String SasKeyValue;
	
		private HttpClient httpClient;
	
		public NotificationHub(String connectionString, String hubPath) {
			this.httpClient = HttpClients.createDefault();
			this.hubPath = hubPath;
	
			String[] parts = connectionString.split(";");
			if (parts.length != 3)
				throw new RuntimeException("Error parsing connection string: "
						+ connectionString);
	
			for (int i = 0; i < parts.length; i++) {
				if (parts[i].startsWith("Endpoint")) {
					this.endpoint = "https" + parts[i].substring(11);
				} else if (parts[i].startsWith("SharedAccessKeyName")) {
					this.SasKeyName = parts[i].substring(20);
				} else if (parts[i].startsWith("SharedAccessKey")) {
					this.SasKeyValue = parts[i].substring(16);
				}
			}
		}
	}


### セキュリティ トークンを作成する
セキュリティ トークン作成についての詳細は、[ここ](http://msdn.microsoft.com/ja-jp/library/dn495627.aspx)で参照できます。
現在の要求の URI および接続文字列から抽出した資格情報に基づいてトークンを作成するには、次のメソッドを **NotificationHub** クラスに追加する必要があります。

	private String generateSasToken(URI uri) {
		String targetUri;
		try {
			targetUri = URLEncoder
					.encode(uri.toString().toLowerCase(), "UTF-8")
					.toLowerCase();

			long expiresOnDate = System.currentTimeMillis();
			int expiresInMins = 60; // 1 hour
			expiresOnDate += expiresInMins * 60 * 1000;
			long expires = expiresOnDate / 1000;
			String toSign = targetUri + "\n" + expires;

			// Get an hmac_sha1 key from the raw key bytes
			byte[] keyBytes = SasKeyValue.getBytes("UTF-8");
			SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

			// Get an hmac_sha1 Mac instance and initialize with the signing key
			Mac mac = Mac.getInstance("HmacSHA256");
			mac.init(signingKey);

			// Compute the hmac on input data bytes
			byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

			// Convert raw bytes to Hex
			String signature = URLEncoder.encode(
					Base64.encodeBase64String(rawHmac), "UTF-8");

			// construct authorization string
			String token = "SharedAccessSignature sr=" + targetUri + "&sig="
					+ signature + "&se=" + expires + "&skn=" + SasKeyName;
			return token;
		} catch (Exception e) {
			throw new RuntimeException(e);
		}
	}

### 通知を送信する
最初に、通知を表すクラスの定義を使用します。

	import java.util.HashMap;
	import java.util.Iterator;
	import java.util.Map;
	import org.apache.http.entity.ContentType;

	public class Notification {
		private Map<String, String> headers = new HashMap<String, String>();
		private String body;
		private ContentType contentType;
	
		public static Notification createWindowsNotification(String body) {
			Notification n = new Notification();
			n.body = body;
			n.headers.put("ServiceBusNotification-Format", "windows");
	
			if (body.contains("<toast>"))
				n.headers.put("X-WNS-Type", "wns/toast");
			if (body.contains("<tile>"))
				n.headers.put("X-WNS-Type", "wns/tile");
			if (body.contains("<badge>"))
				n.headers.put("X-WNS-Type", "wns/badge");
			if (body.startsWith("<")) {
				n.contentType = ContentType.APPLICATION_XML;
			}
			return n;
		}
	
		public static Notification createAppleNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "apple");
			return n;
		}
	
		public static Notification createGcmNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "gcm");
			return n;
		}

		public static Notification createAdmNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "adm");
			return n;
		}

		public static Notification createMpnsNotifiation(String body) {
			Notification n = new Notification();
			n.body = body;
			n.headers.put("ServiceBusNotification-Format", "windowsphone");
	
			if (body.contains("<wp:Toast>")) {
				n.headers.put("X-WindowsPhone-Target", "toast");
				n.headers.put("X-NotificationClass", "2");
			}
			if (body.contains("<wp:Tile>")) {
				n.headers.put("X-WindowsPhone-Target", "tile");
				n.headers.put("X-NotificationClass", "1");
			}
			if (body.startsWith("<")) {
				n.contentType = ContentType.APPLICATION_XML;
			}
			return n;
		}
	
		public static Notification createTemplateNotification(
				Map<String, String> properties) {
			Notification n = new Notification();
			StringBuffer buf = new StringBuffer();
			buf.append("{");
			for (Iterator<String> iterator = properties.keySet().iterator(); iterator
					.hasNext();) {
				String key = iterator.next();
				buf.append("\"" + key + "\":\"" + properties.get(key) + "\"");
				if (iterator.hasNext())
					buf.append(",");
			}
			buf.append("}");
			n.body = buf.toString();
			n.contentType = ContentType.APPLICATION_JSON;
			n.headers.put("ServiceBusNotification-Format", "template");
			return n;
		}
	
		public Map<String, String> getHeaders() { return headers; }
	
		public void setHeaders(Map<String, String> headers) { this.headers = headers; }
	
		public String getBody() { return body; }
	
		public void setBody(String body) { this.body = body; }
	
		public ContentType getContentType() { return contentType; }
	
		public void setContentType(ContentType contentType) { this.contentType = contentType; }
	}

このクラスは、ネイティブ通知本体のコンテナー、またはテンプレート通知の場合にはプロパティのセットと、形式 (ネイティブ プラットフォームまたはテンプレート) およびプラットフォーム固有のプロパティを含むヘッダーのセットです (Apple 有効期限プロパティや WNS ヘッダーなど)。また、一般的に使用する通知タイプを生成するのに便利なコンストラクターも定義します。

利用可能なすべてのオプションについては、[Notification Hubs REST API のドキュメント](http://msdn.microsoft.com/ja-jp/library/dn495827.aspx) および特定の通知プラットフォームの形式を参照してください。

このクラスを利用して、**NotificationHub** クラス内に送信通知メソッドを作成できます。

	public void sendNotification(Notification notification) {
		sendNotification(notification, "");
	}

	public void sendNotification(Notification notification, Set<String> tags) {
		if (tags.isEmpty())
			throw new IllegalArgumentException(
					"tags has to contain at least an element");

		StringBuffer exp = new StringBuffer();
		for (Iterator<String> iterator = tags.iterator(); iterator.hasNext();) {
			exp.append(iterator.next());
			if (iterator.hasNext())
				exp.append(" || ");
		}

		sendNotification(notification, exp.toString());
	}

	public void sendNotification(Notification notification, String tagExpression) {
		HttpPost post = null;
		try {
			URI uri = new URI(endpoint + hubPath + "/messages" + APIVERSION);
			post = new HttpPost(uri);
			post.setHeader("Authorization", generateSasToken(uri));

			if (tagExpression != null && !"".equals(tagExpression)) {
				post.setHeader("ServiceBusNotification-Tags", tagExpression);
			}

			for (String header : notification.getHeaders().keySet()) {
				post.setHeader(header, notification.getHeaders().get(header));
			}

			post.setEntity(new StringEntity(notification.getBody()));
			HttpResponse response = httpClient.execute(post);

			if (response.getStatusLine().getStatusCode() != 201) {
				String msg = "";
				if (response.getEntity() != null
						&& response.getEntity().getContent() != null) {
					msg = IOUtils.toString(response.getEntity().getContent());
				}
				throw new RuntimeException("Error: " + response.getStatusLine()
						+ " body: " + msg);
			}

		} catch (Exception e) {
			throw new RuntimeException(e);
		} finally {
			if (post != null)
				post.releaseConnection();
		}
	}

上記のメソッドは、HTTP POST 要求、および通知を送信する正しい本体とヘッダーを通知ハブの /messages エンドポイントに送信します。

## <a name="complete-tutorial"></a>チュートリアルを完了する
ここで、Java バックエンドから通知を送信して、使用についてのチュートリアルを完了できます。

Notification Hubs クライアントを初期化します (「[通知ハブの使用]」の説明に従って接続文字列とハブ名を置き換えます)。
	NotificationHub hub = new NotificationHub("{connection string}", "{hubname}");

次に、ターゲット モバイル プラットフォームに応じて送信コードを追加します。

### Windows ストアおよび Windows Phone 8.1 (非 Silverlight)

	String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
	Notification n = Notification.createWindowsNotification(toast);
	hub.sendNotification(n);

### iOS

	String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
	Notification n = Notification.createAppleNotification(alert);
	hub.sendNotification(n);

### Android
	String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
	Notification n = Notification.createGcmNotification(message);
	hub.sendNotification(n);

### Windows Phone 8.0 および 8.1 Silverlight

	String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
		        "<wp:Notification xmlns:wp=\"WPNotification\">" +
		           "<wp:Toast>" +
		                "<wp:Text1>Hello from Java!</wp:Text1>" +
		           "</wp:Toast> " +
		        "</wp:Notification>";
	Notification n = Notification.createMpnsNotification(toast);
	hub.sendNotification(n);

### Kindle Fire
	String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
	Notification n = Notification.createAdmNotification(message);
	hub.sendNotification(n);

Java コードを実行すると、ターゲット デバイスに表示される通知が生成されます。


## <a name="next-steps"></a>次のステップ
このトピックでは、Notification Hubs 用の単純な Java REST クライアントの作成方法を説明しました。次は、以下を実行できます。

* [Java REST ラッパー サンプル]をすべてダウンロードします。サンプルには上記のコード、および登録管理のコードがすべて含まれています。
* 引き続き、「[ニュース速報チュートリアル]」で Notification Hubs のタグ付け機能について学習してください。
* 個別ユーザーへの通知のプッシュについては、「[ユーザーへの通知チュートリアル]」で学習してください。




[Java REST wrapper sample (Java REST ラッパー サンプル)]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-java
[Java REST ラッパー サンプル]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-java
[通知ハブの使用]: http://azure.microsoft.com/ja-jp/documentation/articles/notification-hubs-ios-get-started/

<!--HONumber=35.1-->
