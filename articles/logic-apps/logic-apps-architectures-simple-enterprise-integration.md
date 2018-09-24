---
title: 単純なエンタープライズ統合アーキテクチャのパターン - Azure Integration Services
description: このアーキテクチャのリファレンスでは、Azure Logic Apps と Azure API Management を使ってシンプルなエンタープライズ統合パターンを実装できる方法を示します。
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 5ed6fa9f514bae3ea651edba6702714e2680091f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955947"
---
# <a name="simple-enterprise-integration-architecture"></a>単純なエンタープライズ統合アーキテクチャ

この記事では、Azure Integration Services を使用する際に統合アプリケーションに適用できる実証済みプラクティスを使ったエンタープライズ統合アーキテクチャについて説明します。 このアーキテクチャは、HTTP API、ワークフロー、およびオーケストレーションを必要とする、多数のさまざまなアプリケーション パターンの基準として使用できます。

![アーキテクチャの図 - シンプルなエンタープライズ統合](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

このシリーズは、汎用的な統合アプリケーションのビルドに適用できる再利用可能なコンポーネント パーツについて説明しています。 統合テクノロジには単純なポイントツーポイント アプリから完全なエンタープライズ Azure Service Bus アプリに至る多くの用途があるため、アプリやインフラストラクチャにどのコンポーネントを実装する必要があるかを検討してください。

## <a name="architecture-components"></a>アーキテクチャ コンポーネント

このエンタープライズ統合アーキテクチャには、次のコンポーネントが含まれます。

- **リソース グループ**: [リソース グループ](../azure-resource-manager/resource-group-overview.md)は、Azure リソースの論理コンテナーです。

- **Azure API Management**: [API Management](https://docs.microsoft.com/azure/api-management/) サービスは、HTTP API を公開、セキュリティ保護、および変換するためのフル マネージド プラットフォームです。

- **Azure API Management 開発者ポータル**: Azure API Management の各インスタンスは、[開発者ポータル](../api-management/api-management-customize-styles.md)へのアクセスを提供します。 このポータルでは、ドキュメントとコード サンプルにアクセスできます。 開発者ポータル内で API をテストすることもできます。

- **Azure Logic Apps**: [Logic Apps](../logic-apps/logic-apps-overview.md) は、エンタープライズ ワークフローと統合を構築するためのサーバーレス プラットフォームです。

- **コネクタ**: Logic Apps では[コネクタ](../connectors/apis-list.md)を使用して、よく利用するサービスに接続します。 Logic Apps には何百ものコネクタが用意されていますが、カスタム コネクタを作成することもできます。

- **IP アドレス**: Azure API Management サービスには、固定のパブリック [IP アドレス](../virtual-network/virtual-network-ip-addresses-overview-arm.md)とドメイン名があります。 既定のドメイン名は azure-api.net のサブドメイン (例: contoso.azure-api.net) ですが、[カスタム ドメイン](../api-management/configure-custom-domain.md)も構成できます。 Logic Apps と Service Bus にはパブリック IP アドレスもあります。 ただし、このアーキテクチャでは、セキュリティのために、Logic Apps エンドポイントを呼び出すためのアクセスを API Management の IP アドレスのみに制限しています。 Service Bus の呼び出しは、共有アクセス署名 (SAS) によってセキュリティで保護されています。

- **Azure DNS**: [Azure DNS](https://docs.microsoft.com/azure/dns/) は、DNS ドメインのホスティング サービスです。 Azure DNS は、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスに使用しているのと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。 カスタム ドメイン名 (contoso.com など) を使用するには、カスタム ドメイン名を IP アドレスにマップする DNS レコードを作成します。 詳細については、[API Management でのカスタム ドメイン名の構成](../api-management/configure-custom-domain.md)に関するページを参照してください。

- **Azure Active Directory (Azure AD)**: [Azure AD](https://docs.microsoft.com/azure/active-directory/) または他の認証 ID プロバイダーを認証に使用できます。 Azure AD は、[API Management 用の JSON Web トークン](../api-management/policies/authorize-request-based-on-jwt-claims.md)を渡して検証することで、API エンドポイントにアクセスするための認証を提供します。 Standard および Premium レベルの場合、Azure AD によって API Management 開発者ポータルへのアクセスをセキュリティで保護することができます。

## <a name="patterns"></a>パターン 

このアーキテクチャでは、操作の基本となるいくつかのパターンを使用します。

* 複合 API は、サービスとしてのソフトウェア (SaaS) システム、Azure サービス、および API Management に公開された任意の API の呼び出しを調整するロジック アプリを使用してビルドされます。 また、ロジック アプリは、[API Management 開発者ポータル経由](../api-management/import-logic-app-as-api.md)で公開されます。

* アプリケーションでは、Azure AD を使用して、API へのアクセスを獲得するために必要な [OAuth 2.0 セキュリティ トークンを取得](../api-management/api-management-howto-protect-backend-with-aad.md)します。

* Azure API Management は[セキュリティ トークンを検証](../api-management/api-management-howto-protect-backend-with-aad.md)してから、バックエンドの API またはロジック アプリに要求を渡します。

## <a name="recommendations"></a>Recommendations

この記事で説明されている一般的なアーキテクチャとは異なる、特定の要件が生じる場合があります。 このセクションに記載されている推奨事項は原案として使用してください。

### <a name="azure-api-management-tier"></a>Azure API Management のレベル

API Management の Basic、Standard、または Premium レベルを使用します。 これらのレベルでは、運用のサービス レベル アグリーメント (SLA) を提供し、Azure リージョン内でのスケールアウトをサポートしています。 ユニットの数は、レベルによって異なります。 また、Premium レベルでは、複数の Azure リージョン間でのスケールアウトもサポートしています。 お使いの機能セットと必要なスループットのレベルに基づいて、使用するレベルを選択します。 詳しくは、「[API Management の価格](https://azure.microsoft.com/pricing/details/api-management/)」を参照してください。

すべての API Management インスタンスは、実行された場合に課金されます。 スケールアップしても、常にそのパフォーマンス レベルを必要としない場合は、API Management の時間単位の課金を利用して、スケールダウンすることを検討してください。

### <a name="logic-apps-pricing"></a>Logic Apps の価格

Logic Apps では、[サーバーレス](../logic-apps/logic-apps-serverless-overview.md) モデルを使用します。 課金はアクションとコネクタの実行に基づいて計算されます。 詳細については、「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)」をご覧ください。 現在、Logic Apps には、レベルに関する考慮事項はありません。

### <a name="logic-apps-for-asynchronous-api-calls"></a>非同期 API 呼び出しの Logic Apps

Logic Apps は、低待機時間を必要としないシナリオで最適に機能します。 たとえば、Logic Apps は、非同期または実行時間がやや長い API 呼び出しで最適に機能します。 低待機時間が必要になる場合は (ユーザー インターフェイスをブロックする呼び出しなど)、別のテクノロジを利用して該当する API または操作を実装します。 たとえば、Azure App Service を使用してデプロイされた Azure Functions または Web API を使用します。 API Management を使用して、API コンシューマーに向けて API を配置します。

### <a name="region"></a>リージョン

ネットワーク待機時間を最小限に抑えるには、API Management、Logic Apps、および Service Bus に対して同じリージョンを選択します。 通常は、ユーザーに最も近いリージョンを選択します。

リソース グループにもリージョンがあります。 このリージョンによって、デプロイ メタデータを格納する場所と、デプロイ テンプレートを実行する場所が指定されます。 デプロイ時の可用性を向上させるには、リソース グループとそのリソースを同じリージョンに配置します。

## <a name="scalability"></a>スケーラビリティ

API Management サービスを管理している場合にスケーラビリティを向上させるには、適切な場所に[キャッシュ ポリシー](../api-management/api-management-howto-cache.md)を追加します。 また、キャッシュはバックエンド サービスに対する負荷を軽減するのに役立ちます。

提供する容量をより大きくするために、Azure リージョン内で Azure API Management をBasic、Standard、および Premium レベルにスケールアウトできます。 サービスの使用状況を分析するには、**[メトリック]** メニューで、**[容量メトリック]** オプションを選択して、適切にスケールアップまたはスケールダウンします。

API Management サービスのスケーリングに関する推奨事項を次に示します。

- スケーリングする際はトラフィック パターンを検討します。 トラフィック パターンが変動しやすい顧客は、より大きな容量を必要とします。

- 容量が一貫して 66% を上回る場合は、スケールアップの必要があることを示している可能性があります。

- 容量が一貫して 20% を下回る場合は、スケールダウンの機会であることを示している可能性があります。

- 運用環境で読み込みを有効にする前に、想定される負荷で API Management サービスのロードテストを必ず行います。

Premium レベルのサービスは、複数の Azure リージョン間でスケールアウトできます。 複数の Azure リージョン間でサービスをスケーリングすることによってデプロイしている場合、より高い SLA (99.9% に対して 99.95%) を獲得し、複数のリージョンにいるユーザーの近くにサービスをプロビジョニングできます。

Logic Apps サーバーレス モデルによって、管理者はサービスのスケーラビリティについて計画を立てる必要がなくなります。 サービスは、需要に合わせて自動的にスケーリングされます。

## <a name="availability"></a>可用性

* Basic、Standard、および Premium レベルの場合、Azure API Management のサービス レベル アグリーメント (SLA) は現在 99.9% です。 2 つ以上のリージョンに少なくとも 1 つのユニットをデプロイする Premium レベルの構成では、SLA が 99.95% になります。

* Azure Logic Apps の SLA は、現在 99.9% です。

### <a name="backups"></a>バックアップ

定期的な変更に基づいて、お使いの Azure API Management の構成を[定期的にバックアップ](../api-management/api-management-howto-disaster-recovery-backup-restore.md)します。 バックアップ ファイルは、お使いのサービスが配置されている場所とは別の Azure リージョンに保管します。 ディザスター リカバリーの戦略として、次のいずれかのオプションを選択できます。

* ディザスター リカバリー イベントで、新しい API Management インスタンスをプロビジョニングして、バックアップを新しいインスタンスに復元し、DNS レコードを補完します。

* 別の Azure リージョンにサービスのパッシブ コピーを保持します。これには、追加コストが発生します。 そのコピーにバックアップを定期的に復元します。 ディザスター リカバリー イベントの中でサービスを復元するには、DNS レコードのみを補完する必要があります。

ロジック アプリは迅速に再作成することができ、しかもサーバーレスなので、関連する Azure Resource Manager テンプレートのコピーを保存することによってバックアップされます。 テンプレートはソース コントロールに保存でき、お使いの継続的インテグレーション/継続的デプロイ (CI/CD) プロセスにテンプレートを統合できます。

Azure API Management 経由でロジック アプリを公開し、そのロジック アプリを別のデータセンターに移動した場合、アプリの場所を更新してください。 基本の PowerShell スクリプトを使用して、お使いの API の **Backend** プロパティを更新できます。

## <a name="manageability"></a>管理容易性

運用、開発、およびテスト環境それぞれに対して個別のリソース グループを作成してください。 個別のリソース グループにより、デプロイの管理、テスト デプロイの削除、およびアクセス権の割り当てが行いやすくなります。

リソースをリソース グループに割り当てるときは、次の要素を検討してください。

* **ライフサイクル**: 一般的に、同じライフサイクルのリソースは、同じリソース グループに配置します。

* **アクセス**: アクセス ポリシーをグループ内のリソースに適用するには、[ロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) を使用できます。

* **課金**: リソース グループのロールアップ コストを表示できます。

* **API Management の価格レベル**: 開発環境およびテスト環境には、Developer レベルを使用します。 運用前環境のコストを最小限に抑えるには、運用環境のレプリカをデプロイしてテストを実行し、シャットダウンします。

詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。

## <a name="deployment"></a>Deployment

* API Management および Logic Apps をデプロイするには、[Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md)を使用します。 テンプレートによって、PowerShell または Azure CLI を使用してより簡単にデプロイを自動化できます。

* API Management と個々の任意のロジック アプリを別々の独自の Resource Manager テンプレートに配置します。 別々のテンプレートを使用することで、ソース管理システムにリソースを格納できます。 これらのテンプレートは、継続的インテグレーション/継続的なデプロイ (CI/CD) プロセスの一環として、同時または個別にデプロイできます。

### <a name="versions"></a>バージョン

ロジック アプリの構成に変更を加えるか、または Resource Manager テンプレートを使って更新プログラムをデプロイするたびに、Azure では便宜上、該当のバージョンのコピーを保管して、実行履歴のあるすべてのバージョンを保管します。 ロジック アプリの現在の構成として、履歴変更の追跡やバージョンの昇格にこれらのバージョンを使用できます。 たとえば、ロジック アプリを効果的にロールバックすることができます。

Azure API Management には、次に示すように、2 つの異なる補完的な[バージョンの概念](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/)があります。

* v1、v2、ベータ、または実稼働など、ニーズに基づく API バージョンを API コンシューマーが選択するための機能を提供するバージョン

* API 管理者による API での安全な変更と、オプション注釈付きでのユーザーへの変更のデプロイを可能にするリビジョン

デプロイにおいては、安全に変更し、変更履歴を保管し、それらの変更を API コンシューマーに連携するための 1 つの方法として、API Management リビジョンを検討します。 開発環境でリビジョンを作成して、Resource Manager テンプレートを使って他の環境にその変更をデプロイできます。

API を "現在" の状態にしてユーザーが利用可能になる前に、リビジョンを使用して API をテストすることはできますが、負荷テストまたは統合テストには、この方法はお勧めしません。 代わりに、異なるテスト環境または運用前環境を使用してください。

### <a name="configuration-and-sensitive-information"></a>構成と機密情報

パスワード、アクセス キー、または接続文字列を、ソース管理にチェックインしないでください。 これらの値が必要な場合は、適切な手段を使用して、値をセキュリティで保護してデプロイします。 

Logic Apps では、1 つのロジック アプリが接続内に作成できない何らかの機密値を必要としている場合、それらの値を Azure Key Vault に保存して、Resource Manager テンプレートから参照します。 デプロイ テンプレートのパラメーターと、各環境のパラメーター ファイルを使用します。 詳細については、「[ワークフロー内のパラメーターと入力のセキュリティ保護](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow)」を参照してください。

API Management は、"*名前付きの値*" または "*プロパティ*" というオブジェクトを使用して、シークレットを管理します。 これらのオブジェクトでは、API Management ポリシー経由でアクセスできる値を安全に格納します。 詳細については、[API Management での機密情報の管理](../api-management/api-management-howto-properties.md)に関するページを参照してください。

## <a name="diagnostics-and-monitoring"></a>診断および監視

[API Management](../azure-monitor/overview.md) および [Logic Apps](../api-management/api-management-howto-use-azure-monitor.md) の両方で、操作の管理に [Azure Monitor](../logic-apps/logic-apps-monitor-your-logic-apps.md) を使用できます。 Azure Monitor は、各サービスに構成されているメトリックに基づいて情報を提供し、既定で有効になります。

また、各サービスには、次のオプションがあります。

* より詳細な分析およびダッシュボード化のために、[Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) に Logic Apps ログを送信できます。

* DevOps 監視には、API Management 用に Azure Application Insights を構成できます。

* API Management では、[カスタム API 分析のための Power BI ソリューション テンプレート](http://aka.ms/apimpbi)をサポートしています。 独自の分析ソリューションを作成して、このソリューション テンプレートを使用できます。 Power BI ではビジネス ユーザー向けに、レポートを利用可能にします。

## <a name="security"></a>セキュリティ

次の一覧は、セキュリティ上のベスト プラクティスをすべて網羅しているわけではありませんが、この記事で説明したアーキテクチャにデプロイされる Azure サービスに特に該当する、セキュリティ上のいくつかの考慮事項を示しています。

* ユーザーが適切なアクセス レベルを保持していることを確認するには、ロールベースのアクセス制御 (RBAC) を使用します。

* OAuth/OpenID Connect を使用して、API Management にあるパブリック API エンドポイントをセキュリティで保護する。 パブリック API エンドポイントをセキュリティで保護するには、ID プロバイダーを構成し、JSON Web トークン (JWT) 検証ポリシーを追加します。

* 相互証明書を使用して、API Management からバックエンド サービスに接続する。

* API Management の IP アドレスを参照する IP アドレス ホワイトリストを作成することで HTTP トリガーベースのロジック アプリをセキュリティで保護する。 ホワイトリスト IP アドレスにより、最初に API Management を経由せずに、パブリック インターネットからロジック アプリが呼び出されることを回避します。

## <a name="next-steps"></a>次の手順

* [キューとイベントによるエンタープライズ統合](../logic-apps/logic-apps-architectures-enterprise-integration-with-queues-events.md)について確認する
