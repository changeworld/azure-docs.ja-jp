---
title: Azure Integration Services エンタープライズ統合参照アーキテクチャ
description: Logic Apps、API Management、Service Bus、および Event Grid を使ってエンタープライズ統合パターンを実装する方法を示す参照アーキテクチャについて説明します。
author: mattfarm
manager: jonfan
editor: ''
services: logic-apps api-management
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/15/2018
ms.author: LADocs; estfan
ms.openlocfilehash: a86c4c4227795a712dd51ace1fbefe9d2b96518a
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116114"
---
# <a name="reference-architecture-enterprise-integration-with-queues-and-events"></a>参照アーキテクチャ: キューとイベントによるエンタープライズ統合

次の参照アーキテクチャでは、Azure Integration Services を使用する統合アプリケーションに適用できる一連の実証済みプラクティスを示します。 このアーキテクチャは、HTTP API、ワークフロー、およびオーケストレーションを必要とする、多数のさまざまなアプリケーション パターンの基準として使用できます。

![アーキテクチャの図 - キューとイベントによるエンタープライズ統合](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

*統合テクノロジには多くの選択可能なアプリケーションがあります。その範囲は、単純なポイント ツー ポイント アプリケーションから完全なエンタープライズ Azure Service Bus アプリケーションまで多岐にわたります。このアーキテクチャ シリーズは、汎用的な統合アプリケーションのビルドに適用できる再利用可能なコンポーネント パーツについて説明しています。アーキテクトは、アプリケーションとインフラストラクチャの実装に必要なコンポーネントを検討する必要があります。*

## <a name="architecture"></a>アーキテクチャ

このアーキテクチャは、[単純なエンタープライズ統合](logic-apps-architectures-simple-enterprise-integration.md)上に*ビルド*されています。 [単純なエンタープライズ アーキテクチャに関する推奨事項](logic-apps-architectures-simple-enterprise-integration.md#recommendations)がここに適用されます。 簡潔にするため、この記事の[推奨事項](#recommendations)ではこれらは省略されています。 

アーキテクチャには次のコンポーネントがあります。

- **リソース グループ**。 [リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)は、Azure リソースの論理コンテナーです。
- **Azure API Management**。 [API Management](https://docs.microsoft.com/azure/api-management/) は、HTTP API の公開、セキュリティ保護、および変換に使用されるフル マネージド プラットフォームです。
- **Azure API Management 開発者ポータル**。 Azure API Management の各インスタンスには、[開発者ポータル](https://docs.microsoft.com/azure/api-management/api-management-customize-styles)へのアクセスが付属しています。 API Management 開発者ポータルでは、ドキュメントとコード サンプルにアクセスできます。 開発者ポータル内で API をテストすることができます。
- **Azure Logic Apps**。 [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) は、エンタープライズ ワークフローと統合をビルドするために使用される、サーバーレス プラットフォームです。
- **コネクタ**。 Logic Apps では、[コネクタ](https://docs.microsoft.com/azure/connectors/apis-list)を使用してよく利用するサービスに接続します。 Logic Apps には既に多数のさまざまなコネクタがありますが、カスタム コネクタを作成することもできます。
- **Azure Service Bus**。 [Service Bus](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview) は、セキュリティで保護された信頼性の高いメッセージングを提供します。 メッセージングを使用してアプリケーションを切り離したり、他のメッセージベース システムと統合することができます。
- **Azure Event Grid**。 [Event Grid](https://docs.microsoft.com/azure/event-grid/overview) は、アプリケーション イベントの公開に使用されるサーバーレス プラットフォームです。
- **IP アドレス**。 Azure API Management サービスには、固定のパブリック [IP アドレス](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)とドメイン名があります。 ドメイン名は、contoso.azure-api.net などの azure-api.net のサブドメインです。 Logic Apps と Service Bus にはパブリック IP アドレスもあります。 ただし、このアーキテクチャでは、Logic Apps エンドポイントを呼び出すためのアクセスを API Management の IP アドレスのみに制限しています (セキュリティのため)。 Service Bus の呼び出しは、共有アクセス署名 (SAS) によってセキュリティで保護されています。
- **Azure DNS**。 [Azure DNS](https://docs.microsoft.com/azure/dns/) は DNS ドメインのホスティング サービスです。 Azure DNS は、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスに使用しているのと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。 カスタム ドメイン名 (contoso.com など) を使用するには、カスタム ドメイン名を IP アドレスにマップする DNS レコードを作成します。 詳細については、[API Management でのカスタム ドメイン名の構成](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain)に関するページを参照してください。
- **Azure Active Directory (Azure AD)**。 認証には、[Azure AD](https://docs.microsoft.com/azure/active-directory/) または他の ID プロバイダーを使用します。 Azure AD は、[API Management 用の JSON Web トークン](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims)を渡して検証することで、API エンドポイントにアクセスするための認証を提供します。 Azure AD では、API Management 開発者ポータルへのアクセスをセキュリティで保護することができます (Standard および Premium レベル限定)。

このアーキテクチャには、操作するためのいくつかの基本パターンがあります。

* 既存のバックエンド HTTP API は、API Management 開発者ポータル経由で公開されます。 ポータルでは、開発者 (組織名、組織外、その両方のいずれか) はこれらの API への呼び出しをアプリケーションに統合することができます。
* 複合 API は、ロジック アプリを使用して、およびサービスとしてのソフトウェア (SaaS) システム、Azure サービス、および API Management に公開された任意の API の呼び出しを調整することでビルドされます。 [ロジック アプリは API Management 開発者ポータル経由でも公開されます](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api)。
- アプリケーションでは、Azure AD を使用して API へのアクセスを獲得するために必要な [OAuth 2.0 セキュリティ トークン](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)を取得します。
- API Management は[セキュリティ トークンを検証](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)してから、バックエンドの API またはロジック アプリに要求を渡します。
- Service Bus キューは、アプリケーションの動作の[切り離し](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview)と、[負荷のスパイクを軽減](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling)するために使用されます。 メッセージは、ロジック アプリ、サード パーティ製アプリケーションによってキューに追加されるか、または (図には示されていない) API Management 経由で HTTP API としてキューを公開することでキューに追加されます。
- メッセージが Service Bus キューに追加されると、イベントが発生します。 ロジック アプリは、イベントによってトリガーされます。 メッセージはその後、ロジック アプリによって処理されます。
- 他の Azure サービス (Azure Blob Storage、Azure Event Hubs など) も、イベントを Event Grid に公開します。 これらのサービスは、ロジック アプリをトリガーしてイベントを受信してから、後続のアクションを実行します。

## <a name="recommendations"></a>Recommendations

この記事で説明されている一般的なアーキテクチャとは異なる特定の要件がある場合があります。 このセクションに記載されている推奨事項は原案として使用してください。

### <a name="service-bus-tier"></a>Service Bus のレベル

Service Bus Premium レベルを使用します。 Premier レベルは、Event Grid の通知をサポートしています。 詳細については、「[Service Bus の価格](https://azure.microsoft.com/pricing/details/service-bus/)」をご覧ください。

### <a name="event-grid-pricing"></a>Event Grid の価格

Event Grid では、サーバーレス モデルを使用します。 課金は、操作 (イベントの実行) の回数に基づいて計算されます。 詳細については、「[Event Grid の価格](https://azure.microsoft.com/pricing/details/event-grid/)」を参照してください。 現在、Event Grid には、レベルに関する考慮事項はありません。

### <a name="use-peeklock-to-consume-service-bus-messages"></a>PeekLock を使用して Service Bus メッセージを使用する

Service Bus メッセージを使用するためにロジック アプリを作成した場合、ロジック アプリ内の [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) を使用してメッセージのグループにアクセスします。 PeekLock を使用すると、ロジック アプリは、メッセージを完了または中止する前に、各メッセージを検証する手順を実行できます。 この方法によって、メッセージの誤損失から保護します。

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Event Grid トリガーが発生した場合に複数のオブジェクトをチェックする

Event Grid トリガーが発生した場合は、"これらの少なくとも 1 つが発生した" ことを意味します。 たとえば、Event Grid が Service Bus キューに表示されたメッセージ上でロジック アプリをトリガーした場合、ロジック アプリでは常に、1 つ以上のメッセージを処理に利用できることを前提としています。

### <a name="region"></a>リージョン

API Management、Logic Apps、および Service Bus を同じリージョンにプロビジョニングして、ネットワーク待機時間を最小限に抑えます。 通常は、ユーザーに最も近いリージョンを選択します。

リソース グループにもリージョンがあります。 このリージョンによって、メタデータのデプロイの格納場所と、テンプレートのデプロイの実行元の場所が指定されます。 デプロイ時の可用性を向上させるには、リソース グループとそのリソースを同じリージョンに配置します。

## <a name="scalability"></a>スケーラビリティ

Service Bus Premium レベルは、より高いスケーラビリティを実現するためにメッセージング ユニットの数をスケールアウトできます。 Premium レベルの構成では、1 つ、2 つ、または 4 つのメッセージング ユニットを持つことができます。 Service Bus のスケーリングの詳細については、「[Service Bus メッセージングを使用したパフォーマンス向上のためのベスト プラクティス](../service-bus-messaging/service-bus-performance-improvements.md)」を参照してください。

## <a name="availability"></a>可用性

現在、Azure API Management のサービス レベル アグリーメント (SLA) は、Basic、Standard、および Premium レベルで 99.9% です。 2 つ以上のリージョンに少なくとも 1 つのユニットをデプロイする Premium レベルの構成では、SLA が 99.95% になります。

現在、Azure Logic Apps の SLA は 99.9% です。

### <a name="disaster-recovery"></a>ディザスター リカバリー

重大な障害が発生した場合にフェールオーバーを有効にするために、Service Bus Premium で geo ディザスター リカバリーを実装することを検討します。 詳細については、「[Azure Service Bus の geo ディザスター リカバリー](../service-bus-messaging/service-bus-geo-dr.md)」を参照してください。

## <a name="manageability"></a>管理容易性

運用、開発、およびテスト環境それぞれに対して個別のリソース グループを作成してください。 個別のリソース グループにより、デプロイの管理、テスト デプロイの削除、およびアクセス権の割り当てが行いやすくなります。

リソースをリソース グループに割り当てるときは、次の要素を検討してください。

- **ライフサイクル**。 一般的に、同じライフサイクルのリソースは、同じリソース グループに配置します。
- **アクセス**。 [ロールベースのアクセス制御](../role-based-access-control/overview.md) (RBAC) を使用して、アクセス ポリシーをグループ内のリソースに適用できます。
- **課金**。 リソース グループのロールアップ コストを表示できます。
- **API Management の価格レベル**。 開発環境およびテスト環境には、Developer レベルを使用することをお勧めします。 運用前環境には、本番環境のレプリカをデプロイしてテストを実行し、コストを最小限に抑えることをお勧めします。

詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。

### <a name="deployment"></a>Deployment

[Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md)を使用して、API Management、Logic Apps、Event Grid、および Service Bus をデプロイすることを勧めします。 テンプレートにより、PowerShell または Azure CLI を使用したデプロイが自動化しやすくなります。

API Management、個々の任意のロジック アプリ、Event Grid トピック、および独自の Service Bus 名前空間は、別々の Resource Manager テンプレートに配置することをお勧めします。 別々のテンプレートを使用すると、ソース管理システムでリソースを格納できます。 これらのテンプレートは、継続的インテグレーション/継続的なデプロイ (CI/CD) プロセスの一環として、同時または個別にデプロイできます。

### <a name="diagnostics-and-monitoring"></a>診断および監視

API Management や Logic Apps のように、Azure Monitor を使用して Service Bus を監視できます。 Azure Monitor は、各サービスに構成されているメトリックに基づいて情報を提供します。 Azure Monitor は、既定で有効になっています。

## <a name="security"></a>セキュリティ

SAS を使って Service Bus をセキュリティで保護します。 [SAS 認証](../service-bus-messaging/service-bus-sas.md)を使用して、ユーザーに特定の権限で Service Bus リソースへのアクセス権を付与できます。 詳細については、「[Service Bus の認証と承認](../service-bus-messaging/service-bus-authentication-and-authorization.md)」を参照してください。

Service Bus キューが (新しいメッセージの投稿を許可するために) HTTP エンドポイントとして公開される必要がある場合、API Management を使用して、エンドポイントをフロントに配置してセキュリティを確保する必要があります。 必要に応じて、このエンドポイントは証明書または OAuth を使ってセキュリティで保護できます。 エンドポイントをセキュリティで保護する最も簡単な方法は、要求/応答の HTTP トリガーを仲介としてロジック アプリを使用することです。

Event Grid は、検証コードを使ってイベント配信をセキュリティで保護します。 Logic Apps を利用してイベントを使用する場合、検証は自動的に行われます。 詳細については、「[Event Grid security and authentication](../event-grid/security-authentication.md)」(Event Grid のセキュリティと認証) を参照してください。

## <a name="next-steps"></a>次の手順

- [単純なエンタープライズ統合](logic-apps-architectures-simple-enterprise-integration.md)について学習します。