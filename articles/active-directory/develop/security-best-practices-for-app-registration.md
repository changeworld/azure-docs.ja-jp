---
title: Azure AD アプリケーションの登録構成のベスト プラクティス - Microsoft ID プラットフォーム
description: Azure AD アプリケーションの登録構成に関する一連のベスト プラクティスと一般的なガイダンスについて説明します。
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/8/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: saumadan, marsma
ms.openlocfilehash: cd03531e35d51bafeceb2df913e17fc4814e911c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050578"
---
# <a name="azure-ad-application-registration-security-best-practices"></a>Azure AD アプリケーションの登録に関するセキュリティのベスト プラクティス

Azure Active Directory (Azure AD) アプリケーションの登録は、ビジネス アプリケーションの重要な部分です。 アプリケーションの構成ミスやウイルス対策の欠如は、ダウンタイムや侵害につながる可能性があります。

アプリケーションの登録はその対象領域のためにビジネス アプリケーションよりも影響が広範囲に及ぶことを理解することが重要です。 アプリケーションに追加されているアクセス許可によっては、侵害されたアプリが組織全体に影響を与える可能性があります。
アプリケーションの登録はユーザーのログインを可能にするうえで不可欠であるため、そこでダウンタイムが発生すると、ビジネス自体またはビジネスが依存する重要なサービスが影響を受ける可能性があります。 したがって、アプリケーションの登録を常に正常な状態に保つために時間とリソースを割り当てることが重要です。 コードのセキュリティ脅威モデルの評価と同様に、アプリケーションのセキュリティと正常性の評価を定期的に実施することをお勧めします。 組織のセキュリティに関するより広い観点については、[セキュリティ開発ライフサイクル](https://www.microsoft.com/securityengineering/sdl) (SDL) を確認してください。

この記事では、次のアプリケーションの登録プロパティについてセキュリティのベスト プラクティスを説明します。

- リダイレクト URI
- アクセス トークンの暗黙的な許可フロー
- 資格情報
- AppId URI
- アプリケーションの所有権
- チェック リスト

## <a name="redirect-uri-configuration"></a>リダイレクト URI の構成

アプリケーションのリダイレクト URI を最新の状態に保つことが重要です。 いずれかのリダイレクト URI の所有権が失効すると、アプリケーションの侵害につながる可能性があります。 すべての DNS レコードが定期的に更新され、変更の有無が監視されていることを確認してください。 すべての URI の所有権を保持することに加えて、ワイルドカード応答 URL や、http または URN などのセキュリティ保護されていない URI スキームは使用しないようにしてください。

![リダイレクト URI](media/active-directory-application-registration-best-practices/redirect-uri.png)

### <a name="redirect-uri-summary"></a>リダイレクト URI の概要

| 推奨                                    | 非推奨          |
| ------------------------------------- | -------------- |
| すべての URI の所有権を保持する        | ワイルドカードを使用する  |
| DNS を最新の状態に保つ                   | URN スキームを使用する |
| リストを小さく保つ                   |   -----        |
| 不要な URI をトリミングする             |   -----        |
| URL を Http スキームから Https スキームに更新する |   -----        |

## <a name="implicit-flow-token-configuration"></a>暗黙的なフロー トークンの構成

**暗黙的なフロー** を必要とするシナリオでは、**承認コード フロー** を使用して、暗黙的な許可フローの誤用に関連する侵害のリスクを軽減できるようになりました。 暗黙的なフローを使用してアクセス トークンを取得するようにアプリケーションの登録を構成したにもかかわらずそれを積極的に使用していない場合は、誤用から保護するためにその設定をオフにすることをお勧めします。

![暗黙的なフローに使用されるアクセス トークン](media/active-directory-application-registration-best-practices/implict-grant-flow.png)

### <a name="implicit-grant-flow-summary"></a>暗黙的な許可フローの概要

| 推奨                                                                    | 非推奨                                                                  |
| --------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| [暗黙的なフローが必要](./v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)かどうかを理解する | [明示的に必要な場合](./v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)を除き、暗黙的なフローを使用する |
| (有効な) 暗黙的なフロー シナリオに対する個別のアプリ登録 |   -----                                                |
| 未使用の暗黙的なフローをオフにする | -----              |

## <a name="credential-configuration"></a>資格情報の構成

アプリケーションが機密クライアントとして使用される場合、資格情報はアプリケーションの登録の重要な部分です。 アプリの登録が、(ユーザーがパブリック エンドポイントを使用してサインインできる) パブリック クライアント アプリとしてのみ使用される場合は、アプリケーション オブジェクトに資格情報が含まれていないことを確認します。 アプリケーションで使用されている資格情報について、使用状況と有効期限を確認します。 使用されていない資格情報がアプリケーションにあると、セキュリティ侵害が発生する可能性があります。
資格情報としてパスワード シークレットを使用すると便利ですが、アプリケーションのトークンを取得するための唯一の資格情報の種類として x509 証明書を使用することを強くお勧めします。 運用環境のパイプラインを監視して、任意の種類の資格情報がコード リポジトリにコミットされていないことを確認します。 Azure を使用している場合は、マネージド ID を使用して、アプリケーションの資格情報が自動的に管理されるようにすることを強くお勧めします。 詳細については、[マネージド ID に関するドキュメント](../managed-identities-azure-resources/overview.md)を参照してください。 [Credential Scanner](../../security/develop/security-code-analysis-overview.md#credential-scanner) は、ソース コードおよびビルド出力内の資格情報 (およびその他の機密コンテンツ) を検出するために使用できる静的分析ツールです。

![Azure portal の [certificates and secrets]\(証明書とシークレット\)](media/active-directory-application-registration-best-practices/credentials.png)

| 推奨                                                                     | 非推奨                             |
| ---------------------------------------------------------------------- | --------------------------------- |
| [証明書の資格情報](./active-directory-certificate-credentials.md)を使用する              | パスワード資格情報を使用する          |
| [マネージド ID](../managed-identities-azure-resources/overview.md) で Key Vault を使用する | アプリ間で資格情報を共有する     |
| 頻繁にロールオーバーする                                                    | 1 つのアプリで多くの資格情報を保持する  |
|     -----                                                              | 古い資格情報を利用可能なままにする |
|     -----                                                              | コードで資格情報をコミットする        |

## <a name="appid-uri-configuration"></a>AppId URI の構成

特定のアプリケーションでは、(WebAPI を介して) リソースを公開できるため、テナント内のリソースを一意に識別する AppId URI を定義する必要があります。 組織内での URI の競合を回避するために、api または https のいずれかの URI スキームを使用し、AppId URI を次の形式で設定することをお勧めします。
AppId URI は API のコードで参照されるスコープのプレフィックスとして機能します。また、AppId URI では、顧客が所有し、検証済みのドメインが使用される必要があります。 マルチテナント アプリケーションの場合、値もグローバルで一意にする必要があります。

[!INCLUDE [active-directory-identifierUri](../../../includes/active-directory-identifier-uri-patterns.md)]

![アプリケーション ID URI](media/active-directory-application-registration-best-practices/app-id-uri.png)

### <a name="appid-uri-summary"></a>AppId URI の概要

| 推奨                                           | 非推奨                  |
| -------------------------------------------- | ---------------------- |
| 有効な URI 形式を使用して競合を回避する。 | ワイルドカード AppId URI を使用する |
| 基幹業務 (LoB) アプリで確認済みドメインを使用する | 形式に誤りがある URI    |
| AppId URI のインベントリを作成する                    |      -----             |
| AppId URI を使用して組織内で WebApi を公開する| AppId URI を使用してアプリケーションを識別する (代わりに appId プロパティを使用します)|

## <a name="app-ownership-configuration"></a>アプリの所有権の構成

アプリの所有権が組織内の最小のユーザーのセットに保たれていることを確認します。 数か月ごとに所有者の一覧を調べて、所有者がまだ組織とチャーター アカウントに属していて、アプリケーションの登録の所有権があることを確認するようお勧めします。 詳細については、[Azure AD アクセス レビュー](../governance/access-reviews-overview.md)に関するページを参照してください。

![ユーザー プロビジョニング サービス - 所有者](media/active-directory-application-registration-best-practices/app-ownership.png)

### <a name="app-ownership-summary"></a>アプリの所有権の概要

| 推奨                  | 非推奨 |
| ------------------- | ----- |
| 小さく保つ       | ----- |
| 所有者の一覧を監視する | ----- |

## <a name="checklist"></a>チェック リスト

アプリ開発者は、Azure portal に用意されている "_チェックリスト_" を使用して、アプリの登録が高品質の基準を満たし、安全に統合するためのガイダンスを提供することを確認できます。 統合アシスタントは、Microsoft ID プラットフォームと統合する際の一般的なミスを回避するのに役立つベスト プラクティスと推奨事項を明確に示します。

![Azure portal の統合アシスタント チェックリスト](media/active-directory-application-registration-best-practices/checklist.png)

### <a name="checklist-summary"></a>チェックリストの概要

| 推奨                                                 | 非推奨 |
| -------------------------------------------------- | ----- |
| チェックリストを使用して、シナリオベースの推奨事項を取得する | ----- |
| アプリ登録ブレードへのディープ リンク             | ----- |


## <a name="next-steps"></a>次のステップ
承認コード フローの詳細については、[OAuth 2.0 承認コード フロー](./v2-oauth2-auth-code-flow.md)に関するページを参照してください。
