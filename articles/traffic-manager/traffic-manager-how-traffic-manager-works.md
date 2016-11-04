---
title: Traffic Manager のしくみ | Microsoft Docs
description: この記事は、Azure Traffic Manager のしくみを理解するのに役立ちます。
services: traffic-manager
documentationcenter: ''
author: sdwheeler
manager: carmonm
editor: tysonn

ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/07/2016
ms.author: sewhee

---
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

* 3 つのサービス インスタンスをデプロイします。各デプロイの DNS 名は "contoso-us.cloudapp.net"、"contoso-eu.cloudapp.net"、および "contoso-asia.cloudapp.net" です。
* 次に、"contoso.trafficmanager.net" という名前の Traffic Manager プロファイルを作成します。これは、上記の名前の 3 つのエンドポイントで "Performance" トラフィック ルーティング方法を使用するように構成されます。
* 最後に、DNS CNAME レコードを使用して、バニティ ドメイン "partners.contoso.com" が "contoso.trafficmanager.net" を指すように構成します。

![Traffic Manager の DNS 構成][1]

> [!NOTE]
> Azure Traffic Manager でバニティ ドメインを使用する場合は、CNAME を使用して、バニティ ドメイン名が、Traffic Manager ドメイン名を指すようにする必要があります。
> 
> DNS 標準の制限により、ドメインの "頂点" (またはルート) では CNAME を作成できません。したがって "contoso.com" ("ネイキッド" ドメインと呼ばれることがあります) の CNAME を作成することはできません。CNAME は、"contoso.com" ("www.contoso.com" など) の下にあるドメインについてのみ作成できます。
> 
> つまり、ネイキッド ドメインで直接 Traffic Manager を使用することはできません。この問題を回避するために、シンプルな HTTP リダイレクトを使用して、"contoso.com" に対する要求を、"www.contoso.com" などの代替名に転送することをお勧めします。
> 
> 

## Traffic Manager でのクライアントの接続方法
(上記の例で説明したように) エンドユーザーがページ https://partners.contoso.com/login.aspx を要求すると、クライアントは、次の手順を実行して DNS 名を解決し、接続を確立しようとします。

![Traffic Manager を使用した接続の確立][2]

1. クライアント (PC、電話など) は、構成されている再帰 DNS サービスに対して "partners.contoso.com" の DNS クエリを作成します (再帰 DNS サービス ("ローカル DNS" サービスとも呼ばれこともあります) は、DNS ドメインを直接ホストしません。このサービスはクライアントによって使用され、DNS 名を解決するために必要となる、信頼できるさまざまな DNS サービスにインターネット経由で接続する作業をオフロードします)。
2. ここで、再帰 DNS サービスは "partners.contoso.com" DNS 名を解決します。まず、再帰 DNS サービスは、"contoso.com" ドメインのネーム サーバーを見つけます。次に、そのネーム サーバーに接続して、"partners.contoso.com" DNS レコードを要求します。contoso.trafficmanager.net の CNAME が返されます。
3. ここで、再帰 DNS サービスは、Azure Traffic Manager サービスで提供される、"trafficmanager.net" ドメインのネーム サーバーを見つけます。そして、そのネーム サーバーに接続して、"contoso.trafficmanager.net" DNS レコードを要求します。
4. Traffic Manager ネーム サーバーは要求を受け取り、次に情報に基づいて、どのエンドポイントを返すかを選択します。a. 各エンドポイントの有効/無効の状態 (無効なエンドポイントは返されません) b. 各エンドポイントの現在の正常性。Traffic Manager の正常性チェックによって確認されます。詳細については、Traffic Manager のエンドポイント監視に関するページをご覧ください。c. 選択されているトラフィック ルーティング方法。詳細については、Traffic Manager のトラフィック ルーティング方法に関するページをご覧ください。
5. 選択されたエンドポイントは、別の DNS CNAME レコードとして返されます。この場合は、contoso-us.cloudapp.net が返されるものとします。
6. ここで、再帰 DNS サービスは、"cloudapp.net" ドメインのネーム サーバーを見つけます。そして、そのネーム サーバーに接続して、"contoso-us.cloudapp.net" DNS レコードを要求します。米国を拠点とするサービス エンドポイントの IP アドレスが含まれる DNS "A" のレコードが返されます。
7. 再帰 DNS サービスは、名前解決の上記のシーケンスの統合結果をクライアントに返します。
8. クライアントは、再帰 DNS サービスから DNS 結果を受け取り、指定された IP アドレスに接続します。クライアントは、アプリケーション サービス エンドポイントに Traffic Manager 経由ではなく直接接続します。これは HTTPS エンドポイントであるため、必要な SSL/TLS ハンドシェイクを実行し、"/login.aspx" ページに対して HTTP GET 要求を実行します。

