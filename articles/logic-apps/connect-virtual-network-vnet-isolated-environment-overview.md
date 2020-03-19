---
title: Azure Virtual Network にアクセスする
description: 統合サービス環境 (ISE) を利用して、ロジック アプリから Azure の仮想ネットワーク (VNET) にアクセスする方法の概要
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127253"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>統合サービス環境 (ISE) を使用して、Azure Logic Apps から Azure Virtual Network リソースにアクセスする

ロジック アプリで、[Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)内の仮想マシン (VM) や他のシステムまたはサービスなど、セキュリティで保護されたリソースにアクセスすることが必要になる場合があります。 このアクセスを設定するために、["*統合サービス環境*" (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) を作成することができます。 ISE は、専用のリソースを使用し、"グローバル" なマルチテナント Logic Apps サービスとは別に実行される分離された Logic Apps サービスのインスタンスです。

自分専用の分離されたインスタンスでロジック アプリを実行することで、他の Azure テナントが自分のアプリのパフォーマンスに与える可能性がある影響 (["うるさい隣人" エフェクト](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)とも呼ばれる) を減らすことができます。 ISE には、次の利点があります。

* 自分専用の静的 IP アドレス。これらの IP アドレスは、マルチテナント サービスのロジック アプリによって共有される静的 IP アドレスとは区別されています。 また、送信先システムとの通信のために、単一の予測可能な静的パブリック アウトバウンド IP アドレスを自分で設定することもできます。 このようにすると、それらの送信先システムで ISE ごとに追加のファイアウォールを設定する必要はありません。

* 実行継続時間、ストレージのリテンション期間、スループット、HTTP の要求と応答のタイムアウト、メッセージのサイズ、カスタム コネクタの要求の上限が引き上げられました。 詳細については、[Azure Logic Apps の制限と構成](logic-apps-limits-and-config.md)に関するページを参照してください。

> [!NOTE]
> 一部の Azure 仮想ネットワークでは、プライベート エンドポイント ([Azure Private Link](../private-link/private-link-overview.md)) を使用して、Azure でホストされている Azure Storage、Azure Cosmos DB、Azure SQL Database、パートナー サービス、カスタマー サービスなどの Azure PaaS サービスへのアクセスが提供されます。 ロジック アプリでプライベート エンドポイントを使用する仮想ネットワークにアクセスする必要がある場合は、ISE の内部でそれらのロジック アプリを作成、デプロイ、実行する必要があります。

ISE を作成すると、Azure によってその ISE が Azure 仮想ネットワークに "*挿入*"(デプロイ) されます。 アクセスを必要とする統合アカウントやロジック アプリの場所としてその ISE を使用することができます。

![統合サービス環境を選択する](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

ロジック アプリと同じ ISE 内で実行される以下の項目を使用して、仮想ネットワーク内のリソース、または仮想ネットワークに接続されているリソースにロジック アプリからアクセスすることができます。

* **CORE** というラベルが表示された組み込みトリガーまたは組み込みアクション (例: HTTP トリガーまたは HTTP アクション)
* そのシステムまたはサービスの **ISE** とラベル付けされたコネクタ
* カスタム コネクタ

**CORE** や **ISE** のラベルを持たないコネクタを ISE 内のロジック アプリで使用することもできます。 ただし、それらのコネクタは、マルチテナント Logic Apps サービスで実行されます。 詳細については、次のセクションを参照してください。

* [分離とマルチテナント](#difference)
* [統合サービス環境から接続する](../connectors/apis-list.md#integration-service-environment)
* [ISE コネクタ](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> ISE 内で実行されるロジック アプリ、組み込みトリガー、組み込みアクション、およびコネクターでは、使用量ベースの価格プランとは異なる価格プランが使用されます。 詳細については、[Logic Apps の価格モデル](../logic-apps/logic-apps-pricing.md#fixed-pricing)を参照してください。 価格の詳細については、「[Logic Apps の価格](../logic-apps/logic-apps-pricing.md)」を参照してください。

この概要では、ISE によってロジック アプリに Azure 仮想ネットワークへの直接アクセスが提供される方法を詳しく説明し、ISE とマルチテナント Logic Apps サービスとの違いを比較します。

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>分離とマルチテナント

ISE にロジック アプリを作成して実行すると、マルチテナント Logic Apps サービスと同じユーザー エクスペリエンスおよび同様の機能が得られます。 マルチテナント Logic Apps サービスで使用できるのと同じすべての組み込みトリガー、アクション、マネージド コネクタを使用できます。 一部のマネージド コネクタでは、追加の ISE バージョンが提供されます。 ISE コネクタと非 ISE コネクタの違いは、ISE 内で作業するときにロジック アプリ デザイナーに付けられるラベルと実行される場所です。

![ISE 内のラベルが付いているコネクタと付いていないコネクタ](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* 組み込みのトリガーとアクションには、**CORE** ラベルが表示されます。 これらは常に、ご利用のロジック アプリと同じ ISE で実行されます。 **ISE** というラベルが表示されるマネージド コネクタも、ロジック アプリと同じ ISE で実行されます。

  たとえば、ISE のバージョンを提供するコネクタをいくつか次に示します。

  * Azure Blob Storage、Azure File Storage、Azure Table Storage
  * Azure キュー、Azure Service Bus、Azure Event Hubs、IBM MQ
  * FTP、SFTP-SSH
  * SQL Server、Azure SQL Data Warehouse、Azure Cosmos DB
  * AS2、X12、EDIFACT

* 追加のラベルが表示されないマネージド コネクタは、常にマルチテナント Logic Apps サービスで実行されますが、ISE でホストされたロジック アプリでこれらのコネクタを使用することもできます。

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>オンプレミス システムへのアクセス

Azure 仮想ネットワークに接続されたオンプレミスのシステムやデータ ソースにアクセスするために、ISE 内のロジック アプリは、次の項目を使用できます。

* HTTP アクション

* そのシステムの ISE とラベル付けされたコネクタ

  > [!NOTE]
  > [統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) で SQL Server コネクタで Windows 認証を使用するには、[オンプレミス データ ゲートウェイ](../logic-apps/logic-apps-gateway-install.md)でコネクタの ISE 以外のバージョンを使用します。 ISE とラベル付けされたバージョンでは、Windows 認証はサポートされていません。

* カスタム コネクタ

  * オンプレミス データ ゲートウェイを必要とするカスタム コネクタを ISE の外部で作成した場合は、ISE 内のロジック アプリでもそれらのコネクタを使用できます。

  * ISE 内で作成されたカスタム コネクタは、オンプレミス データ ゲートウェイでは動作しません。 ただし、これらのコネクタでは、ISE をホストしている仮想ネットワークに接続されているオンプレミス データ ソースに直接アクセスできます。 そのため、ISE 内のロジック アプリでは、ほとんどの場合、それらのリソースと通信するときにデータ ゲートウェイは不要です。

仮想ネットワークに接続されていない、または ISE とラベル付けされたコネクタがないオンプレミス システムでは、お使いのロジック アプリからこれらのシステムに接続する前に、まず[オンプレミスのデータ ゲートウェイを設定する](../logic-apps/logic-apps-gateway-install.md)必要があります。

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU

ISE を作成するときは、Developer SKU または Premium SKU を選択できます。 これらの SKU の違いを以下に示します。

* **開発者**

  実験、開発、テストには使用できますが、運用環境とパフォーマンス テストには使用できない、低コストの ISE が提供されます。 Developer SKU には、組み込みのトリガーとアクション、標準コネクタ、エンタープライズ コネクタ、および固定月額料金用の 1 つの [Free レベル](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)統合アカウントが含まれています。 ただし、この SKU には、サービス レベル アグリーメント (SLA)、容量のスケールアップ オプション、またはリサイクル中の冗長性は含まれず、これは遅延やダウンタイムが発生する可能性があることを意味します。

* **Premium**

  運用環境で使用できる ISE が提供され、SLA のサポート、組み込みのトリガーとアクション、標準コネクタ、エンタープライズ コネクタ、単一の [Standard レベル](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)の統合アカウント、容量スケールアップ オプション、固定月額料金のリサイクル中の冗長性などが含まれます。

> [!IMPORTANT]
> この SKU オプションは、ISE 作成時にのみ使用できます。後で変更することはできません。

価格については、[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)に関する記事を参照してください。 ISE の価格と課金のしくみについては、「[固定価格モデル](../logic-apps/logic-apps-pricing.md#fixed-pricing)」を参照してください。

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE エンドポイントへのアクセス

ISE を作成するときに、内部アクセス エンドポイントと外部アクセス エンドポイントのどちらを使用するかを選択できます。 この選択により、ISE 内のロジック アプリ上で要求または Webhook トリガーが仮想ネットワークの外からの呼び出しを受信できるかどうかが決まります。

これらのエンドポイントは、ロジック アプリの実行履歴の入力と出力にアクセスする方法にも影響します。

* **内部**:実行履歴で、"*仮想ネットワーク内からのみ*"、ロジック アプリの入力と出力を表示およびアクセスできる ISE のロジック アプリの呼び出しを許可するプライベート エンドポイント

* **外部**:実行履歴で、"*仮想ネットワーク外から*"、ロジック アプリの入力と出力を表示およびアクセスできる ISE のロジック アプリの呼び出しを許可するパブリック エンドポイント ネットワーク セキュリティ グループ (NSG) を使用する場合は、実行履歴の入力と出力へのアクセスを許可する受信規則が設定されていることを確認します。 詳細については、「[ISE のアクセスを有効にする](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)」を参照してください。

> [!IMPORTANT]
> このアクセス エンドポイント オプションは、ISE 作成時にのみ使用できます。後で変更することはできません。

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>ISE での統合アカウント

統合サービス環境 (ISE) の内部のロジック アプリで統合アカウントを使用できます。 ただし、これらの統合アカウントでは、リンクされているロジック アプリと "*同じ ISE*" を使用する必要があります。 ISE のロジック アプリは、同じ ISE にある統合アカウントのみを参照できます。 統合アカウントを作成すると、ご利用の統合アカウントの場所として、自分の ISE を選択できます。 統合アカウントと ISE に対する価格と課金のしくみについては、「[固定価格モデル](../logic-apps/logic-apps-pricing.md#fixed-pricing)」を参照してください。 価格については、[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps から Azure Virtual Network に接続する](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) の詳細を理解する
* [Azure サービスの仮想ネットワーク統合](../virtual-network/virtual-network-for-azure-services.md)について理解する
