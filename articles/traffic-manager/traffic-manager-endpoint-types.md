<properties 
   pageTitle="Traffic Manager エンドポイントの種類 | Microsoft Azure"
   description="この記事では、Azure Traffic Manager で使用できるさまざまなエンドポイントの種類について説明します"
   services="traffic-manager"
   documentationCenter=""
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2016"
   ms.author="jtuliani" />

# Traffic Manager エンドポイント

Microsoft Azure Traffic Manager を使用すると、世界中のさまざまなデータ センターまたは他の場所で実行されているアプリケーションのデプロイに、ユーザー トラフィックを分散させる方法を制御できます。

Traffic Manager では、各アプリケーションのデプロイを "エンドポイント" として構成する必要があります。Traffic Manager は DNS 要求を受信すると、現在のエンドポイントの可用性と、選択されているトラフィック ルーティング方法に基づいて、このエンドポイントのいずれかを選択して DNS 応答で返します。詳細については、「[Traffic Manager のしくみ](traffic-manager-how-traffic-manager-works.md)」をご覧ください。

このページでは、Traffic Manager でさまざまな種類のエンドポイントをサポートする方法について説明します。

## Traffic Manager エンドポイントの種類

Traffic Manager がサポートするエンドポイントには、次の 3 種類があります。

- **Azure エンドポイント**: Azure でホストされるサービスで使用されます。
- **外部エンドポイント**: Azure 外でホストされるサービス (オンプレミス サービスまたはホスティング プロバイダーが異なるサービス) で使用されます。
- **入れ子になったエンドポイント**: Traffic Manager プロファイルを組み合わせて、より柔軟なトラフィック ルーティング スキームを作成し、より大規模で複雑なデプロイのニーズに対応するために使用されます。

さまざまなエンドポイントの種類を 1 つの Traffic Manager プロファイルにまとめる方法に制限はありません。各プロファイルでは、エンドポイントの種類を好きなように組み合わせることができます。

次のセクションでは、各エンドポイントの種類についてさらに詳しく説明します。

### Azure エンドポイント

Azure エンドポイントは、Traffic Manager で Azure ベースのサービスを構成するときに使用されます。Azure エンドポイントが現在サポートする Azure リソースの種類は次のとおりです。

- "従来の" IaaS VM と PaaS クラウド サービス。
- Web Apps
- PublicIPAddress リソース (直接、または Azure Load Balancer を介して VM に接続可能)

PublicIPAddress リソースは Azure Resource Manager のリソースです。Azure Service Management API には存在しません。つまり、Traffic Manager の Azure Resource Manager エクスペリエンスでのみサポートされます。その他のエンドポイントの種類については、Traffic Manager の Resource Manager とサービス管理の両方のエクスペリエンスによってサポートされます。

