---
title: Azure Integration Services のシンプルなエンタープライズ統合参照アーキテクチャ
description: Azure Logic Apps と Azure API Management を使ってシンプルなエンタープライズ統合パターンを実装する方法を示す参照アーキテクチャについて説明します。
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
ms.openlocfilehash: 982a5eabf8c6c3012a9b3e8fdbe2ff32ba439972
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113594"
---
# <a name="reference-architecture-simple-enterprise-integration"></a>参照アーキテクチャ: シンプルなエンタープライズ統合

次の参照アーキテクチャでは、Azure Integration Services を使用する統合アプリケーションに適用できる一連の実証済みプラクティスを示します。 このアーキテクチャは、HTTP API、ワークフロー、およびオーケストレーションを必要とする、多数のさまざまなアプリケーション パターンの基準として使用できます。

![アーキテクチャの図 - シンプルなエンタープライズ統合](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

*統合テクノロジには多くの選択可能なアプリケーションがあります。その範囲は、シンプルなポイント ツー ポイント アプリケーションから完全なエンタープライズ Azure Service Bus まで多岐にわたります。このアーキテクチャ シリーズは、汎用的な統合アプリケーションのビルドに適用できる再利用可能なコンポーネント パーツについて説明しています。アーキテクトは、アプリケーションとインフラストラクチャの実装に必要なコンポーネントを検討する必要があります。*

## <a name="architecture"></a>アーキテクチャ

アーキテクチャには次のコンポーネントがあります。

- **リソース グループ**。 [リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)は、Azure リソースの論理コンテナーです。
- **Azure API Management**。 [API Management](https://docs.microsoft.com/azure/api-management/) は、HTTP API の公開、セキュリティでの保護、および変換に使用されるフル マネージド プラットフォームです。
- **Azure API Management 開発者ポータル**。 Azure API Management の各インスタンスには、[開発者ポータル](https://docs.microsoft.com/azure/api-management/api-management-customize-styles)へのアクセスが付属しています。 API Management 開発者ポータルでは、ドキュメントとコード サンプルにアクセスできます。 開発者ポータル内で API をテストすることができます。
- **Azure Logic Apps**。 [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) は、エンタープライズ ワークフローと統合をビルドするため使用される、サーバーレス プラットフォームです。
- **コネクタ**。 Logic Apps では[コネクタ](https://docs.microsoft.com/azure/connectors/apis-list)を使用して、よく利用するサービスに接続します。 Logic Apps には既に多くのさまざまなコネクタがありますが、カスタム コネクタを作成することもできます。
- **IP アドレス**。 Azure API Management サービスには、固定のパブリック [IP アドレス](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)とドメイン名があります。 ドメイン名は、contoso.azure-api.net などの azure-api.net のサブドメインです。 Logic Apps と Service Bus にはパブリック IP アドレスもあります。 ただし、このアーキテクチャでは、Logic Apps エンドポイントを呼び出すためのアクセスを API Management の IP アドレスのみに制限しています (セキュリティのため)。 Service Bus の呼び出しは、共有アクセス署名 (SAS) によってセキュリティで保護されています。
- **Azure DNS**。 [Azure DNS](https://docs.microsoft.com/azure/dns/) は DNS ドメインのホスティング サービスです。 Azure DNS は、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスに使用しているのと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。 カスタム ドメイン名 (contoso.com など) を使用するには、カスタム ドメイン名を IP アドレスにマップする DNS レコードを作成します。 詳細については、[API Management でのカスタム ドメイン名の構成](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain)に関するページを参照してください。
- **Azure Active Directory (Azure AD)**。 認証には、[Azure AD](https://docs.microsoft.com/azure/active-directory/) または他の ID プロバイダーを使用します。 Azure AD は、[API Management 用の JSON Web トークン](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims)を渡して検証することで、API エンドポイントにアクセスするための認証を提供します。 Azure AD では、API Management 開発者ポータルへのアクセスをセキュリティで保護することができます (Standard および Premium レベル限定)。

このアーキテクチャには、操作するためのいくつかの基本パターンがあります。

- 複合 API はロジック アプリを使用して構築されます。 これらは、サービス (SaaS) システムとしてのソフトウェア、Azure サービス、および API Management に公開された任意の API の呼び出しを調整します。 [ロジック アプリは API Management 開発者ポータル経由でも公開されます](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api)。
- アプリケーションでは、Azure AD を使用して API へのアクセスを獲得するために必要な [OAuth 2.0 セキュリティ トークン](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)を取得します。
- Azure API Management は[セキュリティ トークンを検証](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)してから、バックエンドの API またはロジック アプリに要求を渡します。

## <a name="recommendations"></a>Recommendations

この記事で説明されている一般的なアーキテクチャとは異なる特定の要件がある場合があります。 このセクションに記載されている推奨事項は原案として使用してください。

### <a name="azure-api-management-tier"></a>Azure API Management のレベル

API Management の Basic、Standard、または Premium レベルを使用します。 これらのレベルでは、運用のサービス レベル アグリーメント (SLA) を提供し、Azure リージョン内でのスケールアウトをサポートしています (ユニット数はレベルに応じる)。 また、Premium レベルでは、複数の Azure リージョン間でのスケール アウトもサポートしています。 選択するレベルは、必要なスループットのレベルと機能セットに基づきます。 詳しくは、「[API Management の価格](https://azure.microsoft.com/pricing/details/api-management/)」を参照してください。

すべての API Management インスタンスは、実行された場合に課金されます。 スケールアップしても、常にそのパフォーマンス レベルを必要としない場合は、API Management の時間単位の課金を利用して、スケールダウンすることを検討してください。

### <a name="logic-apps-pricing"></a>Logic Apps の価格

Logic Apps では、[サーバーレス](logic-apps-serverless-overview.md) モデルを使用します。 課金はアクションとコネクタの実行に基づいて計算されます。 詳細については、「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)」をご覧ください。 現在、Logic Apps には、レベルに関する考慮事項はありません。

### <a name="logic-apps-for-asynchronous-api-calls"></a>非同期 API 呼び出しの Logic Apps

Logic Apps は、低待機時間を必要としないシナリオで最適に機能します。 たとえば、非同期または幾分実行時間が長い API 呼び出しで最適に機能します。 低待機時間が必要になる場合は (ユーザー インターフェイスをブロックする呼び出しなど)、異なるテクノロジを利用して該当する API または操作を実装することをお勧めします。 たとえば、Azure App Service を使用してデプロイされた Azure Functions または Web API を使用します。 その上で、API コンシューマーに対しては API Management を使って、この API に対応することをお勧めします。

### <a name="region"></a>リージョン

API Management および Logic Apps を同じリージョンにプロビジョニングして、ネットワーク待機時間を最小限に抑えます。 通常は、ユーザーに最も近いリージョンを選択します。

リソース グループにもリージョンがあります。 このリージョンによって、メタデータのデプロイの格納場所と、テンプレートのデプロイの実行元の場所が指定されます。 デプロイ時の可用性を向上させるには、リソース グループとそのリソースを同じリージョンに配置します。

## <a name="scalability"></a>スケーラビリティ

API Management 管理者は、サービスのスケーラビリティの向上に適した[キャッシュ ポリシー](../api-management/api-management-howto-cache.md)を追加する必要があります。 また、キャッシュはバックエンド サービスに対する負荷を軽減するのに役立ちます。

Azure API Management の Basic、Standard および Premium レベルは、Azure リージョン内でより大きな容量を提供するために、スケールアウトできます。 管理者は、**[メトリック]** メニュー内にある **[容量メトリック]** オプションを使用してサービスの使用状況を分析し、適切にスケールアップまたはスケールダウンできます。

API Management サービスのスケーリングに関する推奨事項を次に示します。

- スケーリングでは、トラフィック パターンについて考慮する必要があります。 変化の大きなトラフィック パターンを利用している顧客の場合は、容量増大の必要性が高まります。
- 容量が一貫して 66% を上回る場合は、スケールアップの必要があることを示している可能性があります。
- 容量が一貫して 20% を下回る場合は、スケールダウンの機会であることを示している可能性があります。
- 運用環境で負荷を有効にする前に、想定される負荷でお使いの API Management サービスのロードテストを行うことが常に推奨されます。

Premium レベルのサービスは、複数の Azure リージョン間でスケールアウトできます。 複数の Azure リージョン間でサービスをスケーリングすることによってデプロイしている顧客は、より高い SLA (99.9% に対して 99.95%) を獲得し、複数のリージョンにいるユーザーに近いサービスをプロビジョニングできます。

Logic Apps サーバーレス モデルは、管理者がサービスのスケーラビリティについて計画を立てる必要がないことを意味します。 サービスは、需要に合わせて自動的にスケーリングされます。

## <a name="availability"></a>可用性

現在、Azure API Management の SLA は、Basic、Standard、および Premium レベルで 99.9% です。 2 つ以上のリージョンに少なくとも 1 つのユニットをデプロイする Premium レベルの構成では、SLA が 99.95% になります。

現在、Azure Logic Apps の SLA は 99.9% です。

### <a name="backups"></a>バックアップ

Azure API Management の構成は、[定期的にバックアップする](../api-management/api-management-howto-disaster-recovery-backup-restore.md)必要があります (定期的な変更に基づいて)。 バックアップ ファイルは任意の 1 か所、またはサービスが配置されている場所とは異なる Azure リージョンに格納する必要があります。 顧客は、次に示す 2 つのディザスター リカバリー戦略のいずれかを選択できます。

- ディザスター リカバリー イベントでは、新しい API Management インスタンスがプロビジョニングされ、バックアップがこの新しいインスタンスに復元されて、DNS レコードが補完されます。
- 顧客は、別の Azure リージョンにサービスのパッシブ コピーを保持します (追加コストが発生します)。 バックアップがそのコピーに定期的に復元されます。 ディザスター リカバリー イベントでは、サービスを復元するために DNS レコードのみが補完される必要があります。

ロジック アプリは迅速に再作成することができ、しかもサーバーレスなので、関連する Azure Resource Manager テンプレートのコピーを保存することによりバックアップされます。 テンプレートは、ソース コントロールに保存できると共に、顧客の継続的インテグレーション/継続的デプロイ (CI/CD) プロセスに統合できます。

API Management を通じて公開されたロジック アプリが別のデータ センターに移動された場合は、アプリの場所を更新してください。 アプリの場所を更新するには、API の **Backend** プロパティを更新する基本的な PowerShell スクリプトを使用します。

## <a name="manageability"></a>管理容易性

運用、開発、およびテスト環境それぞれに対して個別のリソース グループを作成してください。 個別のリソース グループを使用することにより、デプロイの管理、テスト デプロイの削除、およびアクセス権の割り当てが行いやすくなります。

リソースをリソース グループに割り当てるときは、次の要素を検討してください。

- **ライフサイクル**。 一般的に、同じライフサイクルのリソースは、同じリソース グループに配置します。
- **アクセス**。 [ロールベースのアクセス制御](../role-based-access-control/overview.md) (RBAC) を使用して、アクセス ポリシーをグループ内のリソースに適用できます。
- **課金**。 リソース グループのロールアップ コストを表示できます。
- **API Management の価格レベル**。 開発環境およびテスト環境には、Developer レベルを使用することをお勧めします。 運用前環境には、本番環境のレプリカをデプロイしてテストを実行し、コストを最小限に抑えることをお勧めします。

詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。

### <a name="deployment"></a>Deployment

[Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md)を使用して、API Management および Logic Apps をデプロイすることをお勧めします。 テンプレートにより、PowerShell または Azure CLI を使用したデプロイが自動化しやすくなります。

Azure API Management と個々の任意のロジック アプリを別々の独自の Resource Manager テンプレートに配置することをお勧めします。 別々のテンプレートを使用すると、ソース管理システムでリソースを格納できます。 リソースは一緒にデプロイすることも、CI/CD デプロイ プロセスの一部として個別にデプロイすることもできます。

### <a name="versions"></a>バージョン

ロジック アプリに構成の変更を加える (または、Resource Manager テンプレートを使って更新プログラムをデプロイする) たびに、便宜上、該当のバージョンのコピーが保管されます (実行履歴のあるすべてのバージョンが保管されます)。 これらのバージョンを使用して変更履歴を追跡できると共に、ロジック アプリの現在の構成になるようにバージョンを昇格することができます。 たとえば、ロジック アプリを効果的にロールバックすることができます。

API Management には、2 つの異なる (ただし補完的な) [バージョンの概念](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/)があります。

- API コンシューマーにニーズに基づいて利用できる API の選択肢を提供するために、使用されるバージョン (v1、v2、ベータ、運用など)。
- API 管理者によ API への安全な変更と、オプション注釈付きでのユーザーへの変更のデプロイを可能にするリビジョン。

デプロイの際には、安全に変更を加え、変更履歴を保持し、API コンシューマーに変更を通知する手段として、API Management のリビジョンを検討することをお勧めします。 リビジョンは開発環境で作成でき、Resource Manager テンプレートを使って他の環境との間でデプロイできます。

API が "現在" の状態になってユーザーが利用可能になる前に、リビジョンを使用して API をテストすることはできますが、負荷テストまたは統合テストにこのメカニズムを使用することはお勧めしません。 代わりに、異なるテスト環境または運用前環境を使用してください。

### <a name="configuration"></a>構成

パスワード、アクセス キー、または接続文字列をソース管理にチェックインしないでください。 これらの値が必要な場合は、適切な手段を使用して、これらの値をデプロイしてセキュリティで保護してください。 

Logic Apps では、ロジック アプリ内で必要な (接続の形式では作成できない) 機密の値はすべて、Azure Key Vault に格納され、Resource Manager テンプレートから参照される必要があります。 また、デプロイ テンプレートのパラメーターと、各環境のパラメーター ファイルを使用することをお勧めします。 詳細については、「[ワークフロー内のパラメーターと入力のセキュリティ保護](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow)」を参照してください。

API Management で、機密情報は "*名前付きの値*" または "*プロパティ*" というオブジェクトを使用して管理されます。 これらのオブジェクトでは、アクセス可能な値が API Management ポリシーで安全に格納されます。 詳細については、[API Management での機密情報の管理](../api-management/api-management-howto-properties.md)に関するページを参照してください。

### <a name="diagnostics-and-monitoring"></a>診断および監視

[API Management](../api-management/api-management-howto-use-azure-monitor.md) および [Logic Apps](logic-apps-monitor-your-logic-apps.md) は両方とも、[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) を使った操作の監視がサポートされています。 Azure Monitor は、各サービスに構成されているメトリックに基づいて情報を提供します。 Azure Monitor は、既定で有効になっています。

各サービスでは次のオプションも使用できます。

- Logic Apps ログは、より詳細な分析およびダッシュボード化のために、[Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) に送信できます。
- API Management では、DevOps 監視のための Azure Application Insights の構成をサポートしています。
- API Management では、[カスタム API 分析のための Power BI ソリューション テンプレート](http://aka.ms/apimpbi)をサポートしています。 顧客はソリューション テンプレートを使用して、独自のカスタム分析ソリューションを作成できます。 レポートはビジネス ユーザー向けの Power BI で利用できます。

## <a name="security"></a>セキュリティ

このセクションでは、この記事で説明した Azure サービスで、説明に従ってアーキテクチャにデプロイされる Azure サービスに固有のセキュリティ上の考慮事項を示します。 これはセキュリティ上のベスト プラクティスを網羅した一覧ではありません。

- ロール ベースのアクセス制御 (RBAC) を使用して、適切なレベルでのユーザーへのアクセスを保証する。
- OAuth/OpenID Connect を使用して、API Management にあるパブリック API エンドポイントをセキュリティで保護する。 パブリック API エンドポイントをセキュリティで保護するには、ID プロバイダーを構成し、JSON Web トークン (JWT) 検証ポリシーを追加します。
- 相互証明書を使用して、API Management からバックエンド サービスに接続する。
- API Management の IP アドレスを参照する IP アドレス ホワイトリストを作成することで HTTP トリガーベースのロジック アプリをセキュリティで保護する。 ホワイトリスト IP アドレスにより、最初に API Management を経由せずに、パブリック インターネットからロジック アプリが呼び出されることを回避します。

## <a name="next-steps"></a>次の手順

- [キューとイベントによるエンタープライズ統合](logic-apps-architectures-enterprise-integration-with-queues-events.md)について説明します。
