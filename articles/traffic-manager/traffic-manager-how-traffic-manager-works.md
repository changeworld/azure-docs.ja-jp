---
title: "Traffic Manager のしくみ | Microsoft Docs"
description: "この記事では、Azure Traffic Manager のしくみについて説明します"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: a6c9370d-e60d-440f-aa82-b6d3fa5416b0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 6ba1732840d094497a9e2cb7a7ea7825a7f4bc89

---

# <a name="how-traffic-manager-works"></a>Traffic Manager のしくみ

Azure Traffic Manager を使用すると、アプリケーション エンドポイント全体でトラフィックの分散を制御できます。 エンドポイントは、Azure の内部または外部でホストされている、インターネットに公開されたサービスです。

Traffic Manager の 2 つのメリットを次に示します。

1. 複数の [トラフィック ルーティング方法](traffic-manager-routing-methods.md)
2. [エンドポイントの正常性を継続的に監視](traffic-manager-monitoring.md) し、エンドポイントで障害が発生したときに、自動フェールオーバーが行われます

クライアントからサービスへの接続を試みようとするときは、最初にサービスの DNS 名を IP アドレスに解決する必要があります。 その後、クライアントは、その IP アドレスに接続してサービスにアクセスします。

**ここで把握しておかなければならない重要なポイントは、Traffic Manager が DNS レベルで動作するということです。**  Traffic Manager は、DNS を使用して、トラフィックのルーティング メソッドのルールに基づいてクライアントを特定のサービス エンドポイントに転送します。 選択されたエンドポイントに、クライアントが**直接**接続します。 Traffic Manager は、プロキシでもゲートウェイでもありません。 Traffic Manager は、クライアントとサービスの間を通過するトラフィックを認識することはありません。

## <a name="traffic-manager-example"></a>Traffic Manager の例

Contoso Corp が、新しいパートナー ポータルを開発しました。 このポータルの URL は、https://partners.contoso.com/login.aspx です。 アプリケーションは、Azure の 3 つのリージョンにホストされます。 可用性を向上させ、グローバル パフォーマンスを最大化するには、Traffic Manager を使用して、利用可能な最も近いエンドポイントにクライアント トラフィックを分散します。

この構成を実現するには:

* 3 つのサービス インスタンスをデプロイします。 各デプロイの DNS 名は "contoso-us.cloudapp.net"、"contoso-eu.cloudapp.net"、および "contoso-asia.cloudapp.net" です。
* 次に、"contoso.trafficmanager.net" という名前の Traffic Manager プロファイルを作成します。これは、3 つのエンドポイントで "パフォーマンス" トラフィック ルーティング方法を使用するように構成します。
* 最後に、DNS CNAME レコードを使用して、バニティ ドメイン名 "partners.contoso.com" が "contoso.trafficmanager.net" を指すように構成します。

![Traffic Manager の DNS 構成][1]

> [!NOTE]
> Azure Traffic Manager でバニティ ドメインを使用する場合は、CNAME を使用して、バニティ ドメイン名が、Traffic Manager ドメイン名を指すようにする必要があります。 DNS の標準では、ドメインの "頂点" (またはルート) で CNAME を作成できません。 したがって "contoso.com" ("ネイキッド" ドメインと呼ばれることがあります) の CNAME を作成することはできません。 CNAME は、"contoso.com" ("www.contoso.com" など) の下にあるドメインについてのみ作成できます。 この制限を回避するために、シンプルな HTTP リダイレクトを使用して、"contoso.com" に対する要求を、"www.contoso.com" などの代替名に転送することをお勧めします。

## <a name="how-clients-connect-using-traffic-manager"></a>Traffic Manager でのクライアントの接続方法

前の例に続いて、クライアントがページ https://partners.contoso.com/login.aspx を要求すると、クライアントが次の手順を実行して DNS 名を解決して接続を確立します。

![Traffic Manager を使用した接続の確立][2]

1. クライアントは、構成されている再帰 DNS サービスに対して DNS クエリを送信し、名前 "partners.contoso.com" を解決します  再帰 DNS サービス ("ローカル DNS" サービスと呼ばれることもあります) は、DNS ドメインを直接ホストしません。 代わりに、クライアントは、DNS 名を解決するために必要となる、信頼できるさまざまな DNS サービスにインターネット経由で接続する作業をオフロードします。
2. DNS 名を解決するために、再帰 DNS サービスは、"contoso.com" ドメインのネーム サーバーを見つけます。 次に、そのネーム サーバーに接続して、"partners.contoso.com" DNS レコードを要求します。 "contoso.com" DNS サーバーは、"contoso.trafficmanager.net" を指す CNAME レコードを返します。
3. 次に、再帰 DNS サービスは、Azure Traffic Manager サービスで提供される、"trafficmanager.net" ドメインのネーム サーバーを見つけます。 見つけた DNS サーバーに "contoso.trafficmanager.net" の DNS レコードの要求を送信します。
4. Traffic Manager ネーム サーバーは要求を受け取り、 次の内容に基づいてエンドポイントを選択します。

    - 各エンドポイントの構成済みの状態 (無効なエンドポイントは返されません)
    - 各エンドポイントの現在の正常性。Traffic Manager の正常性チェックによって確認されます。 詳細については、 [Traffic Manager のエンドポイント監視](traffic-manager-monitoring.md)に関する記事をご覧ください。
    - 選択されているトラフィック ルーティング方法。 詳細については、「[Traffic Manager のルーティング方法](traffic-manager-routing-methods.md)」を参照してください。

