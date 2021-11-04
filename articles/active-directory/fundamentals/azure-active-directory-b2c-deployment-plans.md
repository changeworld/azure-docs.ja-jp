---
title: Azure AD B2C のデプロイ
description: Azure Active Directory B2C のデプロイ ガイド
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 7/12/2021
ms.author: gasinh
author: gargi-sinha
manager: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9935fb243b6c2824633e67ac1eed26a80b67bc9d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068134"
---
# <a name="azure-active-directory-b2c-deployment-plans"></a>Azure Active Directory B2C のデプロイ計画

Azure Active Directory B2C は、スケーラブルな ID およびアクセス管理ソリューションです。 ビジネス上の期待に応える高い柔軟性と、既存のインフラストラクチャとのスムーズな統合により、さらなるデジタル化が可能になります。

組織がビジネス要件を理解し、コンプライアンス境界を尊重できるようにするには、Azure Active Directory (Azure AD) B2C デプロイの全体を通じて段階的なアプローチをお勧めします。

| 機能 | 説明 |
|:-----|:------|
| [プラン](#plan-an-azure-ad-b2c-deployment) | Azure AD B2C プロジェクトをデプロイに向けて準備します。 まず、関係者を特定し、後でプロジェクトのタイムラインを定義します。 |
| [実装](#implement-an-azure-ad-b2c-deployment) | 最初に認証と認可を有効にし、後で完全なアプリケーションのオンボードを実行します。 |
| [監視](#monitor-an-azure-ad-b2c-solution) | Azure AD B2C ソリューションが導入されたら、ログ、監査、レポートを有効にします。 |

## <a name="plan-an-azure-ad-b2c-deployment"></a>Azure AD B2C のデプロイを計画する

このフェーズには以下の機能が含まれています。

| 機能 | 説明 |
|:------------|:------------|
|[ビジネス要件レビュー](#business-requirements-review) | 組織の状態と期待事項を評価する |
| [利害関係者](#stakeholders) |プロジェクト チームを作る |
|[通信](#communication) | プロジェクトについてチームと連絡を取る |
| [タイムライン](#timeline) | 主要なプロジェクト マイルストーンのリマインダー  |

### <a name="business-requirements-review"></a>ビジネス要件レビュー

- 既存のシステムをオフにして [Azure AD B2C](../../active-directory-b2c/overview.md) に移行する主な理由を評価します。

- 新しいアプリケーションの場合は、顧客 ID アクセス管理 (CIAM) システムを[計画して設計](../../active-directory-b2c/best-practices.md#planning-and-design)します

- 顧客の場所を特定し、[対応するデータセンターにテナントを作成](../../active-directory-b2c/tutorial-create-tenant.md)します。

- 使用しているアプリケーションの種類を確認する
  - 現在サポートされているプラットフォーム ([MSAL](../develop/msal-overview.md) または[オープンソース](https://azure.microsoft.com/free/open-source/search/?OCID=AID2200277_SEM_f63bcafc4d5f1d7378bfaa2085b249f9:G:s&ef_id=f63bcafc4d5f1d7378bfaa2085b249f9:G:s&msclkid=f63bcafc4d5f1d7378bfaa2085b249f9)) を確認します。
  - バックエンド サービスには、[クライアント資格情報フロー](../develop/msal-authentication-flows.md#client-credentials)を使用します。

- 既存の ID プロバイダー (IdP) から移行する場合

  - [シームレスな移行方法](../../active-directory-b2c/user-migration.md#seamless-migration)の使用を検討する
  - [既存のアプリケーションを移行する方法](https://github.com/azure-ad-b2c/user-migration)について学習する
  - 一度に複数のソリューションを共存させます。

- 使用するプロトコルを決定する

  - 現在 Kerberos、NTLM、WS-Fed を使用している場合は、[アプリケーションを移行してリファクタリングします](https://www.bing.com/videos/search?q=application+migration+in+azure+ad+b2c&docid=608034225244808069&mid=E21B87D02347A8260128E21B87D02347A8260128&view=detail&FORM=VIRE)。 移行後、アプリケーションで OAuth 2.0 や OpenID Connect (OIDC) などの最新の ID プロトコルをサポートして、ID の保護とセキュリティをさらに強化できます。

### <a name="stakeholders"></a>利害関係者

テクノロジ プロジェクトが失敗した場合、その原因は通常、影響、結果、および責任に対する想定の不一致です。 これらの落とし穴を回避するには、[適切な利害関係者が担当していることを確認](./active-directory-deployment-plans.md#include-the-right-stakeholders)し、その利害関係者が自分のロールを理解していることを確認します。

- アプリケーションのプライマリ アーキテクト、プロジェクト マネージャー、所有者を特定します。

- 配布リスト (DL) の提供を検討します。 この DL を使用して、製品に関する問題を Microsoft アカウント チームまたはエンジニアリング部門に連絡できます。 質問したり、重要な通知を受け取ったりできます。

- サポートを受けることができる組織外のパートナーまたはリソースを特定します。

### <a name="communication"></a>コミュニケーション

コミュニケーションは、新しいサービスの成功に必要不可欠です。 変化についてユーザーに事前に連絡します。 彼らのエクスペリエンスがどのように変わるのか、いつ変わるのか、また問題が発生したときにサポートを受ける方法について、タイムリーに通知します。

### <a name="timeline"></a>タイムライン

主要なマイルストーンを満たすために明確な予想とフォローアップ計画を定義します。

- 予想されるパイロット日

- 予想される運用開始日

- プロジェクトのデリバリー日に影響する可能性がある日付

## <a name="implement-an-azure-ad-b2c-deployment"></a>Azure AD B2C のデプロイを実装する

このフェーズには以下の機能が含まれています。

| 機能 | 説明 |
|:-------------|:--------------|
| [認証と認可をデプロイする](#deploy-authentication-and-authorization) | [認証と認可](../develop/authentication-vs-authorization.md)のシナリオを理解する |
| [アプリケーションとユーザー ID をデプロイする](#deploy-applications-and-user-identities) | クライアント アプリケーションのデプロイとユーザー ID の移行を計画する  |
| [クライアント アプリケーションのオンボードと成果物](#client-application-onboarding-and-deliverables) | クライアント アプリケーションをオンボードしてソリューションをテストする |
| [Security](#security) | ID ソリューションのセキュリティを強化する |
|[コンプライアンス](#compliance) | 規制要件に対応する |
|[ユーザー エクスペリエンス](#user-experience) | ユーザーフレンドリなサービスを実現する |

### <a name="deploy-authentication-and-authorization"></a>認証と認可をデプロイする

- まず、[Azure AD B2C テナントを設定](../../active-directory-b2c/tutorial-create-tenant.md)します。

- ビジネス駆動型の認可の場合は、[Azure AD B2C Identity Experience Framework (IEF) サンプル ユーザー体験](https://github.com/azure-ad-b2c/samples#local-account-policy-enhancements)を使用します

- [Open Policy Agent](https://www.openpolicyagent.org/) を試してみてください。

Azure AD B2C の詳細については、[こちらの開発者向けコース](https://aka.ms/learnaadb2c)を参照してください。

その他のガイダンスについては、このサンプル チェックリストに従ってください。

- アプリケーションへのアクセスが必要なさまざまなペルソナを特定します。  

- 既存のシステムで現在どのようにアクセス許可とエンタイトルメントを管理しているか、および将来に向けてどのように計画を立てるかを定義します。

- アクセス許可ストアが存在するかどうか、およびディレクトリに追加する必要があるアクセス許可が存在するかどうかを確認します。

- 委任された管理が必要な場合は、それを解決する方法を定義します。 たとえば、ご自身のお客様の顧客管理などです。

- アプリケーションで API Manager (APIM) を直接呼び出すかどうかを確認します。 アプリケーションにトークンを発行する前に、IdP から呼び出すことが必要な場合があります。

### <a name="deploy-applications-and-user-identities"></a>アプリケーションとユーザー ID をデプロイする

すべての Azure AD B2C プロジェクトは、ビジネス目標が異なる場合がある 1 つ以上のクライアント アプリケーションから始まります。

1. [クライアント アプリケーションを作成または構成](../../active-directory-b2c/app-registrations-training-guide.md)します。 実装については、こちらの[コード サンプル](../../active-directory-b2c/integrate-with-app-code-samples.md)を参照してください。

2. 次に、組み込みまたはカスタムのユーザー フローに基づいてユーザー体験を設定します。 [ユーザー フローとカスタム ポリシーを使用するタイミングについて確認してください](../../active-directory-b2c/user-flow-overview.md#comparing-user-flows-and-custom-policies)。

3. ビジネス ニーズに基づいて IdP を設定します。 [IdP として Azure Active Directory B2C を追加する方法についてご確認ください](../../active-directory-b2c/add-identity-provider.md)。

4. データを移行します。 [ユーザーの移行方法についてご確認ください](../../active-directory-b2c/user-migration.md)。 高度なシナリオについては、[Azure AD B2C IEF サンプル ユーザー体験](https://github.com/azure-ad-b2c/samples)を参照してください。  

**アプリケーションをデプロイ** する際には、このサンプル チェックリストを考慮してください。

- CIAM デプロイのスコープ内にあるアプリケーションの数を確認します。

- 使用しているアプリケーションの種類を確認します。 たとえば、従来の Web アプリケーション、API、シングルページ アプリ (SPA)、ネイティブ モバイル アプリケーションなどです。

- 設定されている認証の種類を確認します。 たとえば、フォーム ベース、SAML とのフェデレーション、OIDC とのフェデレーションなどです。
  - OIDC の場合は、応答の種類 (コードまたは id_token) を確認します。

- フロントエンドとバックエンドのすべてのアプリケーションが、オンプレミス、クラウド、ハイブリッド クラウドのいずれでホストされているかを確認します。

- [ASP.NET](../../active-directory-b2c/quickstart-web-app-dotnet.md)、Java、Node.js など、使用されるプラットフォームと言語を確認します。

- 現在のユーザー属性が格納されている場所を確認します。 ライトウェイト ディレクトリ アクセス プロトコル (LDAP) またはデータベースの場合があります。

**ユーザー ID をデプロイ** する際には、このサンプル チェックリストを考慮してください。

- アプリケーションにアクセスするユーザーの数を確認します。

- 必要な IdP の種類を確認します。 たとえば、Facebook、ローカル アカウント、[Active Directory フェデレーション サービス (AD FS)](/windows-server/identity/active-directory-federation-services) などです。

- アプリケーション、[Azure AD B2C](../../active-directory-b2c/claimsschema.md)、および該当する場合は IdP に必要な要求スキーマの概要を指定します。

- [サインインまたはサインアップ フロー](../../active-directory-b2c/add-sign-up-and-sign-in-policy.md?pivots=b2c-user-flow)中に取得する必要がある情報の概要を指定します。

### <a name="client-application-onboarding-and-deliverables"></a>クライアント アプリケーションのオンボードと成果物

**アプリケーションをオンボードする** 際は、このサンプル チェックリストを考慮してください。

|  タスク | 説明 |
|:--------------------|:----------------------|
| アプリケーションのターゲット グループを定義する | このアプリケーションがエンド カスタマー アプリケーション、ビジネス カスタマー アプリケーション、デジタル サービスのいずれであるかを確認します。 従業員のログインが必要かどうかを確認します。 |
| アプリケーションの背後にあるビジネス価値を特定する | アプリケーションの背後にある詳細なビジネス ケースを理解して、Azure AD B2C ソリューションと他のクライアント アプリケーションとの統合に最適なソリューションを見つけます。|
| 使用している ID グループを確認する | 異なる種類の要件を持つさまざまな種類のグループのクラスター ID。たとえば、エンド カスタマーとビジネス カスタマー向け **Business to Customer** (B2C)、パートナーとサプライヤー向け **Business to Business** (B2B)、従業員と外部従業員向け **Business to Employee** (B2E)、IoT デバイス ログインとサービス アカウント用の **Business to Machine** (B2M) などです。|
| ビジネス ニーズとプロセスに必要な IdP を確認する | Azure AD B2C で[複数の種類の IdP](../../active-directory-b2c/add-identity-provider.md#select-an-identity-provider) がサポートされています。ユース ケースに応じて適切な IdP を選択する必要があります。 たとえば、Customer to Customer のモバイル アプリケーションには、高速で簡単なユーザー ログインが必要です。 別のユース ケースでは、デジタル サービスを使用する Business to Customer に追加のコンプライアンス要件が必要です。 ユーザーは、電子メール ログインなどのビジネス ID でログインすることが必要な場合があります。 |
| 規制上の制約を確認する | リモート プロファイルまたは特定のプライバシー ポリシーを使用する理由が何かあるか確認します。  |
| サインインとサインアップのフローを設計する | 電子メールの検証またはサインアップ内での電子メールの検証が必要かどうかを決定します。 Shop システムなどの最初のチェックアウト プロセスまたは [Azure AD 多要素認証 (MFA)](../authentication/concept-mfa-howitworks.md) が必要かどうか。 [こちらのビデオ](https://www.youtube.com/watch?v=c8rN1ZaR7wk&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=4)をご覧ください。 |
| 使用されている、または実装されるアプリケーションと認証プロトコルの種類を確認する | Web アプリケーション、SPA、ネイティブ アプリケーションなどのクライアント アプリケーションの実装に関する情報交換。 クライアント アプリケーションと Azure AD B2C 用の認証プロトコルは、OAuth、OIDC、SAML などです。 [こちらのビデオ](https://www.youtube.com/watch?v=r2TIVBCm7v4&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=9)をご覧ください|
| ユーザーの移行を計画する | [Azure AD B2C を使用したユーザー移行](../../active-directory-b2c/user-migration.md)の可能性を話し合います。 Just In Times (JIT) の移行、一括インポートとエクスポートなど、いくつかのシナリオが可能です。 [こちらのビデオ](https://www.youtube.com/watch?v=lCWR6PGUgz0&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=2)をご覧ください。 ユーザーの移行に [Microsoft Graph API](https://www.youtube.com/watch?v=9BRXBtkBzL4&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=3) を使用することも検討できます。|

**デリバリー** の際には、このサンプル チェックリストを考慮してください。

| 機能 | 説明 |
|:-----|:-------|
|プロトコル情報| 両方のバリアントの基本パス、ポリシー、メタデータ URL を収集します。 クライアント アプリケーションに応じて、サンプル ログイン、クライアント アプリケーション ID、シークレット、リダイレクトなどの属性を指定します。|
| アプリケーション サンプル | 提供されている[サンプル コード](../../active-directory-b2c/integrate-with-app-code-samples.md)を参照してください。 |
|侵入テスト | テストの前に、運用チームに侵入テストについて通知し、OAuth 実装を含むすべてのユーザー フローをテストします。 [侵入テスト](../../security/fundamentals/pen-testing.md)、および [Microsoft Cloud 統合侵入テストの活動規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement)の詳細をご確認ください。
| 単体テスト  | [リソース所有者パスワード資格情報 (ROPC) フローを使用](../develop/v2-oauth-ropc.md)して、単体テストを実行し、トークンを生成します。 Azure AD B2C トークンの上限に達した場合は、[サポート チームにお問い合わせください](../../active-directory-b2c/support-options.md)。 トークンを再利用して、インフラストラクチャに関する調査作業を減らします。 [ROPC フローを設定](../../active-directory-b2c/add-ropc-policy.md?pivots=b2c-user-flow&tabs=app-reg-ga)します。|
| ロード テスト | Azure AD B2C [サービスの制限](../../active-directory-b2c/service-limits.md)に達することを予想します。 サービスで発生する 1 か月あたりの予想される認証数を評価します。 予想される 1 か月あたりの平均ユーザー ログイン数を評価します。 予想される高負荷のトラフィック期間と、休日、移行、イベントなどのビジネス上の理由を評価します。 予想されるピーク時のサインアップ率 (1 秒あたりの要求数など) を評価します。 MFA で予想されるピーク トラフィック レート (1 秒あたりの要求数など) を評価します。 予想されるトラフィックの地理的分布とそのピーク レートを評価します。

### <a name="security"></a>セキュリティ

ビジネス ニーズに応じてアプリケーションのセキュリティを強化するために、このサンプル チェックリストを考慮してください。

- [MFA](../authentication/concept-mfa-howitworks.md) などの強力な認証方法が必要かどうかを確認します。 高価値のトランザクションやその他のリスク イベントをトリガーするユーザーに対しては、MFA の使用をお勧めします。 たとえば、銀行および金融アプリケーション、オンライン ショップ (最初のチェックアウト プロセス) の場合。

- MFA が必要かどうかを確認し、SMS または電話、電子メール、サード パーティのサービスなど、[MFA を行うために使用できる方法を確認](../authentication/concept-authentication-methods.md)します。

- 現在、アプリケーションでボット対策メカニズムが使用されているかどうかを確認します。

- 不正なアカウントとログインの作成が試みられるリスクを評価します。[Microsoft Dynamics 365 Fraud Protection](../../active-directory-b2c/partner-dynamics-365-fraud-protection.md) を使用して、新しい偽のアカウントを作成したり、既存のアカウントを侵害したりしようとする疑わしい試行をブロックまたはチャレンジ処理します。  

- アプリケーションでアカウントのサインインまたはサインアップの一部として適用する必要がある特別な条件付きの状態があるかどうかを確認します。

>[!NOTE]
>[条件付きアクセス規則](../conditional-access/overview.md)を使用して、ビジネス目標に基づいてユーザー エクスペリエンスとセキュリティの差異を調整できます。

詳細については、「[Azure AD B2C における Identity Protection と条件付きアクセス](../../active-directory-b2c/conditional-access-identity-protection-overview.md)」を参照してください。

### <a name="compliance"></a>コンプライアンス

特定の規制要件を満たすために、vNet、IP 制限、Web Application Firewall (WAF)、および同様のサービスを使用して、バックエンド システムのセキュリティを強化することができます。

基本的なコンプライアンス要件に対処するには、次の点を考慮します。

- PCI-DSS など、サポートする必要がある特定の規制のコンプライアンス要件。

- 別のデータベース ストアにデータを格納する必要があるかどうかを確認します。 該当する場合は、この情報をディレクトリに書き込んではいけないかどうかを確認します。

### <a name="user-experience"></a>ユーザー エクスペリエンス

ユーザー エクスペリエンス (UX) の要件を定義するには、このサンプル チェックリストを考慮してください。

- [CIAM 機能を拡張し、シームレスなエンド ユーザー エクスペリエンスを構築](../../active-directory-b2c/partner-gallery.md)するために必要な統合を特定します。

- 既存のアプリケーションのエンド ユーザー エクスペリエンスを示すスクリーンショットとユーザー ストーリーを用意します。 たとえば、サインイン、サインアップ、サインアップとサインイン (SUSI) の組み合わせ、プロファイルの編集、パスワードのリセットのスクリーンショットを用意します。

- 現在の CIAM ソリューションで queryString パラメーターを使用して渡される既存のヒントを探します。

- ピクセルからピクセルなどの高度な UX カスタマイズが予想される場合は、フロントエンド開発者による支援が必要な場合があります。

## <a name="monitor-an-azure-ad-b2c-solution"></a>Azure AD B2C ソリューションを監視する

このフェーズには以下の機能が含まれています。

| 機能 | 説明 |
|:---------|:----------|
|  監視  |[Azure Monitor で Azure AD B2C を監視する](../../active-directory-b2c/azure-monitor.md)。 [こちらのビデオ](https://www.youtube.com/watch?v=Mu9GQy-CbXI&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=1)をご覧ください|
| 監査とログ記録 | [監査ログへのアクセスと確認](../../active-directory-b2c/view-audit-logs.md)

## <a name="more-information"></a>詳細情報

Azure AD B2C のデプロイを加速し、サービスを大規模に監視するには、次の記事を参照してください。

- [Microsoft Graph を使用して Azure AD B2C を管理する](../../active-directory-b2c/microsoft-graph-get-started.md)

- [Microsoft Graph を使用して Azure AD B2C ユーザー アカウントを管理する](../../active-directory-b2c/microsoft-graph-operations.md)

- [Azure Pipelines を使用してカスタム ポリシーをデプロイする](../../active-directory-b2c/deploy-custom-policies-devops.md)

- [Azure PowerShell を使用して Azure AD B2C のカスタム ポリシーを管理する](../../active-directory-b2c/manage-custom-policies-powershell.md)

- [Azure Monitor で Azure AD B2C を監視する](../../active-directory-b2c/azure-monitor.md)

## <a name="next-steps"></a>次のステップ

- [Azure AD B2C のベスト プラクティス](../../active-directory-b2c/best-practices.md)

- [Azure AD B2C サービスの制限](../../active-directory-b2c/service-limits.md)
