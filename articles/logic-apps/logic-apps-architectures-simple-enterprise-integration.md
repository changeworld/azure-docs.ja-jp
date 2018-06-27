---
title: Azure Integration Services - 単純なエンタープライズ統合
description: Azure Logic Apps と Azure API Management を使って単純なエンタープライズ統合パターンを実装する方法を示す参照アーキテクチャ
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
ms.openlocfilehash: 17f591a470bf65d3c9365bca9c5ae2d5a6c9574f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232113"
---
# <a name="simple-enterprise-integration---reference-architecture"></a>単純なエンタープライズ統合 - 参照アーキテクチャ

## <a name="overview"></a>概要

この参照アーキテクチャでは、Azure Integration Services を使用する統合アプリケーションの一連の実証済みプラクティスを示します。 このアーキテクチャは、HTTP API、ワークフロー、およびオーケストレーションを必要とする、多数のさまざまなアプリケーション パターンの基準として使用できます。

*単純なポイント間アプリケーションから完全なエンタープライズ サービス バスまで、選択可能な統合テクノロジは多数あります。このアーキテクチャ シリーズは、任意の統合アプリケーションを構築するための再利用可能なコンポーネント パーツを提供します。設計担当者は、アプリケーションおよびインフラストラクチャに必要なコンポーネントを検討する必要があります。*

   ![アーキテクチャの図 - 単純なエンタープライズ統合](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

## <a name="architecture"></a>アーキテクチャ

アーキテクチャには次のコンポーネントがあります。

- リソース グループ。 リソース グループは、Azure リソースの論理コンテナーです。
- Azure API Management。 Azure API Management は、HTTP API を公開、セキュリティ保護、および変換するための完全なマネージド プラットフォームです。
- Azure API Management 開発者ポータル。 ドキュメント、コード サンプル、および API をテストする機能にアクセスできるように、開発者ポータルには Azure API Management の各インスタンスが用意されています。
- Azure Logic Apps。 Logic Apps は、エンタープライズ ワークフローと統合を構築するための、サーバーなしのプラットフォームです。
- コネクタ。 コネクタは、一般利用されるサービスに接続するために、Logic Apps によって使用されます。 Logic Apps には既に多数のさまざまなコネクタがありますが、カスタム コネクタを使用してロジック アプリを作成することも可能です。
- IP アドレス。 Azure API Management サービスには、固定のパブリック IP アドレスとドメイン名があります。 ドメイン名は、contoso azure-api.net などの azure-api.net のサブドメインです。 Logic Apps にはパブリック IP アドレスも用意されていますが、このアーキテクチャでは、ロジック アプリ エンドポイントを呼び出すためのアクセスを API Management の IP アドレスのみに制限しています (セキュリティのため)。
- Azure DNS。 Azure DNS は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。 カスタム ドメイン名 (contoso.com など) を使用するには、カスタム ドメイン名を IP アドレスにマップする DNS レコードを作成します。 詳細については、Azure API Management でのカスタム ドメイン名の構成に関するページを参照してください。
- Azure Active Directory (Azure AD)。 Azure AD または他の認証 ID プロバイダーを使用します。 Azure AD では、(API Management 用の JSON Web トークンを渡して検証することで) API エンドポイントにアクセスするための認証を提供し、API Management 開発者ポータルへのアクセスをセキュリティで保護できます (Standard および Premium レベルのみ)。

このアーキテクチャには、操作するためのいくつかの基本パターンがあります。

1. 既存のバックエンド HTTP API は API Management 開発者ポータル経由で公開され、開発者は (組織の内部および外部のいずれかまたは両方で) これらの API の呼び出しをアプリケーションに統合できます。
2. 複合 API は Logic Apps を使用して構築されており、SAAS システム、Azure サービス、および API Management に公開された任意の API の呼び出しを調整します。 また、Logic Apps は API Management 開発者ポータル経由で公開されます。
3. アプリケーションでは、Azure Active Directory を使用して API へのアクセスを獲得するために必要な OAuth 2.0 セキュリティ トークンを取得します。
4. Azure API Management はセキュリティ トークンを検証し、バックエンドの API/Logic Apps に要求を渡します。

## <a name="recommendations"></a>Recommendations

実際の要件は、ここで説明するアーキテクチャとは異なる場合があります。 このセクションに記載されている推奨事項は原案として使用してください。

### <a name="azure-api-management-tier"></a>Azure API Management のレベル

Basic、Standard、または Premium レベルでは、運用の SLA を提供し、Azure リージョン内でのスケールアウトをサポートしている (ユニット数はレベルに応じる) ため、これらのレベルを使用してください。 また、Premium レベルでは、複数の Azure リージョン間でのスケール アウトもサポートしています。 選択するレベルは、必要なスループットのレベルと機能セットに基づきます。 詳しくは、「[API Management の価格](https://azure.microsoft.com/pricing/details/api-management/)」を参照してください。

すべての API Management インスタンスは、実行された場合に課金されます。 スケールアップしても、常にそのパフォーマンス レベルを必要としない場合は、API Management の時間単位の課金を利用して、スケールダウンすることを検討してください。

### <a name="logic-apps-pricing"></a>Logic Apps の価格

Logic Apps は、[サーバーなしの](logic-apps-serverless-overview.md)モデルとして機能します。課金は、アクションとコネクタの実行に基づいて計算されます。 詳細については、「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)」を参照してください。 現在、Logic Apps には、レベルに関する考慮事項はありません。

### <a name="logic-apps-for-asynchronous-api-calls"></a>非同期 API 呼び出しの Logic Apps

Logic Apps は、非同期や準長期実行の API 呼び出しなど、低待機時間を必要としないシナリオで、最適に動作します。 低待機時間が必要になる場合は (ユーザー インターフェイスをブロックする呼び出しなど)、App Service を使用してデプロイされた Azure Functions や Web API など、異なるテクノロジを利用している該当の API や操作を実装することをお勧めします。 その上で、API コンシューマーに対しては API Management を使って、この API で対応することをお勧めします。

### <a name="region"></a>リージョン

API Management および Logic Apps を同じリージョンにプロビジョニングして、ネットワーク待機時間を最小限に抑えます。 一般的には、ユーザーに最も近いリージョンを選択してください。

リソース グループにもリージョンがあります。このリージョンによって、メタデータのデプロイの格納場所と、テンプレートのデプロイの実行元の場所が指定されます。 リソース グループとそのリソースは同じリージョンに配置してください。 これにより、デプロイ時の可用性が向上する可能性があります。

## <a name="scalability-considerations"></a>スケーラビリティに関する考慮事項

API Management 管理者は、サービスのスケーラビリティの向上とバックエンド サービスの負荷の軽減に適した[キャッシュ ポリシー](../api-management/api-management-howto-cache.md)を追加する必要があります。

Azure API Management の Basic、Standard および Premium レベルは、Azure リージョン内でより大きな容量を提供するために、スケールアウトできます。 管理者は、[メトリック] メニュー内にある [容量メトリック] を使用してサービスの使用状況を分析し、適切にスケールアップまたはスケールダウンできます。

API Management サービスのスケーリングに関する推奨事項を次に示します。

- スケーリングでは、トラフィック パターンを考慮する必要があります。顧客が揮発性の高いトラフィック パターンを利用している場合は、容量増大へのニーズが高くなります。
- 容量が一貫して 66% を上回る場合は、スケールアップの必要があることを示しています。
- 容量が一貫して 20% を下回る場合は、スケールダウンの機会であることを示しています。
- 運用環境で有効にする前に、想定される負荷でお使いの API Management サービスのロードテストを行うことが常に推奨されます。

Premium レベルのサービスは、複数の Azure リージョン間でスケールアウトできます。 この方法でデプロイしている顧客は、より高い SLA (99.9% に対して 99.95%) を獲得し、複数のリージョンにいるユーザーに近いサービスをプロビジョニングできます。

Logic Apps はサーバーなしのモデルなので、管理者がサービスのスケーラビリティに関して余計な考慮を行う必要はありません。サービスが自動的に、ニーズに合うようにスケーリングします。

## <a name="availability-considerations"></a>可用性に関する考慮事項

この資料の作成時点では、Azure API Management のサービス レベル アグリーメント (SLA) は、Basic、Standard、および Premium レベルで 99.9% です。 2 つ以上のリージョンに少なくとも 1 つのユニットをデプロイする Premium レベルの構成では、SLA が 99.95% になります。

この資料の作成時点では、Azure Logic Apps のサービス レベル アグリーメント (SLA) は 99.9% です。

### <a name="backups"></a>バックアップ

Azure API Management の構成を (定期的な変更に基づいて適切に) [定期的にバックアップする](../api-management/api-management-howto-disaster-recovery-backup-restore.md)必要があります。また、バックアップ ファイルは任意の 1 箇所、またはサービスが配置されている場所とは異なる Azure リージョンに格納する必要があります。 顧客は、次に示す 2 つの DR 戦略のいずれかを選択できます。

1. DR イベントでは、新しい API Management インスタンスがプロビジョニングされ、バックアップがこのインスタンスに復元されて、DNS レコードが補完されます。
2. 顧客は、別の Azure リージョンにサービスのパッシブ コピーを保持し (追加コストが発生します)、バックアップがそこに定期的に復元されます。 DR イベントでは、サービスを復元するために DNS レコードのみが補完される必要があります。

Logic Apps は極めて迅速に再作成でき、しかもシームレスなので、関連する Azure Resource Manager テンプレートのコピーを保存してバックアップされます。 これらは、ソース コントロールに保存して、顧客の継続的インテグレーション/継続的配置 (CI/CD) プロセスに統合できます。

API Management 経由で公開された Logic Apps は、配置場所を更新して、別のデータ センターに移動する必要があります。 この移動は、API の Backend プロパティを更新する簡単な PowerShell スクリプトを使って実現できます。

## <a name="manageability-considerations"></a>管理容易性に関する考慮事項

運用、開発、およびテスト環境それぞれに対して個別のリソース グループを作成してください。 これにより、デプロイの管理、テスト デプロイの削除、およびアクセス権の割り当てを行いやすくなります。
リソースをリソース グループに割り当てるときは、以下の事項を検討してください。

- ライフサイクル。 一般的に、同じライフサイクルのリソースは、同じリソース グループに追加します。
- アクセス。 [ロールベースのアクセス制御](../role-based-access-control/overview.md) (RBAC) を使用して、アクセス ポリシーをグループ内のリソースに適用します。
- 課金。 リソース グループのロールアップ コストを表示できます。
- API Management の価格レベル – 開発環境およびテスト環境には、Developer レベルを使用することをお勧めします。 運用前環境には、本番環境のレプリカをデプロイしてテストを実行し、コストを最小限に抑えることをお勧めします。

詳細については、[リソース グループ](../azure-resource-manager/resource-group-overview.md)の概要に関するページを参照してください。

### <a name="deployment"></a>デプロイ

[Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md)を使用して、Azure API Management および Azure Logic Apps の両方をデプロイすることを勧めします。 テンプレートにより、PowerShell または Azure コマンド ライン インターフェイス (CLI) を使用したデプロイが自動化しやすくなります。

Azure API Management と個々 の任意の Logic Apps を別々の独自の Resource Manager テンプレートに配置することをお勧めします。 この配置によって、これらをソース管理システムに格納できるようになります。 これらのテンプレートは、継続的インテグレーション/継続的配置 (CI/CD) プロセスの一環として、同時または個別にデプロイできます。

### <a name="versions"></a>バージョン

Logic App に構成の変更を加える (または、Resource Manager テンプレートを使って更新プログラムをデプロイする) たびに、便宜上、該当のバージョンのコピーが保管されます (実行履歴のあるすべてのバージョンが保管されます)。 これらのバージョンを使用して変更履歴を追跡できると共に、ロジック アプリの現在の構成になるようにバージョンを昇格することもできます。これを行うことで、たとえば Logic App を効果的にロールバックすることになります。

API Management には、2 つの異なる (ただし補完的な) [バージョンの概念](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/)があります。

- API コンシューマーにニーズに基づいて利用可能な API の選択肢を提供するために、使用されるバージョン (v1、v2、ベータ、運用など)。
- API 管理者による安全な API への変更と、オプション注釈付きでのユーザーへの API のデプロイを可能にするリビジョン。

デプロイメントの際には、安全に変更を加え、変更履歴を保持し、API コンシューマーに変更を通知する手段として、API Management のリビジョンを検討する必要があります。 リビジョンは開発環境で作成でき、Resource Manager テンプレートを使って他の環境との間でデプロイできます。

リビジョンは、API が 'current' の状態になってユーザーが利用可能になる前に、API をテストするために使用できますが、ロードテストまたは統合テストにこのメカニズムを使用することはお勧めしません。リビジョンではなく、個別のテスト環境または運用前環境を使用する必要があります。

### <a name="configuration"></a>構成

パスワード、アクセス キー、または接続文字列をソース管理にチェックインしないでください。 必要な場合は、適切な手段を使用して、これらの値をデプロイしてセイキュリティ保護してください。 

Logic Apps では、ロジック アプリ内で必要な (接続の形式では作成できない) 機密の値はすべて、Azure Key Vault に格納され、Resource Manager テンプレートから参照される必要があります。 また、各環境のパラメーター ファイルと共に、テンプレートのデプロイのパラメーターを使用することをお勧めします。 詳しいガイダンスについては、「[ワークフロー内のパラメーターと入力のセキュリティ保護](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow)」を参照してください。

API Management で、機密情報は "名前付きの値/プロパティ" というオブジェクトを使用して管理されます。 これらのオブジェクトでは、アクセス可能な値を API Management ポリシーに格納します。 [API Management で機密情報を管理する](../api-management/api-management-howto-properties.md)方法を確認してください。

### <a name="diagnostics-and-monitoring"></a>診断および監視

[API Management](../api-management/api-management-howto-use-azure-monitor.md) および [Logic Apps](logic-apps-monitor-your-logic-apps.md) の両方で、[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) を使った操作の監視がサポートされています。 Azure Monitor は既定で有効になっており、各サービスに対して構成されたさまざまなメトリックに基づく情報を提供します。

さらに、各サービスに対して次のオプションが用意されています。

- Logic Apps ログは、より詳細な分析およびダッシュボード化のために、[Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) に送信できます。
- API Management では、Dev Ops 監視のための Application Insights の構成をサポートしています。
- API Management では、[カスタム API 分析のための Power BI ソリューション テンプレート](http://aka.ms/apimpbi)をサポートしています。 このソリューション テンプレートでは、ビジネス ユーザー用の Power BI で利用可能なレポートを使って、独自のカスタム分析ソリューションを作成できます。

## <a name="security-considerations"></a>セキュリティに関する考慮事項

このセクションでは、説明に従ってアーキテクチャにデプロイされている、この記事で説明した Azure サービス固有のセキュリティ上の考慮事項を示します。 これはセキュリティ上のベスト プラクティスを網羅した一覧ではありません。

- ロール ベースのアクセス制御 (RBAC) を使用して、適切なレベルでのユーザーへのアクセスを保証する。
- OAuth/Open IDConnect を使用して、API Management にあるパブリック API エンドポイントをセキュリティで保護する。 ID プロバイダーを構成し、JWT 検証ポリシーを追加することで、これを行います。
- 相互証明書を使用して、API Management からバックエンド サービスに接続する。
- API Management の IP アドレスを参照する IP アドレス ホワイトリストを作成することで HTTP トリガーベースの Logic Apps をセキュリティで保護する。 これにより、最初に API Management を経由せずに、パブリック インターネットからロジック アプリが呼び出されることを回避します。

この参照アーキテクチャでは、Azure Integration Services を使用して単純なエンタープライズ統合プラットフォームを構築する方法を示しました。

## <a name="next-steps"></a>次の手順

* [キューとイベントによるエンタープライズ統合](logic-apps-architectures-enterprise-integration-with-queues-events.md)