5. 選択されたエンドポイントは、別の DNS の CNAME レコードとして返されます。 この場合は、"contoso-us.cloudapp.net" が返されるものとします。
6. 次に、再帰 DNS サービスが、"cloudapp.net" ドメインのネーム サーバーを見つけます。 そして、そのネーム サーバーに接続して、"contoso-us.cloudapp.net" DNS レコードを要求します。 米国を拠点とするサービス エンドポイントの IP アドレスが含まれる DNS "A" のレコードが返されます。
7. 再帰 DNS サービスは、結果を統合し、クライアントに単一の DNS 応答を返します。
8. クライアントは、DNS 結果を受け取り、指定された IP アドレスに接続します。 クライアントは、アプリケーション サービス エンドポイントに Traffic Manager 経由ではなく直接接続します。 これは HTTPS エンドポイントであるため、クライアントは必要な SSL/TLS ハンドシェイクを実行し、"/login.aspx" ページに対して HTTP GET 要求を実行します。

再帰 DNS サービスでは、受信した DNS 応答をキャッシュします。 クライアント デバイス上の DNS リゾルバーも、結果をキャッシュします。 キャッシュのデータを使用することで、後続の DNS クエリに対する応答が迅速になります。他のネーム サーバーにクエリを実行する必要はありません。 キャッシュの期間は、各 DNS レコードの "Time-to-Live" (TTL) プロパティによって決まります。 値を小さくすると、キャッシュの有効期限が短くなるため、Traffic Manager ネーム サーバーとのラウンドトリップが増加します。 値を大きくすると、失敗したエンドポイントからトラフィックを転送するときにより時間がかかる可能性があります。 Traffic Manager を使用すると、Traffic Manager DNS 応答で使用された TTL を構成して、アプリケーションのニーズの最適なバランスを実現する値を選択できます。

## <a name="faq"></a>FAQ

### <a name="what-ip-address-does-traffic-manager-use"></a>Traffic Manager ではどの IP アドレスが使用されますか。

「Traffic Manager のしくみ」の説明にあるとおり、Traffic Manager は DNS レベルで動作します。 Traffic Manager では、DNS 応答を送信して、クライアントを適切なサービス エンドポイントに転送します。 クライアントはサービス エンドポイントに、Traffic Manager 経由ではなく、直接接続します。

そのため、Traffic Manager では、クライアントが接続するエンドポイントまたは IP アドレスが提供されません。 したがって、サービスに静的 IP アドレスが必要な場合は、Traffic Manager ではなくサービス側で構成する必要があります。

### <a name="does-traffic-manager-support-sticky-sessions"></a>Traffic Manager では "スティッキー" セッションはサポートされていますか。

