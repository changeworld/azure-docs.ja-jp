---
title: VNET での Azure Spring Cloud の実行に関するお客様の責任
description: この記事では、VNET での Azure Spring Cloud の実行に関するお客様の責任について説明します。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: devx-track-java
ms.openlocfilehash: 3c21d89329db89b73a0e37046062eae876084e2b
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399770"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>VNET での Azure Spring Cloud の実行に関するお客様の責任

この記事には、仮想ネットワークで Azure Spring Cloud を使用するための仕様が含まれています。

仮想ネットワークにデプロイする場合、Azure Spring Cloud には、仮想ネットワークの外部にあるサービスへの送信依存関係があります。 管理と運用上の目的から、Azure Spring Cloud は特定のポートと完全修飾ドメイン名 (FQDN) にアクセスする必要があります。 Azure Spring Cloud では、管理プレーンとの通信、およびコア Kubernetes クラスター コンポーネントとセキュリティ更新プログラムのダウンロードとインストールに、これらのエンドポイントが必要です。

既定で、Azure Spring Cloud は、送信 (エグレス) インターネット アクセスが無制限です。 このレベルのネットワーク アクセスでは、実行しているアプリケーションから必要に応じて外部リソースにアクセスできます。 エグレス トラフィックを制限する場合は、メンテナンス タスクに対して、アクセスできるポートとアドレスの数を制限する必要があります。 送信アドレスをセキュリティで保護する最も簡単なソリューションは、ドメイン名に基づいて送信トラフィックを制御できるファイアウォール デバイスを使用することです。 たとえば、Azure Firewall では、送信先の FQDN に基づいて HTTP と HTTPS の送信トラフィックを制限できます。 また、適切なファイアウォール規則とセキュリティ規則を構成し、これらの必要なポートとアドレスを許可することができます。

## <a name="azure-spring-cloud-resource-requirements"></a>Azure Spring Cloud のリソース要件

以下の一覧は、Azure Spring Cloud サービスのリソース要件をまとめたものです。 一般的な要件として、Azure Spring Cloud によって作成されたリソース グループおよび基になるネットワーク リソースを変更することはできません。

- Azure Spring Cloud によって作成および所有されているリソース グループは変更しないでください。
  - 既定では、これらのリソース グループには `ap-svc-rt_[SERVICE-INSTANCE-NAME]_[REGION]*` と `ap_[SERVICE-INSTANCE-NAME]_[REGION]*` という名前が付けられています。
  - Azure Spring Cloud で、これらのリソース グループ内のリソースの更新をブロックしないでください。
- Azure Spring Cloud で使用されるサブネットは変更しないでください。
- 同じサブネット内に複数の Azure Spring Cloud サービス インスタンスを作成しないでください。
- ファイアウォールを使用してトラフィックを制御する場合は、サービス インスタンスを運用、保守、およびサポートする Azure Spring Cloud コンポーネントへの次のエグレス トラフィックをブロックしないでください。

## <a name="azure-spring-cloud-network-requirements"></a>Azure Spring Cloud のネットワーク要件

