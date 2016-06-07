<properties
   pageTitle="Traffic Manager のしくみ | Microsoft Azure"
   description="この記事は、Azure Traffic Manager のしくみを理解するのに役立ちます。"
   services="traffic-manager"
   documentationCenter=""
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn"/>

<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/25/2016"
   ms.author="jonatul"/>

# Traffic Manager のしくみ

Azure Traffic Manager を使用すると、アプリケーション エンドポイント間でトラフィックを分散させる方法を制御できます。このエンドポイントには、Azure 内または Azure 外でホストされ、インターネットに接続されているすべてのエンドポイントを使用できます。

Traffic Manager の 2 つのメリットを次に示します。

1. 複数の[トラフィック ルーティング方法](traffic-manager-routing-methods.md)のいずれかに従って、トラフィックを分散します
2. [エンドポイントの正常性を継続的に監視](traffic-manager-monitoring.md)し、エンドポイントで障害が発生したときに、自動フェールオーバーが行われます

エンド ユーザーがサービス エンドポイントに接続しようとすると、クライアント (PC、電話など) は、まず、そのエンドポイントの DNS 名を IP アドレスに解決しようと試みる必要があります。その後、クライアントは、その IP アドレスに接続してサービスにアクセスします。

**ここで把握しておかなければならない重要なポイントは、Traffic Manager が DNS レベルで動作するということです。** Traffic Manager では、選択したトラフィック ルーティング方法と現在のエンドポイントの正常性に基づいて、DNS を使用して、エンド ユーザーを特定のサービス エンドポイントに転送します。その後、選択されたエンドポイントに、クライアントが**直接**接続します。Traffic Manager はプロキシではなく、クライアントとサービスの間のトラフィックを認識することはありません。

## Traffic Manager の例

Contoso Corp が、新しいパートナー ポータルを開発しました。このポータルの URL は https://partners.contoso.com/login.aspx で、アプリケーションは Azure でホストされています。Contoso Corp では、可用性を向上させ、グローバルなパフォーマンスを最大限に高めるために、そのアプリケーションを世界の 3 つのリージョンにデプロイし、Traffic Manager を使って、利用可能な最も近いエンドポイントにエンドユーザーを分散したいと考えています。

この構成を実現するには:

- 3 つのサービス インスタンスをデプロイします。各デプロイの DNS 名は "contoso-us.cloudapp.net"、"contoso-eu.cloudapp.net"、および "contoso-asia.cloudapp.net" です。
- 次に、"contoso.trafficmanager.net" という名前の Traffic Manager プロファイルを作成します。これは、上記の名前の 3 つのエンドポイントで "Performance" トラフィック ルーティング方法を使用するように構成されます。
- 最後に、DNS CNAME レコードを使用して、バニティ ドメイン "partners.contoso.com" が "contoso.trafficmanager.net" を指すように構成します。

![Traffic Manager の DNS 構成][1]

> [AZURE.NOTE] Azure Traffic Manager でバニティ ドメインを使用する場合は、CNAME を使用して、バニティ ドメイン名が、Traffic Manager ドメイン名を指すようにする必要があります。

> DNS 標準の制限により、ドメインの "頂点" (またはルート) では CNAME を作成できません。したがって "contoso.com" ("ネイキッド" ドメインと呼ばれることがあります) の CNAME を作成することはできません。CNAME は、"contoso.com" ("www.contoso.com" など) の下にあるドメインについてのみ作成できます。

> つまり、ネイキッド ドメインで直接 Traffic Manager を使用することはできません。この問題を回避するために、シンプルな HTTP リダイレクトを使用して、"contoso.com" に対する要求を、"www.contoso.com" などの代替名に転送することをお勧めします。

## Traffic Manager でのクライアントの接続方法

(上記の例で説明したように) エンドユーザーがページ https://partners.contoso.com/login.aspx を要求すると、クライアントは、次の手順を実行して DNS 名を解決し、接続を確立しようとします。

![Traffic Manager を使用した接続の確立][2]

1.	クライアント (PC、電話など) は、構成されている再帰 DNS サービスに対して "partners.contoso.com" の DNS クエリを作成します (再帰 DNS サービス ("ローカル DNS" サービスとも呼ばれこともあります) は、DNS ドメインを直接ホストしません。このサービスはクライアントによって使用され、DNS 名を解決するために必要となる、信頼できるさまざまな DNS サービスにインターネット経由で接続する作業をオフロードします)。
2.	ここで、再帰 DNS サービスは "partners.contoso.com" DNS 名を解決します。まず、再帰 DNS サービスは、"contoso.com" ドメインのネーム サーバーを見つけます。次に、そのネーム サーバーに接続して、"partners.contoso.com" DNS レコードを要求します。contoso.trafficmanager.net の CNAME が返されます。
3.	ここで、再帰 DNS サービスは、Azure Traffic Manager サービスで提供される、"trafficmanager.net" ドメインのネーム サーバーを見つけます。そして、そのネーム サーバーに接続して、"contoso.trafficmanager.net" DNS レコードを要求します。
4.	Traffic Manager ネーム サーバーは要求を受け取り、次に情報に基づいて、どのエンドポイントを返すかを選択します。a. 各エンドポイントの有効/無効の状態 (無効なエンドポイントは返されません) b. 各エンドポイントの現在の正常性。Traffic Manager の正常性チェックによって確認されます。詳細については、Traffic Manager のエンドポイント監視に関するページをご覧ください。c. 選択されているトラフィック ルーティング方法。詳細については、Traffic Manager のトラフィック ルーティング方法に関するページをご覧ください。
5.	選択されたエンドポイントは、別の DNS CNAME レコードとして返されます。この場合は、contoso-us.cloudapp.net が返されるものとします。
6.	ここで、再帰 DNS サービスは、"cloudapp.net" ドメインのネーム サーバーを見つけます。そして、そのネーム サーバーに接続して、"contoso-us.cloudapp.net" DNS レコードを要求します。米国を拠点とするサービス エンドポイントの IP アドレスが含まれる DNS "A" のレコードが返されます。
7.	再帰 DNS サービスは、名前解決の上記のシーケンスの統合結果をクライアントに返します。
8.	クライアントは、再帰 DNS サービスから DNS 結果を受け取り、指定された IP アドレスに接続します。クライアントは、アプリケーション サービス エンドポイントに Traffic Manager 経由ではなく直接接続します。これは HTTPS エンドポイントであるため、必要な SSL/TLS ハンドシェイクを実行し、"/login.aspx" ページに対して HTTP GET 要求を実行します。

再帰 DNS サービスでは、エンドユーザー デバイスの DNS クライアントと同様、受信した DNS 応答がキャッシュされます。キャッシュのデータを使用することで、後続の DNS クエリに対する応答が迅速になります。他のネーム サーバーにクエリを実行する必要はありません。キャッシュの期間は、各 DNS レコードの "Time-to-Live" (TTL) プロパティによって決まります。この値を減らすと、キャッシュの有効期限までの期間が短くなるため、Traffic Manager へのラウンドトリップが増えます。この値を増やすと、障害が発生したエンドポイントからトラフィックが離れるまでの時間が長くなる場合があります。Traffic Manager を使用すると、Traffic Manager DNS 応答で使用された TTL を構成して、アプリケーションのニーズの最適なバランスを実現する値を選択できます。

## 次のステップ

Traffic Manager の[エンドポイントの監視と自動フェールオーバー](traffic-manager-monitoring.md)の詳細を確認します。

Traffic Manager の[トラフィック ルーティング方法](traffic-manager-routing-methods.md)の詳細を確認します。

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

<!---HONumber=AcomDC_0525_2016-->