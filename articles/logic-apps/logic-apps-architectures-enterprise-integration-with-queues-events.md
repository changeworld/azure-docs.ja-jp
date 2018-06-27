---
title: Azure Integration Services 参照アーキテクチャ
description: Logic Apps、API Management、Service Bus、および Event Grid を使ってエンタープライズ統合パターン を実装する方法を示す参照アーキテクチャ
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
ms.openlocfilehash: db3350b3c70f6e6f35949e8423334061849b7b37
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232209"
---
# <a name="enterprise-integration-with-queues-and-events-reference-architecture"></a>キューとイベントによるエンタープライズ統合: 参照アーキテクチャ

## <a name="overview"></a>概要

この参照アーキテクチャでは、Azure Integration Services を使用する統合アプリケーションの一連の実証済みプラクティスを示します。 このアーキテクチャは、HTTP API、ワークフロー、およびオーケストレーションを必要とする、多数のさまざまなアプリケーション パターンの基準として使用できます。

*単純なポイント間アプリケーションから完全なエンタープライズ サービス バスまで、選択可能な統合テクノロジは多数あります。このアーキテクチャ シリーズは、任意の統合アプリケーションを構築するための再利用可能なコンポーネント パーツを提供します。設計担当者は、アプリケーションおよびインフラストラクチャに必要なコンポーネントを検討する必要があります。*