再帰 DNS サービスでは、エンドユーザー デバイスの DNS クライアントと同様、受信した DNS 応答がキャッシュされます。キャッシュのデータを使用することで、後続の DNS クエリに対する応答が迅速になります。他のネーム サーバーにクエリを実行する必要はありません。キャッシュの期間は、各 DNS レコードの "Time-to-Live" (TTL) プロパティによって決まります。この値を減らすと、キャッシュの有効期限までの期間が短くなるため、Traffic Manager へのラウンドトリップが増えます。この値を増やすと、障害が発生したエンドポイントからトラフィックが離れるまでの時間が長くなる場合があります。Traffic Manager を使用すると、Traffic Manager DNS 応答で使用された TTL を構成して、アプリケーションのニーズの最適なバランスを実現する値を選択できます。

## FAQ
### Traffic Manager ではどの IP アドレスが使用されますか。
「Traffic Manager のしくみ」の説明にあるとおり、Traffic Manager は DNS レベルで動作します。Traffic Manager では、DNS 応答を使用して、クライアントを適切なサービス エンドポイントに転送します。クライアントはサービス エンドポイントに、Traffic Manager 経由ではなく、直接接続します。

そのため、Traffic Manager では、クライアントが接続するエンドポイントまたは IP アドレスが提供されません。静的 IP アドレスなどが必要な場合には、Traffic Manager ではなくサービス側で構成する必要があります。

