<properties
   pageTitle="Compute、Network、Storage プロバイダー | Microsoft Azure"
   description="Azure リソース マネージャーの Compute、Network、Strage リソース プロバイダー (CRP、NRP、SRP) の概念の概要"
   services="virtual-machines"
   documentationCenter=""
   authors="mahthi"
   manager="timlt"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="04/29/2015"
   ms.author="mahthi"/>

# Azure リソース マネージャーにおける Compute、Network、Strage プロバイダー

Azure リソース マネージャーに Compute、Network、Strage の機能が追加されたことによって、IaaS 上で動作する複雑なアプリケーションのデプロイと管理が大幅に単純化されます。多くのアプリケーションは、仮想ネットワーク、ストレージ アカウント、仮想マシン、ネットワーク インターフェイスなど、リソースの組み合わせを必要とします。Azure リソース マネージャーには、そうしたリソースをすべてひとまとめにして単一のアプリケーションとしてデプロイし、管理する JSON テンプレートを作成する機能が用意されています。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## Azure リソース マネージャー下に Compute、Network、Strage を統合する利点

Azure リソース マネージャーでは、既製のアプリケーション テンプレートを容易に活用できます。また、Azure に Compute、Network、Strage の各リソースをデプロイして管理するためのアプリケーション テンプレートを新たに作成することもできます。このセクションでは、Azure リソース マネージャーを使用してリソースをデプロイする利点について説明します。

-	複雑な作業を単純化 -- 共有可能なテンプレート ファイルからあらゆる Azure リソース (Websites、SQL Database、Virtual Machines、Virtual Network など) を含んだ複合的なアプリケーションを構築、統合し、共同作業を行うことができます。
-	同じテンプレート ファイルを使用することで、開発、DevOps、システム管理者向けに、柔軟性に優れたデプロイを反復的に行うことができます。
-	VM 拡張機能 (カスタム スクリプト、DSC、Chef、Puppet など) と Azure リソース マネージャーがテンプレート ファイル内で密に統合され、VM 内のセットアップ構成の編成が容易になります。
-	Compute、Network、Strage リソース用に、タグとその課金の伝達を定義できます。
-	Azure のロールベースの Access Control (RBAC) を使用して、組織的なリソース アクセス管理を単純かつ厳密に行うことができます。
-	元のテンプレートに変更を加えて再度デプロイすることでアップグレード/更新を単純化することができます。


## Azure リソース マネージャーにおける Compute、Network、Strage API の進化

リリースされている API には、上記の利点に加え、パフォーマンス面で強化された点がいくつかあります。

