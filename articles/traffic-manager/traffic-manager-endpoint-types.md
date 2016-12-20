---
title: "Traffic Manager エンドポイントの種類 | Microsoft Docs"
description: "この記事では、Azure Traffic Manager で使用できるさまざまなエンドポイントの種類について説明します"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 4e506986-f78d-41d1-becf-56c8516e4d21
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 69b94c93ad3e9c9745af8485766b4237cac0062c
ms.openlocfilehash: 2ced9e73a65160f4f3c8ba92affc143ca554d07c

---

# <a name="traffic-manager-endpoints"></a>Traffic Manager エンドポイント
Microsoft Azure Traffic Manager を使用すると、世界中のさまざまなデータ センターで実行されているアプリケーションのデプロイに、ネットワーク トラフィックを分散させる方法を制御できます。 Traffic Manager では、各アプリケーションのデプロイを 'エンドポイント' として構成します。 Traffic Manager が DNS 要求を受信すると、DNS 応答で返すことができるエンドポイントを選択します。 Traffic Manager は、現在のエンドポイントの状態とトラフィック ルーティング方法に基づいて選択を行います。 詳細については、「 [Traffic Manager のしくみ](traffic-manager-how-traffic-manager-works.md)」をご覧ください。

Traffic Manager がサポートするエンドポイントには、次の 3 種類があります。

* **Azure エンドポイント** : Azure でホストされるサービスで使用されます。
* **外部エンドポイント** : Azure 外でホストされるサービス (オンプレミス サービスまたはホスティング プロバイダーが異なるサービス) で使用されます。
* **入れ子になったエンドポイント** : Traffic Manager プロファイルを組み合わせて、より柔軟なトラフィック ルーティング スキームを作成し、より大規模で複雑なデプロイのニーズに対応するために使用されます。

さまざまなエンドポイントの種類を 1 つの Traffic Manager プロファイルにまとめる方法に制限はありません。 各プロファイルでは、エンドポイントの種類を好きなように組み合わせることができます。

次のセクションでは、各エンドポイントの種類についてさらに詳しく説明します。

## <a name="azure-endpoints"></a>Azure エンドポイント

Azure エンドポイントは、Traffic Manager で Azure ベースのサービスに使用されます。 次の Azure リソースの種類がサポートされています。

* '従来の' IaaS VM と PaaS クラウド サービス。
* Web Apps
* PublicIPAddress リソース (直接、または Azure Load Balancer を介して VM に接続可能) publicIpAddress には、Traffic Manager プロファイルで使用するために DNS 名を割り当てておく必要があります。

PublicIPAddress リソースは Azure Resource Manager のリソースです。 クラシック デプロイ モデルには存在しません。 つまり、Traffic Manager の Azure Resource Manager エクスペリエンスでのみサポートされます。 その他の種類のエンドポイントは、リソース マネージャーとクラシック デプロイ モデルの両方でサポートされます。