| 送信先エンドポイント                                         | Port             | 用途                                       | 注意                                                         |
| ------------------------------------------------------------ | ---------------- | ----------------------------------------- | ------------------------------------------------------------ |
| \*:1194 "*または*" [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:1194 | UDP:1194         | 基になる Kubernetes クラスターの管理。 |                                                              |
| \*:443 "*または*" [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:443 | TCP:443          | Azure Spring Cloud サービスの管理。    | サービス インスタンス "requiredTraffics" の情報は、リソース ペイロードの "networkProfile" セクションで確認できます。 |
| \*:9000 "*または*" [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:9000 | TCP:9000         | 基になる Kubernetes クラスターの管理。 |                                                              |
| \*:123 "*または*" ntp.ubuntu.com:123                                | UDP: 123          | Linux ノードでの NTP 時刻の同期。  |                                                              |
| \*.azure.io:443 "*または*" [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureContainerRegistry:443 | TCP:443          | Azure Container Registry。                 | [仮想ネットワークのサービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md) *Azure Container Registry* を有効にすることで置き換えることができます。 |
| \*.core.windows.net:443 および \*.core.windows.net:445 "*または*" [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Storage:443 および Storage:445 | TCP:443、TCP:445 | Azure Files                        | [仮想ネットワークのサービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md) *Azure Storage* を有効にすることで置き換えることができます。 |
| \*.servicebus.windows.net:443 "*または*" [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - EventHub:443 | TCP:443          | Azure Event Hub。                          | [仮想ネットワークのサービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md) *Azure Event Hubs* を有効にすることで置き換えることができます。 |

## <a name="azure-spring-cloud-fqdn-requirementsapplication-rules"></a>Azure Spring Cloud の FQDN 要件またはアプリケーション ルール

Azure Firewall には、次の構成を簡略化するために FQDN タグ **AzureKubernetesService** が用意されています。

| 送信先 FQDN                  | Port      | 用途                                                          |
| --------------------------------- | --------- | ------------------------------------------------------------ |
| <i>*.azmk8s.io</i>                | HTTPS: 443 | 基になる Kubernetes クラスターの管理。                    |
| <i>mcr.microsoft.com</i>          | HTTPS: 443 | Microsoft Container Registry (MCR)。                          |
| <i>*.cdn.mscr.io</i>              | HTTPS: 443 | Azure CDN によってサポートされる MCR ストレージ。                         |
| <i>*.data.mcr.microsoft.com</i>   | HTTPS: 443 | Azure CDN によってサポートされる MCR ストレージ。                         |
| <i>management.azure.com</i>       | HTTPS: 443 | 基になる Kubernetes クラスターの管理。                    |
| <i>*login.microsoftonline.com</i> | HTTPS: 443 | Azure Active Directory 認証。                       |
| <i>*login.microsoft.com</i>       | HTTPS: 443 | Azure Active Directory 認証。                       |
| <i>packages.microsoft.com</i>     | HTTPS: 443 | Microsoft パッケージ リポジトリ。                               |
| <i>acs-mirror.azureedge.net</i>   | HTTPS: 443 | kubenet や Azure CNI などの必要なバイナリをインストールするために必要なリポジトリ。 |
| *mscrl.microsoft.com*<sup>1</sup> | HTTPS:80  | 必要な Microsoft 証明書チェーン パス。                  |
| *crl.microsoft.com*<sup>1</sup>   | HTTPS:80  | 必要な Microsoft 証明書チェーン パス。                  |
| *crl3.digicert.com*<sup>1</sup>   | HTTPS:80  | サードパーティの TLS/SSL 証明書チェーン パス。                 |

<sup>1</sup> これらの FQDN は FQDN タグに含まれていないことに注意してください。


## <a name="azure-spring-cloud-optional-fqdn-for-third-party-application-performance-management"></a>サードパーティ製アプリケーションのパフォーマンス管理に使用される Azure Spring Cloud の省略可能な FQDN

Azure Firewall には、次の構成を簡略化するために FQDN タグ **AzureKubernetesService** が用意されています。

| 送信先 FQDN                   | Port       | 用途                                                          |
| ---------------------------------- | ---------- | ------------------------------------------------------------ |
| <i>collector*.newrelic.com</i>     | TCP:443/80 | 米国リージョンの New Relic APM エージェントの必須ネットワーク。[APM エージェント ネットワーク](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents)に関する記事も参照してください。 |
| <i>collector*.eu01.nr-data.net</i> | TCP:443/80 | EU リージョンの New Relic APM エージェントの必須ネットワーク。[APM エージェント ネットワーク](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents)に関する記事も参照してください。 |
| <i>*.live.dynatrace.com</i>        | TCP:443    | Dynatrace APM エージェントの必須ネットワーク。                    |
| <i>*.live.ruxit.com</i>            | TCP:443    | Dynatrace APM エージェントの必須ネットワーク。                    |
| <i>*.saas.appdynamics.com</i>      | TCP:443/80 | AppDynamics APM エージェントのネットワークが必要です。詳細については、[SaaS ドメインおよび IP 範囲](https://docs.appdynamics.com/display/PAA/SaaS+Domains+and+IP+Ranges)に関するページも参照してください。 |

## <a name="see-also"></a>関連項目

* [プライベート ネットワークのアプリケーションにアクセスする](access-app-virtual-network.md)
* [Application Gateway を使用して、アプリケーションをインターネットに公開する](expose-apps-gateway.md)
