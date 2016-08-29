<properties 
   pageTitle=".NET SDK を使用した Azure DNS の DNS ゾーンとレコード セットの作成 | Microsoft Azure" 
   description=".NET SDK を使用した Azure DNS の DNS ゾーンとレコード セットの作成方法" 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/16/2016"
   ms.author="cherylmc"/>


# .NET SDK を使用した DNS ゾーンとレコード セットの作成

DNS ゾーン、レコードセット、レコードを作成、削除、更新する操作は、DNS SDK と .NET DNS 管理ライブラリを使用して自動化できます。Visual Studio プロジェクト全体は、[こちら](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)から入手できます。

## NuGet パッケージと名前空間宣言

DNS クライアントを使用するには、**Azure DNS 管理ライブラリ** NuGet パッケージをインストールし、DNS 管理用の名前空間をプロジェクトに追加する必要があります。
 
1. **Visual Studio** で、プロジェクトを開くか、新規作成します。

2. **[ツール]****、****[NuGet パッケージ マネージャー]****、****[パッケージ マネージャー コンソール]** の順に進みます。

3. Azure DNS 管理ライブラリをダウンロードします。

		using Microsoft.Azure;
		using Microsoft.Azure.Management.Dns;
		using Microsoft.Azure.Management.Dns.Models;

## DNS 管理クライアントの初期化

*DnsManagementClient* には、DNS ゾーンとレコードセットの管理に必要なメソッドとプロパティが含まれます。クライアントがサブスクリプションにアクセスできるようにするには、正しいアクセス許可を設定し、AWT トークンを生成する必要があります。詳細については、「[Azure Resource Manager 要求の認証](https://msdn.microsoft.com/library/azure/dn790557.aspx)」を参照してください。

	// get a token for the AAD application (see the article link above for code)
	string jwt = GetAToken();

	// make the TokenCloudCredentials using subscription ID and token
	TokenCloudCredentials tcCreds = new TokenCloudCredentials(subID, jwt);

	// make the DNS management client
	DnsManagementClient dnsClient = new DnsManagementClient(tcCreds);

## DNS ゾーンを作成または更新する

DNS ゾーンを作成するには、"Zone" オブジェクトを作成し、*dnsClient.Zones.CreateOrUpdate* に渡します。DNS ゾーンは特定のリージョンにリンクされないため、場所は "global" に設定されます。<BR> Azure DNS では、[Etag](dns-getstarted-create-dnszone.md) と呼ばれるオプティミスティック同時実行制御がサポートされます。"Etag" は Zone のプロパティです。"IfNoneMatch" は ZoneCreateOrUpdateParameters のプロパティです。

	// To create a DNS zone
	Zone z = new Zone("global");
	z.Properties = new ZoneProperties();
	z.Tags.Add("dept", "shopping");
	z.Tags.Add("env", "production");
	ZoneCreateOrUpdateParameters zoneParams = new ZoneCreateOrUpdateParameters(z);
	ZoneCreateOrUpdateResponse responseCreateZone = 
	dnsClient.Zones.CreateOrUpdate("myresgroup", "myzone.com", zoneParams);



## DNS レコードとレコード セットの作成または更新

DNS レコードはレコード セットとして管理されます。レコード セットは、ゾーン内で名前とレコードの種類が同じレコードのセットです。レコード セットを作成または更新するには、"RecordSet" オブジェクトを作成し、*dnsClient.RecordSets.CreateOrUpdate* に渡します。レコード セットの名前は、完全修飾 DNS 名とは対照的に、ゾーン名を基準として相対的であることに注意してください。場所は "global" に設定されます。<BR> Azure DNS では、[Etag](dns-getstarted-create-dnszone.md) と呼ばれるオプティミスティック同時実行制御がサポートされます。"Etag" は RecordSet のプロパティです。"IfNoneMatch" は RecordSetCreateOrUpdateParameters のプロパティです。
    


	// To create record sets
	RecordSet rsWwwA = new RecordSet("global");
	rsWwwA.Properties = new RecordProperties(3600);
	rsWwwA.Properties.ARecords = new List<ARecord>();
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.4"));
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.5"));
	RecordCreateOrUpdateParameters recordParams = 
								new RecordCreateOrUpdateParameters(rsWwwA);
	RecordCreateOrUpdateResponse responseCreateA = 
								dnsClient.RecordSets.CreateOrUpdate("myresgroup", 
	"myzone.com", "www", RecordType.A, recordParams);
	
    
## ゾーンとレコード セットの取得

*Zones* コレクションと *RecordSets* コレクションを使用すると、それぞれゾーンとレコード セットを取得できます。RecordSets は、その種類、名前、それが存在するゾーンとリソース グループで識別されます。ゾーンは、その名前と、それが存在するリソース グループで識別されます。

	ZoneGetResponse getZoneResponse = 
	dnsClient.Zones.Get("myresgroup", "myzone.com");
	RecordGetResponse getRSResp = 
	dnsClient.RecordSets.Get("myresgroup", 
	"myzone.com", "www", RecordType.A);

## ゾーンとレコード セットの一覧表示

ゾーンの一覧を表示するには、Zones コレクションで *List* メソッドを使用します。レコード セットの一覧を表示するには、RecordSets コレクションで *List* メソッドまたは *ListAll* メソッドを使用します。*List* メソッドは、指定された種類のレコード セットのみを返すという点で *ListAll* メソッドと異なります。

次の例では、DNS ゾーンとレコード セットの一覧を取得する方法を示しています。


	ZoneListResponse zoneListResponse = dnsClient.Zones.List("myresgroup", new ZoneListParameters());
	foreach (Zone zone in zoneListResponse.Zones)
	{
    	RecordListResponse recordSets = 
                 			dnsClient.RecordSets.ListAll("myresgroup", "myzone.com", new RecordSetListParameters());

    // do something like write out each record set
	}


## 次のステップ

[Visual Studio SDK のサンプル プロジェクト](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

<!---HONumber=AcomDC_0817_2016-->