### Traffic Manager では "スティッキー" セッションはサポートされていますか。
[前述のとおり](#how-clients-connect-using-traffic-manager)、Traffic Manager は DNS レベルで動作します。Traffic Manager では、DNS 応答を使用して、クライアントを適切なサービス エンドポイントに転送します。クライアントはサービス エンドポイントに、Traffic Manager 経由ではなく、直接接続します。そのため、Traffic Manager では、Cookie を含め、クライアントとサーバーの間の HTTP トラフィックが把握されません。

また、Traffic Manager が受信する DNS クエリの発信元 IP アドレスは、クライアントの IP アドレスではなく、再帰 DNS サービスの IP アドレスになることに注意してください。

そのため、Traffic Manager は、個々のクライアントを識別または追跡することはできず、"スティッキー" セッションを実装することはできません。これは、DNS ベースのトラフィック管理システムすべてに共通の仕様であり、Traffic Manager の利用による制約ではありません。

### Traffic Manager を使用していると HTTP エラーが表示されました。なぜですか。
[前述のとおり](#how-clients-connect-using-traffic-manager)、Traffic Manager は DNS レベルで動作します。Traffic Manager では、DNS 応答を使用して、クライアントを適切なサービス エンドポイントに転送します。クライアントはサービス エンドポイントに、Traffic Manager 経由ではなく、直接接続します。

そのため、Traffic Manager ではクライアントとサーバーの間の HTTP トラフィックは把握されず、HTTP レベルのエラーは生成できません。表示された HTTP エラーは、アプリケーションによって生成されたものと考えられます。クライアントはアプリケーションに接続しているため、このエラーは、Traffic Manager の役割を含む DNS 解決が完了している必要があることも意味しています。

そのため、詳細な調査はアプリケーションを対象とする必要があります。

一般的に問題となるのは、Traffic Manager を使用している場合、ブラウザーからアプリケーションに渡される "ホスト" HTTP ヘッダーに、ブラウザーで使用されるドメイン名が表示される点です。このドメイン名は、テスト時に Traffic Manager ドメイン名 (例: myprofile.trafficmanager.net) を使用している場合はそのドメイン名になることもあれば、Traffic Manager ドメイン名を指すように構成されているバニティ ドメインの CNAME になることもあります。いずれにしても、アプリケーションがこのホスト ヘッダーを受け入れるよう構成されていることを確認してください。

アプリケーションが Azure App Service でホストされている場合は、「[Traffic Manager を使用して Azure App Service Web アプリのカスタム ドメイン名を構成する](../app-service-web/web-sites-traffic-manager-custom-domain-name.md)」を参照してください。

### Traffic Manager を使用すると、パフォーマンスにどのような影響がありますか。
[前述のとおり](#how-clients-connect-using-traffic-manager)、Traffic Manager は DNS レベルで動作します。Traffic Manager では、DNS 応答を使用して、クライアントを適切なサービス エンドポイントに転送します。クライアントはサービス エンドポイントに、Traffic Manager 経由ではなく、直接接続します。

クライアントはサービス エンドポイントに直接接続するため、接続の確立後は、Traffic Manager の使用に伴うパフォーマンスへの影響は発生しません。

Traffic Manager は DNS レベルでアプリケーションと統合されるため、追加の DNS 参照を DNS 解決チェーンに挿入する必要はありません (詳細については、「[Traffic Manager の例](#traffic-manager-example)」を参照してください)。Traffic Manager が DNS 解決時間に与える影響は最小限です。Traffic Manager では、ネーム サーバーのグローバル ネットワークが使用されます。また、エニーキャスト ネットワーキングが使用されるため、DNS クエリは常に、使用可能な最も近いネーム サーバーにルーティングされます。さらに、DNS 応答がキャッシュされるため、Traffic Manager の使用に伴って発生する DNS 待機時間の増加がごく一部のセッションにしか適用されなくなります。

結果として、Traffic Manager をアプリケーションに組み込むことによるパフォーマンス全体への影響は最小限に抑えられます。

また、Traffic Manager の ["パフォーマンス" トラフィック ルーティング方法](traffic-manager-routing-methods.md#performance-traffic-routing-method)が使用されている場合は、エンド ユーザーを使用可能な最も近いエンドポイントにルーティングすることで、DNS 待機時間の増加を相殺して余りあるパフォーマンスの向上が達成されます。

### Traffic Manager ではどのようなアプリケーション プロトコルを使用できますか。
[前述のとおり](#how-clients-connect-using-traffic-manager)、Traffic Manager は DNS レベルで動作します。DNS 参照が完了すると、クライアントはアプリケーション エンドポイントに Traffic Manager 経由ではなく直接接続します。そのため、この接続では、任意のアプリケーション プロトコルを使用できます。

ただし、Traffic Manager のエンドポイント正常性チェックには、HTTP エンドポイントか HTTPS エンドポイントが必要です。このエンドポイントとして、クライアントが接続するアプリケーション エンドポイントとは別のエンドポイントを指定できます。これを行うには、Traffic Manager プロファイル正常性チェックの設定で別の TCP ポートまたは URI パスを指定します。

### "ネイキッド" (www のない) ドメイン名で Traffic Manager を使用することはできますか。
現時点では連携しません。

DNS 名どうしのマッピング作成には、レコードの種類として DNS CNAME が使用されます。「[Traffic Manager の例](#traffic-manager-example)」の説明にあるとおり、Traffic Manager では、バニティ DNS 名 (例: www.contoso.com) を Traffic Manager プロファイルの DNS 名 (例: contoso.trafficmanager.net) にマッピングするために DNS CNAME レコードが必要です。また、Traffic Manager プロファイル自体も、クライアントが接続するエンドポイントを示すために、別の DNS CNAME を返します。

しかし、DNS 標準では、CNAME が同じ種類の他の DNS レコードと共存することは許可されていません。DNS ゾーンの頂点 (またはルート) には常に既存の DNS レコードが 2 つ (SOA レコードと権限のある NS レコード) 格納されているため、ゾーンの頂点で CNAME レコードを作成すると DNS 標準に違反します。

この問題を回避するために、ネイキッド (www のない) ドメインが使用されるサービスで Traffic Manager を使用する場合は、HTTP リダイレクトを使用してトラフィックをネイキッド ドメインから別の URL に転送することをお勧めします。こうすることで、Traffic Manager を使用できるようになります。たとえば、ネイキッド ドメイン "contoso.com" でユーザーを "www.contoso.com" にリダイレクトすると、Traffic Manager を使用できるようになります。

Traffic Manager におけるネイキッド ドメインの完全サポートは、開発待ちの機能として登録されています。この機能に関心をお持ちの場合は、[コミュニティ フィードバック サイトの投票](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly)で、ぜひ支持を表明してください。

## 次のステップ
Traffic Manager の[エンドポイントの監視と自動フェールオーバー](traffic-manager-monitoring.md)の詳細を確認する。

Traffic Manager の[トラフィック ルーティング方法](traffic-manager-routing-methods.md)の詳細を確認する。

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

<!---HONumber=AcomDC_0824_2016-->