Traffic Manager は、Azure エンドポイントを使用して、"従来の" IaaS VM、PaaS クラウド サービス、または Web アプリの停止と開始を検出します。これはエンドポイントの状態に反映されます (詳細については、[Traffic Manager エンドポイントの監視](traffic-manager-monitoring.md#endpoint-and-profile-status)に関するトピックをご覧ください)。 基になるサービスが停止すると、Traffic Manager のエンドポイント正常性チェックに対する課金が停止し、エンドポイントにトラフィックが送信されなくなります。サービスの再開とともに課金も再開され、エンドポイントがトラフィックを受信できるようになります。これは、PublicIpAddress エンドポイントには適用されません。

### 外部エンドポイント

外部エンドポイントは、オンプレミスでホストされているサービス、別のプロバイダーでホストされているサービスなど、Azure 外部のサービスにトラフィックが送信されるよう Traffic Manager を構成をするときに使用されます。

外部エンドポイントは単独で使用することも、同じ Traffic Manager プロファイルで Azure エンドポイントと組み合わせることもできます。Azure エンドポイントと外部エンドポイントを組み合わせると、次のようにさまざまなシナリオが可能になります。

- Azure を使用して、アクティブ/アクティブまたはアクティブ/パッシブ フェールオーバー モデルで、既存のオンプレミス アプリケーションの冗長性を向上させる。
- Azure を使用して、既存のオンプレミス アプリケーションを、[Traffic Manager "パフォーマンス" トラフィック ルーティング](traffic-manager-routing-methods.md#performance-traffic-routing-method)と共に他の地理的な場所に拡張して、アプリケーションの待機時間を短縮し、エンド ユーザーのパフォーマンスを向上させる。
- Azure を使用して、既存のオンプレミス アプリケーションの容量を、継続的に、または "クラウドへのバースト" として追加し、需要の急増に対処する。

場合によっては、外部エンドポイントを使用して Azure サービスを参照すると便利です (具体例については、「[FAQ](#faq)」を参照)。この場合、正常性チェックは、外部エンドポイントではなく、Azure エンドポイントの料金で課金されます。ただし、Azure エンドポイントとは異なり、基になるサービスを停止または削除しても、該当する正常性チェックへの課金は、Traffic Manager でエンドポイントを明示的に無効にするか削除しない限り停止されません。

### "入れ子" になったエンドポイント

入れ子になったエンドポイントを使用すると、Traffic Manager プロファイルを組み合わせて、より柔軟なトラフィック ルーティング スキームを作成し、より大規模で複雑なデプロイのニーズに対応できます。

入れ子になったエンドポイントでは、"子" プロファイルがエンドポイントとして "親" プロファイルに追加され、階層が作成されます。子と親の両方のプロファイルには、入れ子になった他のプロファイルなど、あらゆる種類の他のエンドポイントを含めることができます。

詳細については、「[入れ子になった Traffic Manager プロファイル](traffic-manager-nested-profiles.md)」をご覧ください。

## エンドポイントとしての Web アプリ

Traffic Manager で Web アプリをエンドポイントとして構成するときの追加の考慮事項を次に示します。

1. Web Apps を Traffic Manager で使用するには、その SKU が "Standard" 以上である必要があります。SKU が低い Web アプリを追加するための Traffic Manager への呼び出しは失敗します。既存の Web アプリの SKU をダウングレードすると、Traffic Manager が、その Web アプリにトラフィックを送信できなくなり、エンドポイントが Traffic Manager から削除される場合があります。

2. HTTP 要求を受信した Web Apps サービスは、その要求の "host" ヘッダーを使用して、どの Web アプリで要求を処理するかを判断します。ホスト ヘッダーには、"contosoapp.azurewebsites.net" など、要求を開始するための DNS 名が含まれています。別の DNS 名を Web アプリで使用するには、その DNS 名を、アプリのカスタム ドメインとして登録する必要があります。Web アプリ エンドポイントを Azure エンドポイントとして Traffic Manager プロファイルに追加すると、Traffic Manager プロファイルの DNS 名は、そのアプリのカスタム ドメインとして自動的に登録されます。この登録は、エンドポイントが削除されると、自動的に削除されます。

3. 通常、Web アプリは、Azure のエンドポイントとして構成されます。ただし、状況によっては、外部エンドポイントを使用して Web アプリを構成すると便利です (具体例については、次の項目を参照)。Web Apps は、Resource Manager Traffic Manager エクスペリエンスを使用するときに、Traffic Manager で外部エンドポイントとしてのみ構成できます。これは、サービス管理エクスペリエンスではサポートされません。

4. 各 Traffic Manager プロファイルでは、Azure リージョンごとに最大 1 つの Web アプリ エンドポイントを使用できます。この制約を回避する方法については、「[FAQ](#faq)」をご覧ください。

## エンドポイントの有効化と無効化

Traffic Manager でエンドポイントを無効にすると、メンテナンス モードのエンドポイントや再デプロイされるエンドポイントから一時的にトラフィックを削除するときに便利です。エンドポイントが再度稼働状態になったら、もう一度有効にできます。

エンドポイントを有効および無効にするには、Resource Manager と サービス管理の両方のエクスペリエンスでサポートされている、Traffic Manager ポータル、PowerShell、CLI、または REST API を使用します。

>[AZURE.NOTE] エンドポイントの無効化は、デプロイメント状態とは関係ありません。Azure サービス (VM、Web アプリなど) は、トラフィックが Traffic Manager プロファイルの DNS 名経由ではなく、そのサービスに直接アドレス指定されていれば、Traffic Manager で無効になっても稼働し続け、トラフィックを受信できます。詳細については、「[Traffic Manager のしくみ](traffic-manager-how-traffic-manager-works.md)」をご覧ください。

無効化されたエンドポイントは DNS 応答では返されないため、そのエンドポイントにトラフィックが送信されることはありません。また、エンドポイントに対する正常性チェックは停止し、課金もされません。エンドポイントの無効化と削除はほぼ同じですが、無効化の方が削除するよりも簡単です。

各エンドポイントが現在トラフィックを受信できるかどうかは、プロファイルの状態 (有効/無効)、エンドポイントの状態 (有効/無効)、およびそのエンドポイントの正常性チェックの結果によって異なります。詳細については、[Traffic Manager のエンドポイント監視](traffic-manager-monitoring.md#endpoint-and-profile-status)に関する記事をご覧ください。

>[AZURE.NOTE] Traffic Manager は DNS レベルで動作するため、いずれかのエンドポイントとの既存の接続に影響を与えることはありません。(プロファイル設定を変更するか、フェールオーバーまたはフェールバックが発生して) エンドポイント間でトラフィックが送信される際、新しい接続は Traffic Manager によって使用可能なエンドポイントに転送されます。ただし、他のエンドポイントは、セッションが終了するまで既存の接続を介してトラフィックを受信し続ける可能性があります。トラフィックが既存の接続に転送されないようにするには、各エンドポイントで使用されるセッション期間をアプリケーションで制限する必要があります。

プロファイル内のすべてのエンドポイントまたはプロファイル自体が無効になっている場合は、DNS クエリは、"NXDOMAIN" 応答を受け取ります。これは、プロファイルが削除されている場合と同じです。

## FAQ

### 複数のサブスクリプションのエンドポイントで Traffic Manager を使用できますか。
はい。この方法は、Traffic Manager に対して、Service Management API を使用しているか Resource Manager API を使用しているかによって異なります。[Azure ポータル](https://portal.azure.com)では Resource Manager を使用し、["従来の" ポータル](https://manage.windowsazure.com)では サービス管理を使用します。

Resource Manager では、すべてのサブスクリプションのエンドポイントを Traffic Manager に追加できますが、それには、Traffic Manager プロファイルを構成しているユーザーに、エンドポイントへの読み取りアクセス権が付与されている必要があります。こうしたアクセス許可を付与するには、[Azure Resource Manager とのロール ベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-configure.md) を使用します。

サービス管理では、Azure エンドポイントとして構成されたすべてのクラウド サービスや Web アプリが、Traffic Manager プロファイルと同じサブスクリプションに存在していることが、Traffic Manager によって求められます。その他のサブスクリプションにあるクラウド サービスのエンドポイントは、"外部" エンドポイントとして Traffic Manager に追加できます (引き続き "内部" エンドポイントの料金で課金されます)。その他のサブスクリプションの Web アプリは使用できません。

### クラウド サービス "Staging" スロットで Traffic Manager を使用できますか。
はい。クラウド サービス "Staging" スロットは、Traffic Manager で外部エンドポイントとして構成できます。

エンドポイントによって参照されるサービスは Azure に存在するため、正常性チェックは引き続き Azure エンドポイントの料金で課金されます。

使用されているエンドポイントの種類は外部エンドポイントです。したがって、基になるサービスへの変更は自動的には反映されません。つまり、クラウド サービスが停止または削除されても、Traffic Manager 内でエンドポイントが無効化または削除されるまで、Traffic Manager エンドポイントの正常性チェックは課金され続けます。

### Traffic Manager では IPv6 エンドポイントはサポートされていますか。

はい。現在 Traffic Manager ではアドレス指定可能な IPv6 ネーム サーバーを提供していませんが、IPv6 エンドポイントに接続する IPv6 クライアントは引き続き Traffic Manager を使用できます。

IPv6 専用クライアントがまだ Traffic Manager を使用できるのは、このクライアントは、直接 Traffic Manager に DNS 要求を送信するのではなく、再帰 DNS サービスを使用するためです。このサービスには IPv6 を介して要求を送信できます。そして、再帰サービスは、IPv4 を使用して Traffic Manager ネーム サーバーに接続することができます。

DNS クエリを受信した Traffic Manager は、クライアントの接続先エンドポイントの DNS 名で応答します。IPv6 エンドポイントをサポートするには、エンドポイント DNS 名が IPv6 アドレスを指すように DNS AAAA レコードを構成するだけです。

Traffic Manager の正常性チェックを適切に動作させるために、サービスは IPv4 エンドポイントを公開する必要もあります。これは、DNS A レコードを使用して、同じエンドポイント DNS 名からマップする必要があります。

### 同じリージョン内の複数の Web アプリで Traffic Manager を使用できますか。

通常、Traffic Manager は、さまざまなリージョンにデプロイされたアプリケーションにトラフィックを送信するときに使用されます。ただし、この Traffic Manager は、同じリージョンに複数のアプリケーションがデプロイされている場合も使用できます。

Web アプリの場合、Traffic Manager Azure エンドポイントでは、同じ Azure リージョンの複数の Web アプリ エンドポイントを、同じ Traffic Manager プロファイルに追加することはできません。次の手順により、この制約を回避することができます。

1.	同じリージョン内の Web アプリが、別の Web アプリ "スケール ユニット" にあること、つまり、別の Web アプリ サービス インスタンスであることを確認します。これを行うには、DNS エントリである <...>.azurewebsites.net の DNS パスを確認します。スケール ユニットは、"waws-prod-xyz-123.vip.azurewebsites.net" のようになります。特定のドメイン名を、特定のスケール ユニットの 1 つのサイトにマップする必要があります。この理由から、同じスケール ユニット内の 2 つの Web アプリが、Traffic Manager プロファイルを共有することはできません。
2.	各 Web アプリが別のスケール ユニットにあるものと想定し、バニティ ドメイン名をカスタム ホスト名として各 Web アプリに追加します。これには、すべての Web アプリが、同じサブスクリプションに属している必要があります。
3.	Traffic Manager プロファイルに対して通常行うように、Web アプリ エンドポイントを Azure エンドポイントとして 1 つだけ追加します。
4.	追加の各 Web アプリ エンドポイントを、外部エンドポイントとして Traffic Manager プロファイルに追加します。これを行うには、サービス管理エクスペリエンスではなく、Traffic Manager の Resource Manager エクスペリエンスを使用する必要があります。
5.	バニティ ドメイン (上記の手順 2. で使用) から Traffic Manager プロファイルの DNS 名 (<…>.trafficmanager.net) への、DNS CNAME レコードを作成します。
6.	Traffic Manager プロファイルの DNS 名ではなく、バニティ ドメイン名を使用してサイトにアクセスします。

## 次のステップ

- [Traffic Manager のしくみ](traffic-manager-how-traffic-manager-works.md)を確認します。

- Traffic Manager の[エンドポイントの監視と自動フェールオーバー](traffic-manager-monitoring.md)を確認します。

- Traffic Manager の[トラフィック ルーティング方法](traffic-manager-routing-methods.md)を確認します。

<!---HONumber=AcomDC_0706_2016-->