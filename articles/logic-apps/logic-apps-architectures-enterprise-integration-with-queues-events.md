---
title: エンタープライズ統合アーキテクチャのパターン - Azure Integration Services
description: このアーキテクチャ リファレンスでは、Azure Logic Apps、Azure API Management、Azure Service Bus、および Azure Event Grid を使用してエンタープライズ統合パターンを実装する方法を示します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 5b58f4b71d8d9f3ca91d8cefc6215073fd836765
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093668"
---
# <a name="enterprise-integration-architecture-with-queues-and-events"></a>キューとイベントによるエンタープライズ統合アーキテクチャ

この記事では、Azure Integration Services を使用する際に統合アプリケーションに適用できる実証済みプラクティスを使用するエンタープライズ統合アーキテクチャについて説明します。 このアーキテクチャは、HTTP API、ワークフロー、およびオーケストレーションを必要とする、多数のさまざまなアプリケーション パターンの基準として使用できます。

![アーキテクチャの図 - キューとイベントによるエンタープライズ統合](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

このシリーズは、汎用的な統合アプリケーションのビルドに適用できる再利用可能なコンポーネント パーツについて説明しています。 統合テクノロジには単純なポイントツーポイント アプリから完全なエンタープライズ Azure Service Bus アプリに至る多くの用途があるため、アプリやインフラストラクチャにどのコンポーネントを実装する必要があるかを検討してください。

## <a name="architecture-components"></a>アーキテクチャ コンポーネント

このアーキテクチャは、[アーキテクチャ リファレンスのシンプルなエンタープライズ統合](/azure/architecture/reference-architectures/enterprise-integration/simple-enterprise-integration)に関する記事で説明されているアーキテクチャをベースにしています。 このアーキテクチャの[推奨事項](/azure/architecture/reference-architectures/enterprise-integration/simple-enterprise-integration#recommendations)もこの記事に当てはまりますが、ここでは、簡潔にするために、「[推奨事項](#recommendations)」セクションの推奨事項を省略しています。 このエンタープライズ統合アーキテクチャには、次のコンポーネントが含まれます。

- **リソース グループ**: [リソース グループ](../azure-resource-manager/resource-group-overview.md)は、Azure リソースの論理コンテナーです。

- **Azure API Management**: [API Management](https://docs.microsoft.com/azure/api-management/) サービスは、HTTP API を公開、セキュリティ保護、および変換するためのフル マネージド プラットフォームです。

- **Azure API Management 開発者ポータル**: Azure API Management の各インスタンスは、[開発者ポータル](../api-management/api-management-customize-styles.md)へのアクセスを提供します。 このポータルでは、ドキュメントとコード サンプルにアクセスできます。 開発者ポータル内で API をテストすることもできます。

- **Azure Logic Apps**: [Logic Apps](../logic-apps/logic-apps-overview.md) は、エンタープライズ ワークフローと統合を構築するためのサーバーレス プラットフォームです。

- **コネクタ**: Logic Apps では[コネクタ](../connectors/apis-list.md)を使用して、よく利用するサービスに接続します。 Logic Apps には何百ものコネクタが用意されていますが、カスタム コネクタを作成することもできます。

- **Azure Service Bus**: [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) は、セキュリティで保護された信頼性の高いメッセージングを提供します。 メッセージングを使用してアプリケーションを切り離したり、他のメッセージベース システムと統合したりすることができます。

- **Azure Event Grid**: [Event Grid](../event-grid/overview.md) は、アプリケーション イベントを公開して配信するためのサーバーレス プラットフォームです。

- **IP アドレス**: Azure API Management サービスには、固定のパブリック [IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md)とドメイン名があります。 既定のドメイン名は azure-api.net のサブドメイン (例: contoso.azure-api.net) ですが、[カスタム ドメイン](../api-management/configure-custom-domain.md)も構成できます。 Logic Apps と Service Bus にはパブリック IP アドレスもあります。 ただし、このアーキテクチャでは、セキュリティのために、Logic Apps エンドポイントを呼び出すためのアクセスを API Management の IP アドレスのみに制限しています。 Service Bus の呼び出しは、共有アクセス署名 (SAS) によってセキュリティで保護されています。

- **Azure DNS**: [Azure DNS](https://docs.microsoft.com/azure/dns/) は、DNS ドメインのホスティング サービスです。 Azure DNS は、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスに使用しているのと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。 カスタム ドメイン名 (contoso.com など) を使用するには、カスタム ドメイン名を IP アドレスにマップする DNS レコードを作成します。 詳細については、[API Management でのカスタム ドメイン名の構成](../api-management/configure-custom-domain.md)に関するページを参照してください。

- **Azure Active Directory (Azure AD)**: [Azure AD](https://docs.microsoft.com/azure/active-directory/) または他の認証 ID プロバイダーを認証に使用できます。 Azure AD は、[API Management 用の JSON Web トークン](../api-management/policies/authorize-request-based-on-jwt-claims.md)を渡して検証することで、API エンドポイントにアクセスするための認証を提供します。 Standard および Premium レベルの場合、Azure AD によって API Management 開発者ポータルへのアクセスをセキュリティで保護することができます。

## <a name="patterns"></a>パターン 

このアーキテクチャでは、操作の基本となるいくつかのパターンを使用します。

* 既存のバックエンド HTTP API は、API Management 開発者ポータル経由で公開されます。 ポータルでは、開発者 (組織内、組織外、またはその両方) は、  
これらの API への呼び出しをアプリケーションに統合することができます。

* 複合 API は、サービスとしてのソフトウェア (SaaS) システム、Azure サービス、および API Management に公開された任意の API の呼び出しを調整するロジック アプリを使用してビルドされます。 また、ロジック アプリは、[API Management 開発者ポータル経由](../api-management/import-logic-app-as-api.md)で公開されます。

* アプリケーションでは、Azure AD を使用して、API へのアクセスを獲得するために必要な [OAuth 2.0 セキュリティ トークンを取得](../api-management/api-management-howto-protect-backend-with-aad.md)します。

* Azure API Management は[セキュリティ トークンを検証](../api-management/api-management-howto-protect-backend-with-aad.md)してから、バックエンドの API またはロジック アプリに要求を渡します。

* Azure Service Bus キューは、アプリケーション アクティビティを[切り離す](../service-bus-messaging/service-bus-messaging-overview.md)ためと、[負荷のスパイクを平滑化](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling)するために使用されます。 メッセージは、ロジック アプリ、サード パーティ製アプリによってキューに追加されるか、または (表示されていない) API Management 経由で HTTP API としてキューを公開することでキューに追加されます。

* メッセージが Service Bus キューに追加されると、イベントが発生します。 イベントによってロジック アプリがトリガーされ、ロジック アプリによってメッセージが処理されます。

* 他の Azure サービス (Azure Blob Storage、Azure Event Hubs など) も、イベントを Event Grid に公開します。 これらのサービスは、ロジック アプリをトリガーしてイベントを受信してから、後続のアクションを実行します。

## <a name="recommendations"></a>Recommendations

この記事で説明されている一般的なアーキテクチャとは異なる、特定の要件が生じる場合があります。 このセクションに記載されている推奨事項は原案として使用してください。

### <a name="service-bus-tier"></a>Service Bus のレベル

Event Grid の通知をサポートする Service Bus Premium レベルを使用します。 詳細については、「[Service Bus の価格](https://azure.microsoft.com/pricing/details/service-bus/)」をご覧ください。

### <a name="event-grid-pricing"></a>Event Grid の価格

Event Grid では、サーバーレス モデルを使用します。 課金は、操作 (イベントの実行) の回数に基づいて計算されます。 詳細については、「[Event Grid の価格](https://azure.microsoft.com/pricing/details/event-grid/)」を参照してください。 現在、Event Grid には、レベルに関する考慮事項はありません。

### <a name="use-peeklock-to-consume-service-bus-messages"></a>PeekLock を使用して Service Bus メッセージを使用する

Service Bus メッセージを使用するためにロジック アプリを作成する場合、[PeekLock](../service-bus-messaging/service-bus-messaging-overview.md#queues) を使用してメッセージのグループにアクセスするようにロジック アプリを設定します。 PeekLock を使用すると、ロジック アプリは、メッセージを完了または中止する前に、各メッセージを検証する手順を実行できます。 この方法によって、メッセージの誤損失から保護します。

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Event Grid トリガーが発生した場合に複数のオブジェクトをチェックする

Event Grid トリガーが発生した場合、このアクションは、単に "これらの少なくとも 1 つが発生した" ことを意味します。 たとえば、Event Grid が Service Bus キューに表示されたメッセージ上でロジック アプリをトリガーした場合、ロジック アプリでは常に、1 つ以上のメッセージを処理に利用できることを前提としています。

### <a name="region"></a>リージョン

ネットワーク待機時間を最小限に抑えるには、API Management、Logic Apps、および Service Bus に対して同じリージョンを選択します。 通常は、ユーザーに最も近いリージョンを選択します。

リソース グループにもリージョンがあります。 このリージョンによって、デプロイ メタデータを格納する場所と、デプロイ テンプレートを実行する場所が指定されます。 デプロイ時の可用性を向上させるには、リソース グループとそのリソースを同じリージョンに配置します。

## <a name="scalability"></a>スケーラビリティ

Service Bus Premium レベルは、より高いスケーラビリティを実現するためにメッセージング ユニットの数をスケールアウトできます。 Premium レベルの構成では、1 つ、2 つ、または 4 つのメッセージング ユニットを持つことができます。 Service Bus のスケーリングの詳細については、「[Service Bus メッセージングを使用したパフォーマンス向上のためのベスト プラクティス](../service-bus-messaging/service-bus-performance-improvements.md)」を参照してください。

## <a name="availability"></a>可用性

* Basic、Standard、および Premium レベルの場合、Azure API Management のサービス レベル アグリーメント (SLA) は現在 99.9% です。 2 つ以上のリージョンに少なくとも 1 つのユニットをデプロイする Premium レベルの構成では、SLA が 99.95% になります。

* Azure Logic Apps の SLA は、現在 99.9% です。

### <a name="disaster-recovery"></a>障害復旧

重大な障害が発生した場合にフェールオーバーを有効にするために、Service Bus Premium で geo ディザスター リカバリーを実装することを検討します。 詳細については、「[Azure Service Bus の geo ディザスター リカバリー](../service-bus-messaging/service-bus-geo-dr.md)」を参照してください。

## <a name="manageability"></a>管理容易性

運用、開発、およびテスト環境それぞれに対して個別のリソース グループを作成してください。 個別のリソース グループにより、デプロイの管理、テスト デプロイの削除、およびアクセス権の割り当てが行いやすくなります。

リソースをリソース グループに割り当てるときは、次の要素を検討してください。

* **ライフサイクル**: 一般的に、同じライフサイクルのリソースは、同じリソース グループに配置します。

* **アクセス**: アクセス ポリシーをグループ内のリソースに適用するには、[ロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) を使用できます。

* **課金**: リソース グループのロールアップ コストを表示できます。

* **API Management の価格レベル**: 開発環境およびテスト環境には、Developer レベルを使用します。 運用前環境のコストを最小限に抑えるには、運用環境のレプリカをデプロイしてテストを実行し、シャットダウンします。

詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。

## <a name="deployment"></a>Deployment

* API Management、Logic Apps、Event Grid、および Service Bus をデプロイするには、[Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md)を使用します。 テンプレートにより、PowerShell または Azure CLI を使用して簡単にデプロイを自動化できます。

* API Management、個々の任意のロジック アプリ、Event Grid トピック、および独自の Service Bus 名前空間は、別々の Resource Manager テンプレートに配置します。 別々のテンプレートを使用することで、ソース管理システムにリソースを格納できます。 これらのテンプレートは、継続的インテグレーション/継続的なデプロイ (CI/CD) プロセスの一環として、同時または個別にデプロイできます。

## <a name="diagnostics-and-monitoring"></a>診断および監視

API Management や Logic Apps のように、Azure Monitor を使用して Service Bus を監視できます。この機能は既定で有効になっています。 Azure Monitor は、各サービスに構成されているメトリックに基づいて情報を提供します。 

## <a name="security"></a>セキュリティ

Service Bus をセキュリティで保護するには、Shared Access Signature (SAS) を使用します。 たとえば、[SAS 認証](../service-bus-messaging/service-bus-sas.md)を使用して、ユーザーに特定の権限で Service Bus リソースへのアクセス権を付与できます。 詳細については、「[Service Bus の認証と承認](../service-bus-messaging/service-bus-authentication-and-authorization.md)」を参照してください。

Service Bus キューを HTTP エンドポイントとして公開する必要がある場合は (たとえば、新しいメッセージを投稿する場合)、API Management を使用して、エンドポイントをフロントに配置してキューをセキュリティで保護します。 次に、必要に応じて、証明書または OAuth 認証を使用してこのエンドポイントをセキュリティ保護できます。 エンドポイントをセキュリティで保護する最も簡単な方法は、HTTP 要求/応答トリガーを仲介としてロジック アプリを使用することです。

Event Grid サービスは、検証コードを使ってイベント配信をセキュリティで保護します。 Logic Apps を利用してイベントを使用する場合、検証は自動的に行われます。 詳細については、「[Event Grid security and authentication](../event-grid/security-authentication.md)」(Event Grid のセキュリティと認証) を参照してください。

## <a name="next-steps"></a>次の手順

* [単純なエンタープライズ統合](/azure/architecture/reference-architectures/enterprise-integration/simple-enterprise-integration)について学習します