[前述のとおり](#how-clients-connect-using-traffic-manager)、Traffic Manager は DNS レベルで動作します。 Traffic Manager では、DNS 応答を使用して、クライアントを適切なサービス エンドポイントに転送します。 クライアントはサービス エンドポイントに、Traffic Manager 経由ではなく、直接接続します。 したがって、Traffic Manager は、クライアントとサーバーの間の HTTP トラフィックを認識することはありません。

また、Traffic Manager が受信する DNS クエリの発信元 IP アドレスは、クライアントではなく、再帰 DNS サービスのものであることに注意してください。 そのため、Traffic Manager は、個々のクライアントを追跡することはできず、"スティッキー" セッションを実装することはできません。 この制限は、すべての DNS ベースのトラフィック管理システムに共通であり、Traffic Manager 固有のものではありません。

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Traffic Manager を使用していると HTTP エラーが表示されたのはなぜですか。

[前述のとおり](#how-clients-connect-using-traffic-manager)、Traffic Manager は DNS レベルで動作します。 Traffic Manager では、DNS 応答を使用して、クライアントを適切なサービス エンドポイントに転送します。 クライアントはサービス エンドポイントに、Traffic Manager 経由ではなく、直接接続します。 Traffic Manager では、クライアントとサーバーの間の HTTP トラフィックは把握されません。 そのため、表示される HTTP エラーはすべて、アプリケーションによって生成されたものと考えられます。 クライアントがアプリケーションに接続するための DNS 解決手順は、すべて完了しています。 これには、Traffic Manager がアプリケーション トラフィック フローにおいて行う対話がすべて含まれます。

そのため、詳細な調査はアプリケーションを対象とする必要があります。

クライアントのブラウザーから送信される HTTP ホスト ヘッダーが、問題の最もよくある原因です。 使用しているドメイン名の正しいホスト ヘッダーを受け入れるようにアプリケーションが構成されていることを確認します。 Azure App Service を使用しているエンドポイントの場合は、「[Traffic Manager を使用して Azure App Service Web アプリのカスタム ドメイン名を構成する](../app-service-web/web-sites-traffic-manager-custom-domain-name.md)」を参照してください。

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Traffic Manager を使用すると、パフォーマンスにどのような影響がありますか。

[前述のとおり](#how-clients-connect-using-traffic-manager)、Traffic Manager は DNS レベルで動作します。 クライアントはサービス エンドポイントに直接接続するため、接続の確立後は、Traffic Manager の使用に伴うパフォーマンスへの影響は発生しません。

Traffic Manager は DNS レベルでアプリケーションと統合されるため、追加の DNS 参照を DNS 解決チェーンに挿入する必要はありません (詳細については、「[Traffic Manager の例](#traffic-manager-example)」を参照してください)。 Traffic Manager が DNS 解決時間に与える影響は最小限です。 Traffic Manager では、ネーム サーバーのグローバル ネットワークが使用されます。また、[エニーキャスト](https://en.wikipedia.org/wiki/Anycast) ネットワーキングが使用されるため、DNS クエリは常に、使用可能な最も近いネーム サーバーにルーティングされます。 さらに、DNS 応答がキャッシュされるため、Traffic Manager の使用に伴って発生する DNS 待機時間の増加がごく一部のセッションにしか適用されなくなります。

パフォーマンス方式は、利用可能な最も近いエンドポイントにトラフィックをルーティングします。 最終的に、このメソッドに関連付けられているパフォーマンス全体の影響を最小限に抑える必要があります。 DNS 待機時間の増大は、エンドポイントへのネットワーク待ち時間を短縮することで相殺する必要があります。

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Traffic Manager ではどのようなアプリケーション プロトコルを使用できますか。

[前述のとおり](#how-clients-connect-using-traffic-manager)、Traffic Manager は DNS レベルで動作します。 DNS 参照が完了すると、クライアントはアプリケーション エンドポイントに Traffic Manager 経由ではなく直接接続します。 そのため、この接続では、任意のアプリケーション プロトコルを使用できます。 ただし、Traffic Manager のエンドポイント正常性チェックには、HTTP エンドポイントか HTTPS エンドポイントが必要です。 正常性チェックのためのエンドポイントは、クライアントが接続しているアプリケーション エンドポイントと異なる場合があります。

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>"ネイキッド" ドメイン名で Traffic Manager を使用することはできますか。

いいえ。 DNS 標準では、同じ名前を持つ他の DNS レコードと CNAME が共存することは許可されていません。 DNS ゾーンの頂点 (またはルート) には、SOA と権限のある NS レコードという、既存の 2 つの DNS レコードが常に含まれます。 これは、ゾーンの頂点で CNAME レコードを作成すると DNS 標準に違反してしまうことを意味します。

「[Traffic Manager の例](#traffic-manager-example)」の説明にあるとおり、Traffic Manager では、バニティ DNS 名をマッピングするために DNS CNAME レコードが必要です。 たとえば、www.contoso.com を Traffic Manager プロファイルの DNS 名 contoso.trafficmanager.net にマッピングします。 また、Traffic Manager プロファイルも、クライアントが接続するエンドポイントを示すために、別の DNS CNAME を返します。

この問題を回避するために、HTTP リダイレクトを使用してトラフィックをネイキッド ドメイン名から別の URL に転送することをお勧めします。これにより、Traffic Manager を使用できるようになります。 たとえば、ネイキッド ドメイン "contoso.com" を使用すると、Traffic Manager の DNS 名を指す CNAME "www.contoso.com" にユーザーをリダイレクトできます。

Traffic Manager におけるネイキッド ドメインの完全サポートは、開発待ちの機能として登録されています。 この機能を要求するためにサポートを登録するには、[コミュニティ フィードバック サイトの投票](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly)で、ぜひ支持を表明してください。

## <a name="next-steps"></a>次のステップ

Traffic Manager の [エンドポイントの監視と自動フェールオーバー](traffic-manager-monitoring.md)の詳細を確認する。

Traffic Manager の [トラフィック ルーティング方法](traffic-manager-routing-methods.md)の詳細を確認する。

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png



<!--HONumber=Nov16_HO5-->


