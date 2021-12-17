---
title: Azure API Management での収益化のサポート
description: Azure API Management により、API 製品の収益化戦略をサポートする方法について説明します。
author: dlepow
ms.author: danlep
ms.date: 08/23/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: ad6e4358ebd312c498504da6650045d9019fadbe
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548715"
---
# <a name="how-api-management-supports-monetization"></a>API Management での収益化のサポート方法

[Azure API Management](./api-management-key-concepts.md) サービス プラットフォームを使用すると、以下が可能になります。
* コンシューマーがサブスクライブする API を発行します。
* 実装のリスクを軽減します。 
* プロジェクトのタイムスケールを加速します。
* 自信を持って API をスケーリングします。

このドキュメントでは、収益化戦略の導入を可能にする API Management の機能について説明します。これには、以下に対する摩擦のないエクスペリエンスの提供が含まれます。
* パブリック API を検出する。
* 支払いの詳細を入力する。
* サブスクリプションをアクティブ化する。
* API を使用する。
* 使用状況を監視する。
* API の使用に対して自動的に支払いを行う。

次の図は、こうした主な API Management の機能を示しています。

:::image type="content" source="media/monetization-support/architecture-overview.png" alt-text="主な API Management 収益化機能の図":::

## <a name="api-discovery"></a>API の検出

API Management 組み込みの開発者ポータルを使用して API を起動し、API コンシューマーをオンボードします。 開発者ポータルの高品質な開発コンテンツを強調することで、API コンシューマーが API をシームレスに探索して使用できるようにします。 提供されるコンテンツと情報のアクセシビリティ、周到さ、使いやすさをテストします。

コンテンツを追加し、開発者ポータルのブランドをコントロールする方法の詳細については、「[開発者ポータルの概要](./api-management-howto-developer-portal.md)」をご覧ください。

## <a name="api-packaging"></a>API のパッケージ化

API Management は、*製品* と *ポリシー* の概念を使用して、API がパッケージ化および提供される方法を管理します。

### <a name="products"></a>製品

API は、[製品を通じて](./api-management-howto-add-products.md)発行されます。 製品では、以下を定義できます。
* サブスクライバーがアクセスできる API。
* 特定のサブスクリプションを 1 か月あたりの呼び出しのクォータにより制限するなどの、特定の調整[ポリシー](./api-management-howto-policies.md)。

製品をサブスクライブした API コンシューマーは API キーを受け取り、それを使用して呼び出しを行います。 最初、サブスクリプションは `submitted` 状態に設定されています。 サブスクリプションをアクティブ化して、サブスクライバーが API を使用できるようにします。

以下により、API Management 製品を構成して、収益モデルを反映するように基になる API をパッケージ化します。
* 収益モデルの各層間の 1 対 1 のリレーションシップ。
* 対応する API Management 製品。

プロジェクトの例では、収益化戦略を体系化するための最上位レベルの手段として、API Management 製品を使用します。 API Management 製品は収益モデル層を反映し、各層の特定の価格モデルにインデックスを付けます。 このセットアップでは、収益化戦略を準備するための柔軟で構成に基づくアプローチが提供されます。

### <a name="policies"></a>ポリシー

各製品のサービスの品質を制御するための API Management ポリシーを適用します。 プロジェクトの例では、収益モデルに合わせて以下の 2 つの特定のポリシー機能を使用して、サービスの品質を制御します。

| ポリシー機能 | 説明 |
| ----- | ----- |
| **[クォータ]** | 指定した期間にユーザーが API に対して実行できる呼び出しの総数を規定します。 たとえば、"1 か月あたり 100 回の呼び出し" などです。 ユーザーがクォータに達すると、API の呼び出しは失敗し、呼び出し元は `403 Forbidden` の応答状態コードを受け取ります。 |
| **レート制限** | 移動する時間枠内で API に対して実行できる呼び出しの数を規定します。 たとえば、"1 分あたり 200 回の呼び出し" などです。 選択した製品で支払い済みのサービス品質を超えて、API の使用が急増するのを防ぐ目的で設計されています。 この呼び出しレートを超えると、呼び出し元は `429 Too Many Requests` 応答状態コードを受信します。 |

ポリシーについて詳しくは、「[Azure API Management のポリシー](./api-management-howto-policies.md)」ドキュメントをご覧ください。

## <a name="api-consumption"></a>API の使用

API サブスクリプションを使用して、API コンシューマーに製品を介して API へのアクセス権を付与します。

1. API コンシューマーは、特定の API Management 製品にサインアップするときに API サブスクリプションを開設します。 
1. API Management の委任を使用して、サブスクリプション プロセスを支払プロバイダーに統合します。 
1. 支払いの詳細が正常に提供されると、ユーザーはサブスクリプションに対して生成された一意のセキュリティ キーを使用して API にアクセスできます。