![アーキテクチャの図 - キューとイベントによるエンタープライズ統合](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

## <a name="architecture"></a>アーキテクチャ

このアーキテクチャは、「[simple enterprise integration](logic-apps-architectures-simple-enterprise-integration.md)」(単純なエンタープライズ統合) で説明されているアーキテクチャに基づいて構築されています。 アーキテクチャに含まれるコンポーネントを次に示します。

- リソース グループ。 リソース グループは、Azure リソースの論理コンテナーです。
- Azure API Management。 Azure API Management は、HTTP API を公開、セキュリティ保護、および変換するための完全なマネージド プラットフォームです。
- Azure API Management 開発者ポータル。 ドキュメント、コード サンプル、および API をテストする機能にアクセスできるように、開発者ポータルには Azure API Management の各インスタンスが用意されています。
- Azure Logic Apps。 Logic Apps は、エンタープライズ ワークフローと統合を構築するための、サーバーなしのプラットフォームです。
- コネクタ。 コネクタは、一般利用されるサービスに接続するために、Logic Apps によって使用されます。 Logic Apps には既に多数のさまざまなコネクタがありますが、カスタム コネクタを使用してロジック アプリを作成することも可能です。
- Azure Service Bus。 Service Bus は、セキュリティで保護された信頼性の高いメッセージングを提供します。 相互に切り離されたアプリケーションに対してメッセージングを使用し、他のメッセージベース システムと統合できます。
- Azure Event Grid。 Event Grid は、アプリケーション イベントを公開して配信するための、サーバーなしのプラットフォームです。
- IP アドレス。 Azure API Management サービスには、固定のパブリック IP アドレスとドメイン名があります。 ドメイン名は、contoso.azure-api.net などの azure-api.net のサブドメインです。 Logic Apps および Service Bus にはパブリック IP アドレスも用意されていますが、このアーキテクチャでは、ロジック アプリ エンドポイントへのアクセスを API Management の IP アドレスのみに制限しています (セキュリティのため)。 Service Bus の呼び出しは、共有アクセス署名によってセキュリティで保護されています。
- Azure DNS。 Azure DNS は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。 カスタム ドメイン名 (contoso.com など) を使用するには、カスタム ドメイン名を IP アドレスにマップする DNS レコードを作成します。 詳細については、Azure API Management でのカスタム ドメイン名の構成に関するページを参照してください。
- Azure Active Directory (Azure AD)。 Azure AD または他の認証 ID プロバイダーを使用します。 Azure AD では、(API Management 用の JSON Web トークンを渡して検証することで) API エンドポイントにアクセスするための認証を提供し、API Management 開発者ポータルへのアクセスをセキュリティで保護できます (Standard および Premium レベルのみ)。

このアーキテクチャには、操作するためのいくつかの基本パターンがあります。

1. 既存のバックエンド HTTP API は API Management 開発者ポータル経由で公開され、開発者は (組織の内部および外部のいずれかまたは両方で) これらの API の呼び出しをアプリケーションに統合できます。
2. 複合 API は Logic Apps を使用して構築されており、SAAS システム、Azure サービス、および API Management に公開された任意の API の呼び出しを調整します。 また、Logic Apps は API Management 開発者ポータル経由で公開されます。
3. アプリケーションでは、Azure Active Directory を使用して API へのアクセスを獲得するために必要な OAuth 2.0 セキュリティ トークンを取得します。
4. Azure API Management はセキュリティ トークンを検証し、バックエンドの API/Logic Apps に要求を渡します。
5. Service Bus キューは、読み込み時にアプリケーションの動作と速やかな急上昇を切り離します。 メッセージングは、Logic Apps、サード パーティ製アプリケーションによってキューに追加されるか、または (図には示されていない) API Management 経由で HTTP API としてキューを公開することでキューに追加されます。
6. メッセージが Service Bus キューに追加されると、イベントが発生します。 Logic App はこのイベントによってトリガーされ、メッセージを処理します。
7. 同様に、他の Azure サービス (Blob Storage、Event Hub など) も、イベントを Event Grid に公開します。 これらは Logic Apps をトリガーしてイベントを受信し、後続のアクションを実行します。

## <a name="recommendations"></a>Recommendations

実際の要件は、ここで説明するアーキテクチャとは異なる場合があります。 このセクションに記載されている推奨事項は原案として使用してください。

### <a name="service-bus-tier"></a>Service Bus のレベル

現在、Premium レベルでイベント グリッド通知がサポートされているため、Service Bus の Premium レベルを使用します (すべてのレベルでのサポートが期待されています)。 「[Service Bus の価格](https://azure.microsoft.com/pricing/details/service-bus/)」を参照してください。

### <a name="event-grid-pricing"></a>Event Grid の価格

Event Grid は、サーバーなしのモデルを使用して動作します。課金は、操作数に基づいて計算されます (イベントの実行)。 詳細については、「[Event Grid の価格](https://azure.microsoft.com/pricing/details/event-grid/)」を参照してください。 現在、Event Grid には、レベルに関する考慮事項はありません。

### <a name="use-peeklock-when-consuming-service-bus-messages"></a>Service Bus メッセージを使用する場合の PeekLock の使用

Logic Apps を作成して Service Bus メッセージを使用した場合、メッセージのグループにアクセスするために、Logic App 内で [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) を使用します。 その後、Logic App は、完了または中止する前に、各メッセージを検証する手順を実行できます。 この方法によって、メッセージの誤損失から保護します。

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Event Grid トリガーが発生した場合に複数のオブジェクトをチェックする

Event Grid でのトリガーの発生は単純に、"これらのうちの少なくとも 1 つが発生した" ことを意味します。 たとえば、Event Grid が Service Bus キューに表示されたメッセージ上でロジック アプリをトリガーした場合、ロジック アプリでは常に、1 つ以上のメッセージを処理に利用できることを前提としています。

### <a name="region"></a>リージョン

API Management、Logic Apps、および Service Bus を同じリージョンにプロビジョニングして、ネットワーク待機時間を最小限に抑えます。 一般的には、ユーザーに最も近いリージョンを選択してください。

リソース グループにもリージョンがあります。このリージョンによって、メタデータのデプロイの格納場所と、テンプレートのデプロイの実行元の場所が指定されます。 リソース グループとそのリソースは同じリージョンに配置してください。 これにより、デプロイ時の可用性が向上する可能性があります。

## <a name="scalability-considerations"></a>スケーラビリティに関する考慮事項

Azure Service Bus の Premium レベルは、より高いスケーラビリティを実現するためにメッセージング ユニットの数をスケールアウトできます。 Premium では、1、2、または 4 個のメッセージング ユニットを保持できます。 Azure Service Bus のスケーリングの詳細については、「[Service Bus メッセージングを使用したパフォーマンス向上のためのベスト プラクティス](../service-bus-messaging/service-bus-performance-improvements.md)」を参照してください。

## <a name="availability-considerations"></a>可用性に関する考慮事項

この資料の作成時点では、Azure API Management のサービス レベル アグリーメント (SLA) は、Basic、Standard、および Premium レベルで 99.9% です。 2 つ以上のリージョンに少なくとも 1 つのユニットをデプロイする Premium レベルの構成では、SLA が 99.95% になります。

この資料の作成時点では、Azure Logic Apps のサービス レベル アグリーメント (SLA) は 99.9% です。

### <a name="disaster-recovery"></a>ディザスター リカバリー

重大な障害が発生した場合にフェールオーバーを有効にするために、Service Bus の Premium で geo ディザスター リカバリーを実装することを検討します。 「[Azure Service Bus の geo ディザスター リカバリー](../service-bus-messaging/service-bus-geo-dr.md)」を参照してください。

## <a name="manageability-considerations"></a>管理容易性に関する考慮事項

運用、開発、およびテスト環境それぞれに対して個別のリソース グループを作成してください。 これにより、デプロイの管理、テスト デプロイの削除、およびアクセス権の割り当てを行いやすくなります。
リソースをリソース グループに割り当てるときは、以下の事項を検討してください。

- ライフサイクル。 一般的に、同じライフサイクルのリソースは、同じリソース グループに追加します。
- アクセス。 [ロールベースのアクセス制御](../role-based-access-control/overview.md) (RBAC) を使用して、アクセス ポリシーをグループ内のリソースに適用します。
- 課金。 リソース グループのロールアップ コストを表示できます。
- API Management の価格レベル – 開発環境およびテスト環境には、Developer レベルを使用することをお勧めします。 運用前環境には、本番環境のレプリカをデプロイしてテストを実行し、コストを最小限に抑えることをお勧めします。

詳細については、[リソース グループ](../azure-resource-manager/resource-group-overview.md)の概要に関するページを参照してください。

### <a name="deployment"></a>デプロイ

[Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md)を使用して、Azure API Management、Logic Apps、Event Grid、および Service Bus をデプロイすることを勧めします。 テンプレートにより、PowerShell または Azure コマンド ライン インターフェイス (CLI) を使用したデプロイが自動化しやすくなります。

Azure API Management と個々 の任意の Logic Apps、Event Grid トピック、および Service Bus 名前空間を別々の独自の Resource Manager テンプレートに配置することをお勧めします。 この配置によって、これらをソース管理システムに格納できるようになります。 これらのテンプレートは、継続的インテグレーション/継続的配置 (CI/CD) プロセスの一環として、同時または個別にデプロイできます。

### <a name="diagnostics-and-monitoring"></a>診断および監視

API Management や Logic Apps などの Service Bus は、Azure Monitor を使用して監視できます。 Azure Monitor は既定で有効になっており、各サービスに対して構成されたさまざまなメトリックに基づく情報を提供します。

## <a name="security-considerations"></a>セキュリティに関する考慮事項

Shared Access Signature を使用して Service Bus をセキュリティで保護します。 [SAS 認証](../service-bus-messaging/service-bus-sas.md)により、特定の権限で Service Bus リソースにアクセスできるようになります。 詳細については、「[Service Bus の認証と承認](../service-bus-messaging/service-bus-authentication-and-authorization.md)」を参照してください。

さらに、Service Bus キューは、(新しいメッセージの投稿を許可するために) HTTP エンドポイントとして公開される必要があり、API Management は、エンドポイントをフロントに配置してセキュリティを確保するために使用する必要があります。 必要に応じて、これは証明書または OAuth を使ってセキュリティで保護できます。 これを行う最も簡単な方法は、要求/応答の HTTP トリガーを仲介として Logic App を 使用することです。

Event Grid は、検証コードを使ってイベント配信をセキュリティで保護します。 LogicApps を利用してイベントを使用する場合は、この保護が自動的に行われます。 詳細については、「[Event Grid のセキュリティと認証](../event-grid/security-authentication.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* [単純なエンタープライズ統合](logic-apps-architectures-simple-enterprise-integration.md)
