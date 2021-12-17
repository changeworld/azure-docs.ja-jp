---
title: Azure Firewall とは
description: Azure Firewall は、Azure Virtual Network リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperf-fy21q1
ms.date: 11/10/2021
ms.author: victorh
ms.openlocfilehash: 54f5de052c6fed4729a41e9f54c614480efc1e7e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309925"
---
# <a name="what-is-azure-firewall"></a>Azure Firewall とは

<!--- ![ICSA certification](media/overview/icsa-cert-firewall-small.png) --->

Azure Firewall は、Azure で実行されているクラウド ワークロードに最高レベルの脅威保護を提供する、クラウドネイティブでインテリジェントなネットワーク ファイアウォールのセキュリティ サービスです。 組み込みの高可用性とクラウドの無制限のスケーラビリティを備えた、完全にステートフルなサービスとしてのファイアウォールです。 これは、東西と北南の両方のトラフィック検査を提供します。

Azure Firewall は、Standard と Premium の 2 つの SKU で提供されています。

## <a name="azure-firewall-standard"></a>Azure Firewall Standard

   Azure Firewall Standard は、L3-L7 フィルタリングと脅威脅威インテリジェンスを、Microsoft Cyber Security から直接提供します。 脅威インテリジェンスベースのフィルタリングにより、既知の悪意のある IP アドレスやドメインとの間のトラフィックを警告したり、拒否したりすることができます。これらは、新たに出現した攻撃から保護するためにリアルタイムで更新されています。

   ![Firewall Standard の概要](media/overview/firewall-standard.png)

Firewall Standard の機能の詳細については、[Azure Firewall Standard の機能](features.md)に関するページを参照してください。


## <a name="azure-firewall-premium"></a>Azure Firewall Premium

   Azure Firewall Premium には、特定のパターンを見つけることで攻撃を迅速に検知することができる、シグネチャベースの IDPS などの高度な機能が備わっています。 これらのパターンには、ネットワーク トラフィックのバイト シーケンスや、マルウェアで使用される既知の悪意のある命令シーケンスなどがあります。 また、リアルタイムに更新されている 50 以上のカテゴリに分類された 58,000 以上のシグネチャにより、新たに出現した悪用方法から保護します。 悪用のカテゴリには、マルウェア、フィッシング、コインのマイニング、およびトロイの木馬攻撃が含まれます。

   ![Firewall Premium の概要](media/overview/firewall-premium.png)


Firewall Premium の機能については、[Azure Firewall Premium の機能](premium-features.md)に関するページを参照してください。


## <a name="azure-firewall-manager"></a>Azure Firewall Manager

Azure Firewall Manager を使用して、複数のサブスクリプションにまたがる Azure Firewall を一元的に管理できます。 Firewall Manager は、ファイアウォール ポリシーを活用して、共通のネットワークまたはアプリケーション ルールと構成をテナント内のファイアウォールに適用します。
 
Firewall Manager は、VNet と Virtual WAN (セキュリティ保護付き仮想ハブ) の両方の環境のファイアウォールに対応しています。 セキュリティ保護付き仮想ハブでは Virtual WAN ルートの自動化ソリューションが使用されているため、数回のクリックでファイアウォールにトラフィックをルーティングできるよう簡略化されています。

Azure Firewall Manager の詳細については、[Azure Firewall Manager](../firewall-manager/overview.md) に関するページを参照してください。

## <a name="pricing-and-sla"></a>料金と SLA

