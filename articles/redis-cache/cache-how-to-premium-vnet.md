<properties 
	pageTitle="Premium Azure Redis Cache の Virtual Network のサポートを構成する方法" 
	description="Premium レベル Azure Redis Cache インスタンスの Virtual Network のサポートを作成および管理する方法" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2016" 
	ms.author="sdanie"/>

# Premium Azure Redis Cache の Virtual Network のサポートを構成する方法
Azure Redis Cache には、新しい Premium レベルなど、キャッシュのサイズと機能を柔軟に選択できるさまざまなキャッシュ サービスがあります。

Azure Redis Cache の Premium レベルには、クラスタリング、永続性、および Virtual Network (VNET) のサポートが含まれています。VNET は、クラウド内のユーザー独自のネットワークを表すものです。VNET を使用して Azure Redis Cache インスタンスを構成する場合、パブリックにアドレスを指定することはできず、VNET 内のクライアントからのみ指定できます。この記事では、Azure Redis Cache インスタンスの Virtual Network のサポートを構成する方法について説明します。

その他の Premium キャッシュ機能については、「[Premium Azure Redis Cache の永続性を構成する方法](cache-how-to-premium-persistence.md)」と「[Premium Azure Redis Cache のクラスタリングを構成する方法](cache-how-to-premium-clustering.md)」を参照してください。

## VNET を選ぶ理由
[Azure Virtual Network (VNET)](https://azure.microsoft.com/services/virtual-network/) のデプロイメントでは、Azure Redis Cache のための強化されたセキュリティと分離、サブネット、アクセス制御ポリシーなど、Azure Redis Cache へのアクセスをさらに制限するための機能が提供されます。

## Virtual Network のサポート
Virtual Network (VNET) のサポートは、キャッシュの作成中に **[Redis Cache の新規作成]** ブレードで構成します。キャッシュを作成するには、[Azure ポータル](https://portal.azure.com)にサインインし、**[新規]**、**[データ + ストレージ]**、**[Redis Cache]** の順にクリックします。

![Redis Cache の作成][redis-cache-new-cache-menu]

VNET のサポートを構成するには、まず **[価格レベルの選択]** ブレードで **[Premium]** キャッシュのいずれかを選択します。

![価格レベルの選択][redis-cache-premium-pricing-tier]

Azure Redis Cache VNET 統合は、**[仮想ネットワーク (クラシック)]** ブレードで構成します。ここでは、既存の従来型 VNET を選択できます。新しい VNET を使用するには、「[Azure ポータルを使用した仮想ネットワーク (従来型) の作成](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)」の手順に従って作成し、**[Redis Cache Virtual Network]** ブレードに戻って選択します。

>[AZURE.NOTE] Azure Redis Cache は、クラシック VNET に対応しています。従来型 VNET を作成する方法については、「[」Azure ポータルを使用した仮想ネットワーク (従来型) の作成](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)」を参照してください。従来の VNET を ARM VNETS に接続する方法の詳細については、「[従来の Vnet を新しい Vnet に接続する](../virtual-network/virtual-networks-arm-asm-s2s.md)」を参照してください。

VNET を選択して構成するには、**[新規 Redis Cache]** ブレードで **[仮想ネットワーク (クラシック)]** をクリックし、ドロップダウン リストから目的の VNET を選択します。

![Virtual Network][redis-cache-vnet]

**[サブネット]** ドロップダウン リストから目的のサブネットを選択します。

![Virtual Network][redis-cache-vnet-ip]

**[静的 IP アドレス]** フィールドは省略可能です。ここでなにも指定しない場合は、選択したサブネットから選択されます。特定の静的 IP が必要な場合は、**[静的 IP アドレス]** に目的のアドレスを入力し、**[OK]** をクリックして VNET 構成を保存します。選択した静的 IP が既に使用中の場合、エラー メッセージが表示されます。

キャッシュが作成されたら、**[設定]** ブレードで **[仮想ネットワーク]** をクリックすることで、VNET の IP アドレスとその他の情報を表示できます。

![Virtual Network][redis-cache-vnet-info]

>[AZURE.IMPORTANT] VNET を使用するときに Azure Redis Cache インスタンスにアクセスするには、VNET で 1 つ目のパラメーターとしてキャッシュの静的 IP アドレスを渡し、`sslhost` パラメーターにキャッシュのエンドポイントを指定して渡します。次の例では、静的 IP アドレスは `172.160.0.99`、キャッシュ エンドポイントは `contoso5.redis.cache.windows.net` です。

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("172.160.0.99,sslhost=contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

## Azure Redis Cache VNET についてよく寄せられる質問 (FAQ)

次の一覧は、Azure Redis Cache のスケーリングに関するよく寄せられる質問への回答です。

## Azure Redis Cache と VNET に構成の誤りがあると、一般的にどのような問題がありますか

Azure Redis Cache が VNET でホストされている場合は、次の表のポートが使用されます。これらのポートがブロックされている場合は、キャッシュが正しく機能しない可能性があります。VNET で Azure Redis Cache を使用する場合に、不正な構成に関する最も一般的な問題は、これらの 1 つまたは複数のポートがブロックされていることです。

| ポート | 方向 | トランスポート プロトコル | 目的 | リモート IP |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80、443 | 送信 | TCP | Azure Storage/PKI (インターネット) に対する Redis の依存関係 | * |
| 53 | 送信 | TCP/UDP | DNS (インターネット/VNet) に対する Redis の依存関係 | * |
| 6379, 6380 | 受信 | TCP | Redis へのクライアント通信、Azure 負荷分散 | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 8443 | 受信/送信 | TCP | Redis の実装の詳細 | VIRTUAL\_NETWORK |
| 8500 | 受信 | TCP/UDP | Azure 負荷分散 | AZURE\_LOADBALANCER |
| 10221-10231 | 受信/送信 | TCP | Redis の実装の詳細 (リモート エンドポイントを VIRTUAL\_NETWORK に制限できます) | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 13000-13999 | 受信 | TCP | Redis クラスターへのクライアント通信、Azure 負荷分散 | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 15000-15999 | 受信 | TCP | Redis クラスターへのクライアント通信、Azure 負荷分散 | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 16001 | 受信 | TCP/UDP | Azure 負荷分散 | AZURE\_LOADBALANCER |
| 20226 | 受信 + 送信 | TCP | Redis クラスターの実装の詳細 | VIRTUAL\_NETWORK |




## Standard キャッシュまたは Basic キャッシュで VNET を使用できますか

VNET は Premium キャッシュでのみ使用できます。

## 次のステップ
Premium キャッシュ機能をさらに使用する方法を学習します。

-	[How to configure persistence for a Premium Azure Redis Cache (Premium Azure Redis Cache の永続性の構成方法)](cache-how-to-premium-persistence.md)
-	[How to configure clustering for a Premium Azure Redis Cache (Premium Azure Redis Cache のクラスタリングの構成方法)](cache-how-to-premium-clustering.md)





  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-vnet/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-vnet/redis-cache-premium-pricing-tier.png

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

<!---HONumber=AcomDC_0504_2016-->