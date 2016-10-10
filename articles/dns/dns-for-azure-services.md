<properties
  pageTitle="他の Azure サービスと共に Azure DNS を使用する | Microsoft Azure"
  description="Azure DNS を使用して 他の Azure サービスの名前を解決する方法を理解します"
  services="dns"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure dns"
/>
<tags
  ms.service="dns"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="09/21/2016"
  ms.author="sewhee"
/>

# Azure DNS を他の Azure サービスで使用する

Azure DNS は、ホストされる DNS 管理および名前解決サービスです。このサービスによって、Azure にデプロイしている他のアプリケーションやサービスのパブリック DNS 名を作成できます。カスタム ドメイン内の Azure サービスの名前の作成は、サービスに適した種類のレコードを追加するだけの単純な作業です。

* IP アドレスが動的に割り当てられる場合は、Azure でサービスに対して作成された DNS 名に対応する DNS CNAME レコードを作成する必要があります。DNS の標準により、ゾーンの頂点でCNAME レコードを使用することはできません。
* IP アドレスが静的に割り当てられる場合は、任意の名前を使用して DNS A レコードを作成でき、ゾーンの頂点で_ネイキッド ドメイン_を作成することもできます。

次の表は、さまざまな Azure サービスで使用できる、サポートされているレコードの種類の要点を示しています。この表からわかるように、Azure DNS は、インターネットに接続するネットワーク リソースの DNS レコードのみをサポートします。Azure DNS は、内部アドレスやプライベート アドレスの名前解決のために使用することはできません。

| Azure サービス | ネットワーク インターフェイス | Description |
|---------------|-------------------|-------------|
| Application Gateway | フロントエンド パブリック IP | DNS A または CNAME レコードを作成できます。 |
| Load Balancer | フロントエンド パブリック IP | DNS A または CNAME レコードを作成できます。Load Balancer には、動的に割り当てられる IPv6 パブリック IP アドレスを設定できます。したがって、IPv6 アドレス用の CNAME レコードを作成する必要があります。 |
| Traffic Manager | パブリック名 | Traffic Manager プロファイルに割り当てられている .trafficmanager.net 名にマップする CNAME のみを作成できます。詳細については、「[Traffic Manager のしくみ](../traffic-manager/traffic-manager-how-traffic-manager-works.md#traffic-manager-example)」をご覧ください。 |
| クラウド サービス | パブリック IP | 静的に割り当てられる IP アドレスには、DNS A レコードを作成できます。動的に割り当てられる IP アドレスには、_cloudapp.net_ 名にマップする CNAME レコードを作成する必要があります。クラシック ポータルで作成された VM はクラウド サービスとしてデプロイされるため、このルールが適用されます。詳細については、「[Azure クラウド サービスのカスタム ドメイン名の構成](../cloud-services/cloud-services-custom-domain-name-portal.md)」を参照してください。 |
| App Service | 外部 IP | 外部 IP アドレスには、DNS A レコードを作成できます。それ以外の場合は、azurewebsites.net 名にマップする CNAME レコードを作成する必要があります。詳細については、「[Azure アプリへのカスタム ドメイン名のマッピング](../app-service-web/web-sites-custom-domain-name.md)」を参照してください。 |
| Resource Manager VM | パブリック IP | Resource Manager VM はパブリック IP アドレスを持つことができます。パブリック IP アドレスを持つ VM は、ロード バランサーの背後に配置されることもあります。パブリック アドレスの DNS A または CNAME レコードを作成できます。このカスタム名を使用して、ロード バランサーの VIP をバイパスできます。 |
| クラシック VM | パブリック IP | PowerShell または CLI は使用して作成したクラシック VM には、動的または静的 (予約済み) 仮想アドレスを構成できます。それぞれに DNS CNAME または A レコードを作成できます。 |

<!---HONumber=AcomDC_0928_2016-->