Traffic Manager は、Azure エンドポイントを使用して、'従来の' IaaS VM、クラウド サービス、または Web アプリケーションの停止と開始を検出します。 この状態は、エンドポイントの状態に反映されます。 詳細については、[Traffic Manager のエンドポイント監視](traffic-manager-monitoring.md#endpoint-and-profile-status)に関する記事をご覧ください。 基になるサービスが停止すると、Traffic Manager では、エンドポイントの正常性チェックや、エンドポイントへのトラフィックの転送が行われません。 停止したインスタンスに対しては、Traffic Manager の課金イベントは発生しません。 サービスが再起動されると課金が再開され、エンドポイントはトラフィックを受け取れるようになります。 この検出機能は、PublicIpAddress エンドポイントには適用されません。

## <a name="external-endpoints"></a>外部エンドポイント

Azure 外部のサービスには、外部エンドポイントが使用されます。 オンプレミスのホスト サービスや他のプロバイダーなどがその例です。 外部エンドポイントは個別に使用することも、同じ Traffic Manager プロファイルで Azure エンドポイントと組み合わせることもできます。 Azure エンドポイントと外部エンドポイントを組み合わせると、次のようにさまざまなシナリオが可能になります。

* アクティブ/アクティブまたはアクティブ/パッシブ フェールオーバー モデルでは、Azure を使用して既存のオンプレミス アプリケーションの冗長性を向上させます。
* 世界各地のユーザーのアプリケーションの待機時間を減らすためには、Azure で既存のオンプレミス アプリケーションを他の主要地域に拡張します。 詳細については、[Traffic Manager の 'パフォーマンス' によるトラフィック ルーティング](traffic-manager-routing-methods.md#performance-traffic-routing-method)に関する記事をご覧ください。
* Azure を使用して、既存のオンプレミス アプリケーションの容量を、継続的に、または 'クラウドへのバースト' として追加し、需要の急増に対処します。

場合によっては、外部エンドポイントを使用して Azure サービスを参照すると便利です (具体例については、「[FAQ](#faq)」を参照)。 この場合、正常性チェックは、外部エンドポイントではなく、Azure エンドポイントの料金で課金されます。 ただし、Azure エンドポイントとは異なり、基になるサービスを停止または削除しても、該当する正常性チェックへの課金は、Traffic Manager でエンドポイントを無効にするか削除しない限り停止されません。

## <a name="nested-endpoints"></a>入れ子になったエンドポイント

入れ子になったエンドポイントでは、複数の Traffic Manager プロファイルを組み合わせて、柔軟なトラフィック ルーティング スキームを作成し、大規模で複雑なデプロイのニーズに対応できます。 入れ子になったエンドポイントでは、’子’ プロファイルがエンドポイントとして ’親’ プロファイルに追加されます。 子と親の両方のプロファイルには、入れ子になった他のプロファイルなど、あらゆる種類の他のエンドポイントを含めることができます。 詳細については、「 [入れ子になった Traffic Manager プロファイル](traffic-manager-nested-profiles.md)」をご覧ください。

## <a name="web-apps-as-endpoints"></a>エンドポイントとしての Web アプリ

Traffic Manager で Web アプリをエンドポイントとして構成するときの追加の考慮事項を次に示します。

1. Web Apps を Traffic Manager で使用するには、その SKU が ’Standard’ 以上である必要があります。 SKU が低い Web アプリケーションの追加の試みが失敗します。 既存の Web アプリケーションの SKU をダウングレードすると、Traffic Manager がその Web アプリケーションにトラフィックを送信しなくなります。
2. エンドポイントが HTTP 要求を受信すると、その要求の 'host' ヘッダーを使用して、どの Web アプリケーションで要求を処理するかを判断します。 ホスト ヘッダーには、'contosoapp.azurewebsites.net' など、要求を開始するための DNS 名が含まれています。 別の DNS 名を Web アプリケーションで使用するには、その DNS 名を、アプリのカスタム ドメイン名として登録する必要があります。 Web アプリケーション エンドポイントを Azure エンドポイントとして追加すると、Traffic Manager プロファイルの DNS 名は、そのアプリに自動的に登録されます。 この登録は、エンドポイントが削除されると、自動的に削除されます。
3. 各 Traffic Manager プロファイルでは、Azure リージョンごとに最大 1 つの Web アプリ エンドポイントを使用できます。 この制約を回避するには、Web アプリケーションを外部エンドポイントとして構成します。 詳細については、[FAQ](#faq) をご覧ください。

## <a name="enabling-and-disabling-endpoints"></a>エンドポイントの有効化と無効化

Traffic Manager でエンドポイントを無効にすると、メンテナンス モードのエンドポイントや再デプロイされるエンドポイントから一時的にトラフィックを削除するときに便利です。 エンドポイントが再度稼働状態になったら、もう一度有効にできます。

エンドポイントを有効および無効にするには、Resource Manager と クラシック デプロイ モデルの両方でサポートされている、Traffic Manager ポータル、PowerShell、CLI、または REST API を使用します。

> [!NOTE]
> エンドポイントの無効化は、デプロイメント状態とは関係ありません。 Azure サービス (VM または Web アプリケーションなど) は実行状態のままとなり、Traffic Manager で無効になってもトラフィックを受信できます。 トラフィックは、Traffic Manager プロファイルの DNS 名を介してではなく、サービス インスタンスに直接送信できます。 詳細については、「 [Traffic Manager のしくみ](traffic-manager-how-traffic-manager-works.md)」をご覧ください。

現在、各エンドポイントがトラフィックを受信できるかどうかは、次の要因によって決まります。

* プロファイルの状態 (有効または無効)
* エンドポイントの状態 (有効または無効)
* そのエンドポイントの正常性チェックの結果

詳細については、 [Traffic Manager のエンドポイント監視](traffic-manager-monitoring.md#endpoint-and-profile-status)に関する記事をご覧ください。

> [!NOTE]
> Traffic Manager は DNS レベルで動作するため、いずれかのエンドポイントとの既存の接続に影響を与えることはありません。 エンドポイントが利用できない場合、Traffic Manager は新しい接続を使用可能な別のエンドポイントへ誘導します。 ただし、無効または異常なエンドポイントの外側にあるホストは、セッションが終了するまで既存の接続を介してトラフィックを受信し続ける可能性があります。 トラフィックが既存の接続に転送されないようにするには、セッション期間をアプリケーションで制限する必要があります。

プロファイル内のすべてのエンドポイントまたはプロファイル自体が無効になっている場合、Traffic Manager は 'NXDOMAIN' 応答を新しい DNS クエリに送信します。

## <a name="faq"></a>FAQ

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>複数のサブスクリプションのエンドポイントで Traffic Manager を使用できますか。

Azure Web Apps では、複数のサブスクリプションからのエンドポイントを使用できません。 Azure Web Apps の要件により、Web Apps で使用するカスタム ドメイン名を使用できるのは 1 つのサブスクリプション内に限定されます。 複数のサブスクリプション内で同一のドメイン名を持つ Web Apps を使用することはできません。

他の種類のエンドポイントの場合は、複数のサブスクリプションのエンドポイントで Traffic Manager を使用できます。 Traffic Manager の構成方法は、クラシック デプロイ モデルまたはリソース マネージャーのエクスペリエンスのどちらを使用しているかによって異なります。

* Resource Manager では、すべてのサブスクリプションのエンドポイントを Traffic Manager に追加できますが、それには、Traffic Manager プロファイルを構成しているユーザーに、エンドポイントへの読み取りアクセス権が付与されている必要があります。 こうしたアクセス許可を付与するには、 [Azure Resource Manager のロール ベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-configure.md)を使用します。
* クラシック デプロイ モデル インターフェイスでは、Azure エンドポイントとして構成された Cloud Services や Web Apps が、Traffic Manager プロファイルと同じサブスクリプションに存在していることが、Traffic Manager によって求められます。 その他のサブスクリプションにあるクラウド サービスのエンドポイントは、'外部' エンドポイントとして Traffic Manager に追加できます。 これらの外部エンドポイントは、外部レートではなく Azure エンドポイントとして課金されます。

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>クラウド サービス 'Staging' スロットで Traffic Manager を使用できますか。

はい。 クラウド サービス 'Staging' スロットは、Traffic Manager で外部エンドポイントとして構成できます。 正常性チェックには、Azure エンドポイントの料金が課金されます。 使用されているエンドポイントの種類は外部エンドポイントです。したがって、基になるサービスへの変更は自動的には反映されません。 外部エンドポイントでは、Traffic Manager はクラウド サービスが停止または削除されたタイミングを検出できません。 そのため、Traffic Manager では、エンドポイントが無効になるか、削除されるまで、正常性チェックへの課金を続けます。

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Traffic Manager では IPv6 エンドポイントはサポートされていますか。

現在、Traffic Manager では、IPv6 アドレスに対応するネーム サーバーを提供していません。 ただし、Traffic Manager は、IPv6 エンドポイントに接続する IPv6 クライアントでも使用できます。 クライアントが Traffic Manager に対して DNS 要求を直接行うことはなく、 代わりに、再帰的な DNS サービスを使用します。 IPv6 専用のクライアントは、IPv6 経由で再帰的な DNS サービスに要求を送信します。 その後、再帰サービスは、IPv4 を使用して Traffic Manager ネーム サーバーに接続します。

Traffic Manager はエンドポイントの DNS 名を返します。 IPv6 エンドポイントをサポートするには、エンドポイント DNS 名が IPv6 アドレスを指す DNS AAAA レコードが必要です。 Traffic Manager の正常性チェックは、IPv4 アドレスのみをサポートしています。 サービスは、同じ DNS 名で IPv4 エンドポイントを公開する必要があります。

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>同じリージョン内の複数の Web アプリで Traffic Manager を使用できますか。

通常、Traffic Manager は、さまざまなリージョンにデプロイされたアプリケーションにトラフィックを送信するときに使用されます。 ただし、この Traffic Manager は、同じリージョンに複数のアプリケーションがデプロイされている場合も使用できます。 Traffic Manager Azure エンドポイントでは、同じ Azure リージョンの複数の Web アプリケーション エンドポイントを、同じ Traffic Manager プロファイルに追加することはできません。

次の手順により、この制約を回避することができます。

1. エンドポイントが別の Web アプリケーション 'スケール ユニット' に存在することを確認します。 ドメイン名は特定のスケール ユニット内の 1 つのサイトにマップする必要があります。 したがって、同じスケール ユニット内の 2 つの Web アプリケーションは、Traffic Manager プロファイルを共有できません。
2. 各 Web アプリケーションにカスタム ホスト名としてバニティ ドメイン名を追加します。 各 Web アプリケーションは、別のスケール ユニット内に存在する必要があります。 Web アプリケーションは、すべて同じサブスクリプションに属する必要があります。
3. Traffic Manager プロファイルに Web アプリケーション エンドポイントを Azure エンドポイントとして 1 つだけ追加します。
4. 追加の各 Web アプリ エンドポイントを、外部エンドポイントとして Traffic Manager プロファイルに追加します。 外部エンドポイントを追加する場合は、Resource Manager デプロイ モデルを使用する必要があります。
5. Traffic Manager プロファイルの DNS 名 (<…>.trafficmanager.net) をポイントするバニティ ドメインで、DNS CNAME レコードを作成します。
6. Traffic Manager プロファイルの DNS 名ではなく、バニティ ドメイン名を使用してサイトにアクセスします。

## <a name="next-steps"></a>次のステップ

* [Traffic Manager のしくみ](traffic-manager-how-traffic-manager-works.md)を確認します。
* Traffic Manager の [エンドポイントの監視と自動フェールオーバー](traffic-manager-monitoring.md)を確認します。
* Traffic Manager の [トラフィック ルーティング方法](traffic-manager-routing-methods.md)を確認します。




<!--HONumber=Nov16_HO3-->