-	大量の Virtual Machines の並列デプロイが可能になりました。
-	可用性セットで 3 つの障害ドメインがサポートされます。
-	カスタム スクリプト拡張機能が強化され、パブリックにアクセスできるカスタム URL からのスクリプトの指定が可能になりました。
- Virtual Machines と Azure Key Vault が統合され、[FIPS 検証済みの](http://wikipedia.org/wiki/FIPS_140-2)[ハードウェア セキュリティ モジュール](http://wikipedia.org/wiki/Hardware_security_module)によってシークレットを安全に保管し、プライベートなデプロイを実現します。
-	API を使用したネットワークの基本的なビルディングブロックを提供します。お客様は、ネットワーク インターフェイス、Load Balancer、Virtual Network を含んだ複合的なアプリケーションを構築できます。
-	新しいオブジェクトとしてのネットワーク インターフェイスによって、Virtual Machines に複雑なネットワーク構成を維持し、再利用することができます。
-	最上位のリソースとしての Load Balancer によって IP アドレスの割り当てが可能となります。
-	粒度の細かい Virtual Network API によって、個々の Virtual Network の管理が容易になります。

## 新たに導入された API との概念上の相違点

このセクションでは、現在利用できる XML ベースの API とCompute、Network、Storage に関して Azure リソース マネージャーを通じて使用できる JSON ベース API との特に重要な概念上の相違点をいくつか説明します。

 項目 | Azure サービス管理 (XML ベース) | Compute、Network、Strage プロバイダー (JSON ベース)
 ---|---|---
| Virtual Machines 用クラウド サービス |	クラウド サービスは、プラットフォームに基づく可用性と負荷分散を必要とする仮想マシンを保持するためのコンテナーです。 | 新しいモデルを使用して仮想マシンを作成するためのオブジェクトとしてのクラウド サービスは不要となりました。 |
| 可用性セット | プラットフォームに対する可用性は、Virtual Machines に同じ "AvailabilitySetName" を構成することによって示されます。障害ドメインの最大数は 2 です。 | 可用性セットは、Microsoft.Compute プロバイダーによって公開されるリソースです。可用性セットには、高可用性を必要とする Virtual Machines を含める必要があります。障害ドメインの最大数は 3 です。 |
| [アフィニティ グループ] をクリックし、 |	Virtual Network を作成するにはアフィニティ グループが必要です。ただし、Regional Virtual Network の導入により、それは不要となりました。 |単純化するために、Azure リソース マネージャーによって公開される API には、アフィニティ グループの概念が存在しません。 |
| 負荷分散 | デプロイされている Virtual Machines には、クラウド サービスの作成によって暗黙的な Load Balancer が提供されます。 | Load Balancer は、Microsoft.Network プロバイダーによって公開されるリソースです。負荷分散を必要とする Virtual Machines のプライマリ ネットワーク インターフェイスは、Load Balancer を参照している必要があります。Load Balancer には、内部 Load Balancer と外部 Load Balancer とがあります。[詳細については、こちらを参照してください。](resource-groups-networking.md) |
|仮想 IP アドレス | Cloud Services に VM を追加すると、既定の VIP (仮想 IP アドレス) がクラウド サービスに与えられます。仮想 IP アドレスは、暗黙的なロード バランサーに関連付けられるアドレスです。 | パブリック IP アドレスは、Microsoft.Network プロバイダーによって公開されるリソースです。パブリック IP アドレスには、静的 (予約済み) アドレスと動的アドレスとがあります。Load Balancer には、動的パブリック IP を割り当てることができます。パブリック IP のセキュリティは、セキュリティ グループを使用して保護できます。 |
|予約済み IP アドレス|	IP アドレスを Azure で予約し、クラウド サービスに関連付けることで、その IP アドレスを固定アドレスとすることができます。 | パブリック IP アドレスは "静的" モードで作成でき、"予約済み IP アドレス" と同じ機能を持ちます。現在、Load Balancer に割り当てることができるのは静的パブリック IP だけです。 |
|VM ごとのパブリック IP アドレス (PIP) | Public IP Addresses は、直接 VM に関連付けることもできます。 | パブリック IP アドレスは、Microsoft.Network プロバイダーによって公開されるリソースです。パブリック IP アドレスには、静的 (予約済み) アドレスと動的アドレスとがあります。ただし、VM ごとのパブリック IP を取得するためにネットワーク インターフェイスに割り当てることができるのは現在、動的パブリック IP だけです。 |
|エンドポイント| 特定のポートの接続を確立するためには、仮想マシンに入力エンドポイントを構成する必要があります。入力エンドポイントを設定することによって仮想マシンに接続する一般的なモードの 1 つ。 | VM への接続用に特定のポートのエンドポイントを有効にする機能は、Load Balancer に受信 NAT ルールを構成することで実現できます。 |
|DNS 名| クラウド サービスには、グローバルに一意となる暗黙的な DNS 名が与えられます (例: `mycoffeeshop.cloudapp.net`)。 | DNS 名は、パブリック IP アドレス リソースに指定できる省略可能なパラメーターです。FQDN は、`<domainlabel>.<region>.cloudapp.azure.com` という形式になります。 |
|ネットワーク インターフェイス | プライマリとセカンダリのネットワーク インターフェイスおよびそのプロパティは、仮想マシンのネットワーク構成として定義されます。 | ネットワーク インターフェイスは、Microsoft.Network プロバイダーによって公開されるリソースです。ネットワーク インターフェイスのライフサイクルは、仮想マシンに関連付けられません。 |

## Virtual Machines 用 Azure テンプレートの概要

Azure テンプレートを初めて使用する際は、開発とプラットフォームへのデプロイを目的とした各種のツールが用意されているのでご利用ください。

### Azure ポータル

Azure ポータルには、Virtual Machines のクラシック デプロイ モデルと、Virtual Machines のリソース マネージャー デプロイ モデルのデプロイ オプションが引き続き同時にあります。Azure ポータルでカスタム テンプレートのデプロイを行うこともできます。

### Azure PowerShell

Azure PowerShell には、**AzureServiceManagement** モードと **AzureResourceManager** モードの 2 つのデプロイ モードがあります。AzureResourceManager モードにはさらに、Virtual Machines、Virtual Network、ストレージ アカウントを管理するためのコマンドレットが含まれます。詳細については、[こちら](../powershell-azure-resource-manager.md)を参照してください。

### Azure CLI

Azure コマンドライン インターフェイス (Azure CLI) には、**AzureServiceManagement** モードと **AzureResourceManager** モードの 2 つのデプロイ モードがあります。AzureResourceManager モードにはさらに、Virtual Machines、Virtual Network、ストレージ アカウントを管理するためのコマンドが含まれます。詳細については、[こちら](xplat-cli-azure-resource-manager.md)を参照してください。

### Visual Studio

最新の Visual Studio 用 Azure SDK リリースでは、Virtual Machines と複雑なアプリケーションを Visual Studio からすばやく作成してデプロイすることができます。Visual Studio には、一連の既製のテンプレートからデプロイする機能が用意されているほか、空のテンプレートを出発点とすることもできます。

### REST API

コンピューティング、ネットワーク、ストレージ リソース プロバイダーの REST API に関する詳細なドキュメントは、[こちら](https://msdn.microsoft.com/library/azure/dn790568.aspx)でご覧いただけます。

## よく寄せられる質問

**Azure サービス管理 API を使用して作成した仮想マシンまたはストレージ アカウントにデプロイする仮想マシンを、新しい Azure リソース マネージャーで作成することはできますか。**

現時点ではサポートされません。新しい Azure リソース マネージャー API では、サービス管理 API を使用して作成された仮想ネットワークに仮想マシンをデプロイすることはできません。

**新しい Azure リソース マネージャー API で、Azure サービス管理 API を使用して作成されたユーザー イメージから仮想マシンを作成することはできますか。**

現時点ではサポートされません。ただし、サービス管理 API で作成したストレージ アカウントから VHD ファイルをコピーし、新しい Azure リソース マネージャー API で作成した新しいアカウントにコピーすることはできます。

**サブスクリプションのクォータにはどのような影響が生じますか。**

新しい Azure リソース マネージャー API を介して作成された Virtual Machines、Virtual Network、ストレージ アカウントのクォータは、お客様が現在保有しているクォータとは区別されます。新しい API を使用してリソースを作成するためのクォータが別途、各サブスクリプションに与えられます。追加クォータの詳細については、[こちら](../azure-subscription-service-limits.md)を参照してください。

**Virtual Machines、Virtual Network、ストレージ アカウントなどをプロビジョニングするための独自の自動スクリプトは、新しい Azure リソース マネージャー API で引き続き使用できますか。**

これまでに作成したオートメーションやスクリプトはすべて、Azure サービス管理モードで作成された既存の Virtual Machines、Virtual Network にも使用できます。ただし、新しい Azure リソース マネージャー モードで同じリソースを作成するためには、新しいスキーマを使用するようにスクリプトを更新する必要があります。Azure CLI スクリプトを変更する方法の詳細については、[こちら](xplat-cli-azure-manage-vm-asm-arm.md)を参照してください。

**新しい Azure リソース マネージャー API で作成した Virtual Network を Express Route 回線に接続することはできますか。**

現時点ではサポートされません。新しい Azure リソース マネージャー API で作成した Virtual Network を Express Route 回線と接続することはできません。これは、後でサポートされます。

**Azure リソース マネージャーのテンプレートの例はどこで入手できますか。**

広範囲にわたるスターター テンプレートを「[Azure リソース マネージャー クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates/)」で見つけることができます。

<!---HONumber=AcomDC_0128_2016-->