Azure Firewall の価格については、「[Azure Firewall の価格](https://azure.microsoft.com/pricing/details/azure-firewall/)」を参照してください。

Azure Firewall SLA の情報については、「[Azure Firewall の SLA](https://azure.microsoft.com/support/legal/sla/azure-firewall/)」を参照してください。

## <a name="whats-new"></a>新機能

Azure Firewall の新機能については、「[Azure の更新情報](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Firewall)」を参照してください。


## <a name="known-issues"></a>既知の問題

Azure Firewall には、次の既知の問題があります。

|問題  |説明  |対応策  |
|---------|---------|---------|
|TCP/UDP 以外のプロトコル (ICMP など) に関するネットワーク フィルタリング規則が、インターネットへのトラフィックで機能しない|TCP/UDP 以外のプロトコルに関するネットワーク フィルタリング規則は、パブリック IP アドレスへの SNAT で機能しません。 TCP/UDP 以外のプロトコルは、スポーク サブネットと VNet との間でサポートされます。|Azure Firewall では Standard Load Balancer が使用されます。[現在 Standard Load Balancer では、IP プロトコルの SNAT はサポートされていません](../load-balancer/load-balancer-overview.md)。 Microsoft は、将来のリリースでこのシナリオに対応できるよう方法を模索しています。|
|PowerShell と CLI では ICMP がサポートされない|Azure PowerShell と CLI は、ネットワーク ルールの有効なプロトコルとして ICMP をサポートしていません。|それでも、ポータルと REST API を介して ICMP をプロトコルとして使用することが可能です。 近いうちに PowerShell と CLI に ICMP を追加するよう取り組んでいます。|
|FQDN タグで port:protocol を設定する必要がある|FQDN タグを使用するアプリケーション ルールには、port:protocol の定義が必要です。|port:protocol 値として、**https** を使用できます。 FQDN タグの使用時にこのフィールドを省略可能にするため、取り組みを進めています。|
|ファイアウォールを別のリソース グループまたはサブスクリプションへ移動することはサポートされていません|ファイアウォールを別のリソース グループまたはサブスクリプションへ移動することはサポートされていません。|この機能は今後サポートされる予定です。 ファイアウォールを別のリソース グループまたはサブスクリプションに移動するには、現在のインスタンスを削除して、新しいリソース グループまたはサブスクリプション内に再作成する必要があります。|
|脅威インテリジェンス アラートがマスクされることがある|アラートのみのモードに構成されている場合、送信フィルター処理用の宛先 80/443 のネットワーク ルールによって脅威インテリジェンス アラートがマスクされます。|アプリケーション ルールを使用して 80/443 の送信フィルター処理を作成します。 または、脅威インテリジェンス モードを **[Alert and Deny]\(アラートと拒否\)** に変更します。|
|Azure Firewall DNAT がプライベート IP 送信先で機能しない|Azure Firewall DNAT のサポートは、インターネット エグレスまたはイングレスに制限されています。 現在、DNAT はプライベート IP 送信先で機能しません。 たとえば、スポークからスポークです。|これは現在の制限です。|
|最初のパブリック IP の構成を削除できない|Azure Firewall の各パブリック IP アドレスは、"*IP 構成*" に割り当てられています。  最初の IP 構成はファイアウォールのデプロイ中に割り当てられ、通常、それにはファイアウォールのサブネットへの参照も含まれます (テンプレートのデプロイによって別に明示的に構成されていない場合)。 この IP 構成を削除するとファイアウォールの割り当てが解除されるため、削除できません。 ただし、ファイアウォールで使用できるパブリック IP アドレスが他に 1 つ以上ある場合は、この IP 構成に関連付けられているパブリック IP アドレスを変更または削除できます。|これは仕様です。|
|Availability Zones は、デプロイ時にのみ構成できます。|Availability Zones は、デプロイ時にのみ構成できます。 ファイアウォールがデプロイされた後、Availability Zones を構成することはできません。|これは仕様です。|
|受信接続での SNAT|DNAT に加えて、ファイアウォールのパブリック IP アドレスを使用した (受信) 接続は SNAT によっていずれかのファイアウォールのプライベート IP に変換されます。 対称的なルーティングを実現するために、現在このような要件が (アクティブ/アクティブ NVA に対しても) 適用されます。|HTTP/S の元の送信元を保持するには、[XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) ヘッダーを使用することを検討します。 たとえば、ファイアウォールの直前に [Azure Front Door](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) や [Azure Application Gateway](../application-gateway/rewrite-http-headers-url.md) などのサービスを使用します。 Azure Front Door とチェーンの一部としてファイアウォールに WAF を追加することもできます。
|SQL の FQDN のフィルター処理がプロキシ モードでのみサポートされる (ポート 1433)|Azure SQL Database、Azure Synapse Analytics、Azure SQL Managed Instance の場合:<br><br>SQL の FQDN のフィルター処理は、プロキシ モードのみでサポートされます (ポート 1433)。<br><br>Azure SQL IaaS の場合:<br><br>標準以外のポートを使っている場合は、アプリケーション ルールでそれらのポートを指定できます。|リダイレクト モードの SQL (Azure 内から接続する場合の既定) では、代わりに Azure Firewall ネットワーク ルールの一部として SQL サービス タグを使ってアクセスをフィルター処理できます。
|TCP ポート 25 でアウトバウンド SMTP トラフィックがブロックされている|TCP ポート 25 の外部ドメイン (`outlook.com` や `gmail.com` など) に直接送信されるアウトバウンド電子メール メッセージは Azure Firewall によってブロックされます。 これは、Azure での既定のプラットフォーム動作です。 |認証済みの SMTP リレー サービスを使用してください。これは、通常は TCP ポート 587 を経由して接続しますが、他のポートもサポートします。  詳細については、[Azure でのアウトバウンド SMTP 接続問題のトラブルシューティング](../virtual-network/troubleshoot-outbound-smtp-connectivity.md)に関するページを参照してください。 現在、Azure Firewall はアウトバウンド TCP 25 を使用してパブリック IP と通信できる場合がありますが、動作は保証されておらず、すべてのサブスクリプションの種類でサポートされているわけではありません。 仮想ネットワーク、VPN、Azure ExpressRoute のようなプライベート IP の場合、Azure Firewall は TCP ポート 25 のアウトバウンド接続をサポートしています。
|SNAT ポートの枯渇|Azure Firewall では現在、バックエンドの仮想マシン スケール セット インスタンス 1 つにつき、パブリック IP アドレスごとに 1,024 個のポートがサポートされます。 既定では、2 つの仮想マシン スケール セット インスタンスがあります。|これは SLB の制限であり、Microsoft は現在、この制限の引き上げを検討しています。 その間、SNAT 枯渇の影響を受けやすいデプロイについては、パブリック IP アドレスを最低でも 5 つ使用して Azure Firewall のデプロイを構成するようをお勧めします。 そうすれば、使用できる SNAT ポートは 5 倍になります。 ダウンストリームのアクセス許可を簡素化するために、IP アドレス プレフィックスから割り当ててください。|
|強制トンネリングが有効になっている場合、DNAT はサポートされない|強制トンネリングが有効になった状態でデプロイされているファイアウォールは、非対称ルーティングのため、インターネットからの受信アクセスをサポートできません。|これは、非対称ルーティングのための仕様です。 受信接続のリターン パスは、確立された接続が検出されていないオンプレミスのファイアウォールを経由します。
|FTP サーバーの構成によっては、複数のパブリック IP アドレスがあるファイアウォールでは、アウトバウンド パッシブ FTP が機能しないことがあります。|パッシブ FTP は、コントロールとデータのチャネルに対して異なる接続を確立します。 複数のパブリック IP アドレスを持つファイアウォールは、送信データを送信するときに、ソース IP アドレスとしてパブリック IP アドレスの 1 つをランダムに選択します。 データ チャネルとコントロール チャネルとで異なる送信元 IP アドレスが使用されていると、FTP サーバーの構成によっては FTP が失敗することがあります。|明示的な SNAT 構成が計画されています。 その間は、異なる送信元 IP アドレスからのデータ チャネルとコントロール チャネルを受け入れるように FTP サーバーを構成することができます ([IIS の例](/iis/configuration/system.applicationhost/sites/sitedefaults/ftpserver/security/datachannelsecurity)を参照)。 ただし、このケースでは、1 つの IP アドレスを使用することを検討してください。|
|FTP サーバーの構成によっては、インバウンド パッシブ FTP が機能しないことがあります。 |パッシブ FTP は、コントロールとデータのチャネルに対して異なる接続を確立します。 対称的なルーティングを実現するために、Azure Firewall へのインバウンド接続は、ファイアウォールのいずれかのプライベート IP アドレスに SNAT 変換されます。 データ チャネルとコントロール チャネルとで異なる送信元 IP アドレスが使用されていると、FTP サーバーの構成によっては FTP が失敗することがあります。|元の送信元 IP アドレスの保持機能を調査中です。 その間は、異なる送信元 IP アドレスからのデータ チャネルとコントロール チャネルを受け入れるように FTP サーバーを構成することができます。|
|FTP クライアントがインターネット上の FTP サーバーに到達する必要がある場合、アクティブ FTP が機能しない。|アクティブ FTP では、FTP クライアントからの PORT コマンドを使用して、データ チャネルに使用する IP とポートを FTP サーバーに指示します。 この PORT コマンドは、変更できないクライアントのプライベート IP を使用します。 Azure Firewall を通過するクライアント側のトラフィックはインターネット ベースの通信でネットワーク アドレス変換されます。これにより、PORT コマンドは FTP サーバーによって無効と見なされます。|これは、アクティブ FTP をクライアント側 NAT と一緒に使用する場合の一般的な制限です。|
|NetworkRuleHit メトリックにプロトコル ディメンションがない|ApplicationRuleHit メトリックでは、プロトコルに基づいたフィルター処理が許可されていますが、対応する NetworkRuleHit メトリックにこの機能がありません。|解決策を調査中です。|
|64000 から 65535 の範囲のポートを使用した NAT ルールはサポートされません。|Azure Firewall では、ネットワーク ルールとアプリケーション ルールで 1 から 65535 の範囲の任意のポートを使用できますが、NAT ルールでサポートされるのは、1 から 63999 の範囲のポートのみです。|これは現在の制限です。
|構成の更新に平均 5 分かかる|Azure Firewall 構成の更新は平均で 3 から 5 分かかる場合があり、並列更新はサポートされていません。|解決策を調査中です。|
|Azure Firewall では、HTTPS トラフィックと MSSQL トラフィックのフィルター処理に SNI TLS ヘッダーが使用される|ブラウザーまたはサーバー ソフトウェアが Server Name Indicator (SNI) 拡張機能をサポートしていない場合は、Azure Firewall 経由で接続することがはできません。|ブラウザーまたはサーバー ソフトウェアが SNI をサポートしていない場合は、アプリケーション ルールではなくネットワーク ルールを使用して接続を制御できます。 SNI をサポートするソフトウェアについては、「[Server Name Indication](https://wikipedia.org/wiki/Server_Name_Indication)」を参照してください。|
|カスタム DNS が強制トンネリングで正しく機能しない|強制トンネリングが有効になっている場合、カスタム DNS は正しく機能しません。|解決策を調査中です。|
|強制トンネリング モードで構成されたファイアウォールで開始と停止が機能しない|強制トンネリング モードで構成された Azure ファイアウォールで、開始と停止が機能しません。 強制トンネリングが構成された Azure Firewall を起動しようとすると、次のエラーが発生します。<br><br>*Set-AzFirewall: AzureFirewall FW-xx management IP configuration cannot be added to an existing firewall. Redeploy with a management IP configuration if you want to use forced tunneling support. (Set-AzFirewall: AzureFirewall FW-xx の管理 IP 構成を既存のファイアウォールに追加できません。強制トンネリング機能を使用したい場合は、管理 IP 構成で再デプロイしてください。)<br>StatusCode: 400<br>ReasonPhrase: Bad Request (無効な要求)*|調査中。<br><br>この問題は、既存のファイアウォールを削除してから、同じパラメーターで新しいファイアウォールを作成することで回避できます。|
|ポータルまたは Azure Resource Manager (ARM) テンプレートを使用してファイアウォール ポリシー タグを追加できない|Azure Firewall ポリシーにはパッチ サポートの制限があり、Azure portal または ARM テンプレートを使用してタグを追加することはできません。 次のエラーが発生します: "*リソースのタグを保存できませんでした*"。|解決策を調査中です。 または、Azure PowerShell のコマンドレット `Set-AzFirewallPolicy` を使用してタグを更新することもできます。|
|IPv6 は現在、サポートされていない|IPv6 アドレスをルールに追加した場合、ファイアウォールのエラーが発生します。|IPv4 アドレスのみを使用してください。 IPv6 のサポートは調査中です|
|複数の IP グループを更新すると、競合エラーが発生して失敗します。|同じファイアウォールにアタッチされている複数の IP グループを更新すると、いずれかのリソースがエラー状態になります。|これは既知の問題および制限です。 <br><br>IP グループを更新すると、IP グループがアタッチされているすべてのファイアウォールで更新がトリガーされます。 ファイアウォールがまだ "*更新中*" の状態であるときに 2 番目の IP グループへの更新が開始された場合、IP グループの更新は失敗します。<br><br>この失敗を回避するには、同じファイアウォールにアタッチされている IP グループを一度に 1 つずつ更新する必要があります。 各更新の間に十分な時間を確保して、ファイアウォールが "*更新中*" の状態を終了できるようにします。|
|ARM テンプレートを使用した RuleCollectionGroups の削除はサポートされていません。|ARM テンプレートを使用した RuleCollectionGroup の削除はサポートされていないため、エラーが発生します。|これはサポートされている操作ではありません。|
|*any* (*) を許可する DNAT ルールではトラフィックが SNAT (送信元ネットワーク アドレス変換) されます。|DNAT ルールによって送信元 IP アドレスとして *any* (*) が許可される場合、暗黙的なネットワーク ルールによって VNet-VNet トラフィックが照合され、トラフィックが常に SNAT されます。|これは現在の制限です。|
|セキュリティ プロバイダーを使用してセキュリティ保護付き仮想ハブに DNAT ルールを追加することはできません。|これは結果的に、返される DNAT トラフィックの非同期ルートになり、これがセキュリティ プロバイダーに送信されます。|サポートされていません。|
| 2000 個を超えるルール コレクションを作成するときにエラーが発生しました。 | NAT/アプリケーションまたはネットワークのルール コレクションの最大数は、2000 個です (Resource Manager の制限)。 | これは現在の制限です。 |
|Azure Firewall のログにネットワーク ルール名が表示されない|Azure Firewall のネットワーク ルールのログ データに、ネットワーク トラフィックのルール名が表示されません。|これをサポートするための機能が検討されています。|

## <a name="next-steps"></a>次のステップ

- [クイックスタート: Azure ファイアウォールとファイアウォール ポリシーを作成する - ARM テンプレート](../firewall-manager/quick-firewall-policy.md)
- [クイック スタート:可用性ゾーンを使用して Azure Firewall をデプロイする - ARM テンプレート](deploy-template.md)
- [チュートリアル:Azure portal を使用して Azure Firewall をデプロイして構成する](tutorial-firewall-deploy-portal.md)
- [Learn モジュール: Azure Firewall の概要](/learn/modules/introduction-azure-firewall/)