サブスクリプションの詳細については、「[Azure API Management のサブスクリプション](./api-management-subscriptions.md)」ドキュメントをご覧ください。

## <a name="api-usage-monitoring"></a>API 使用の監視

API の使用状況とパフォーマンスに関する分析情報は、API Management の組み込み分析を使用して得ることができます。 これらの分析は、以下の内容でレポートを提供します。
* API
* [地理的な場所]
* API 操作
* 製品
* Request
* サブスクリプション
* Time 
* User

分析レポートを定期的に確認して、収益化戦略が API コンシューマーによってどのように採用されているのかを理解します。

詳細については、「[Azure API Management で API 分析を取得する](./howto-use-analytics.md)」をご覧ください。

## <a name="security"></a>セキュリティ

API Management の製品、API ポリシー、サブスクリプションを使用して、各ユーザーと各製品のアクセス レベルを制御します。 特定の API 製品が無料である場合でも、ユーザーが支払プロバイダーによって正常に認証された場合にサブスクリプション レベルの API アクセスを許可することで、誤用や不正使用を防ぎます。

## <a name="integration"></a>統合

API Management と選択した支払プロバイダーの間のフロントエンドとバック エンドの両方の統合を通じて、シームレスな収益化エクスペリエンスを構築します。  フロントエンドの統合に API Management の委任を使用し、バックエンドの統合には REST API を使用します。

### <a name="delegation"></a>委任

プロジェクトの例では、[API Management の委任](./api-management-howto-setup-delegation.md)を使用してサードパーティの支払プロバイダーとのカスタム統合を行うことができます。 このデモでは、サインアップ/サインインと製品サブスクリプションの両方のエクスペリエンスに委任を使用します。

#### <a name="sign-upsign-in-workflow"></a>サインアップ/サインイン ワークフロー

1. 開発者が、API Management 開発者ポータルのサインインまたはサインアップ リンクをクリックします。
1. ブラウザーは委任エンドポイントにリダイレクトされます (カスタム課金ポータル アプリのページに設定されています)。
1. カスタム課金ポータル アプリで、サインイン/サインアップ UI が表示されます。
1. サインイン/サインアップが成功すると、ユーザーは認証され、最初の API Management 開発者ポータル ページにリダイレクトされて戻ります。

#### <a name="product-subscription-workflow"></a>製品サブスクリプションのワークフロー

1. 開発者が API Management 開発者ポータルで製品を選択し、 **[サブスクライブ]** ボタンをクリックします。
1. ブラウザーは委任エンドポイントにリダイレクトされます (カスタム課金ポータル アプリのページに設定されています)。
1. カスタム課金ポータル アプリで、以下が行われます。
    * 支払プロバイダー (Stripe または Adyen) に基づいて構成された UI を表示します。
    * ユーザーに該当する精算プロセスを案内します。
1. ユーザーは最初の API Management 製品ページにリダイレクトされて戻ります。 
    * 製品はアクティブとなり、API キーを使用できるようになります。

### <a name="rest-api"></a>REST API

収益化戦略の運用を自動化するには、API Management 用 REST API を使用します。

サンプル プロジェクトでは、API を使用して以下をプログラム的に実行します。

- API Management 製品およびポリシーを取得して、Stripe などの支払プロバイダーにおける同様の概念を同期的に構成できるようにします。
- API Management を定期的にポーリングして、各サブスクリプションの API 使用状況のメトリックを取得し、課金プロセスを開始します。

詳しくは、[REST API Azure API Management](/rest/api/apimanagement/#rest-operation-groups) の概要に関するページをご覧ください。

## <a name="devops"></a>DevOps

Azure Resource Manager を使用して、API Management に対するデプロイの変更の自動化とバージョン管理を行います。収益化戦略を実装する、以下のような機能の構成などが含まれます。
* 製品
* ポリシー
* 開発者ポータル

プロジェクトの例では、Azure Resource Manager スクリプトは JSON ファイルによって拡張されており、各 API Management 製品の価格モデルが定義されています。 この拡張により、API Management と選択した支払プロバイダーの間の構成を同期できます。 ソリューション全体は、単一のソース管理リポジトリで管理され、以下を可能にします。
* 進行中の収益化戦略の進化に関連するすべての変更を 1 回のリリースとして調整します。
* ガバナンスと監査の要件に従って変更を実施します。

## <a name="initialization-and-deployment"></a>初期化とデプロイ

API Management は、次のいずれかを使用してデプロイできます。
* Azure portal UI。
* [Azure Resource Manager テンプレート](https://azure.microsoft.com/services/arm-templates)を使用した "コードとしてのインフラストラクチャ" アプローチ。 

## <a name="next-steps"></a>次のステップ

* [API Management 収益化戦略の詳細についてご確認ください](monetization-overview.md)。
* 関連する [Git リポジトリ](https://github.com/microsoft/azure-api-management-monetization)を使用して、Adyen または Stripe 統合のデモをデプロイします。