---
title: Azure API Management 開発者ポータルの概要
titleSuffix: Azure API Management
description: API Management の開発者ポータルについて説明します。
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/22/2019
ms.author: apimpm
ms.openlocfilehash: 81daada7a62da86772d4657a1a8aaff91f27b673
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045585"
---
# <a name="azure-api-management-developer-portal-overview"></a>Azure API Management 開発者ポータルの概要

開発者ポータルは、自動的に生成され、完全にカスタマイズ可能な、API のドキュメントが含まれる Web サイトです。 API コンシューマーはここで API を見つけ、使用方法を確認し、アクセスを要求し、試すことができます。

この記事では、API Management の開発者ポータルのセルフホステッド バージョンとマネージド バージョンの違いについて説明します。 また、アーキテクチャについて説明し、よく寄せられる質問とその回答を紹介します。

> [!WARNING]
>
> 開発者ポータルの[プレビュー バージョンから一般公開バージョンへの移行方法について確認](#preview-to-ga)してください。

![API Management 開発者ポータル](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a>マネージド バージョンとセルフホステッド バージョン

開発者ポータルは 2 つの方法で構築できます。

- **マネージド バージョン** - API Management インスタンスに組み込まれたポータルを編集およびカスタマイズします。URL `<your-api-management-instance-name>.developer.azure-api.net` からアクセスできます。 マネージド ポータルにアクセスしてカスタマイズする方法については、[こちらのドキュメント記事](api-management-howto-developer-portal-customize.md)を参照してください。
- **セルフホステッド バージョン** - API Management インスタンスの外部にポータルをデプロイし、自らホスティングします。 このアプローチを使用すると、ポータルのコードベースを編集したり、提供されているコア機能を拡張したりできます。 また、自分でポータルを最新バージョンにアップグレードする必要もあります。 詳細および手順については、[ポータルのソース コードを含む GitHub リポジトリ][1]と[ウィジェットの実装に関するチュートリアル][4]を参照してください。 [マネージド バージョンのチュートリアル](api-management-howto-developer-portal-customize.md)では、ポータルの管理パネルについて説明します。これは、セルフホステッド バージョンでも取り上げられています。

## <a name="portal-architectural-concepts"></a>ポータルのアーキテクチャの概念

ポータルのコンポーネントは、*コード*と*コンテンツ*の 2 つのカテゴリに論理的に分類することができます。

*コード*は [GitHub リポジトリ][1]で保持されており、次のものが含まれています。

- ウィジェット - ビジュアル要素を表します。HTML、JavaScript、スタイル設定機能、設定、およびコンテンツ マッピングが結合されます。 例としては、画像、テキスト段落、フォーム、API 一覧などがあります。
- スタイル定義 - ウィジェットのスタイル設定の方法を指定します。
- エンジン - ポータル コンテンツから静的 Web ページを生成します。JavaScript で記述されています。
- ビジュアル エディター - ブラウザー内でのカスタマイズおよび作成エクスペリエンスを可能にします。

*コンテンツ*は、*ポータル コンテンツ*と *API Management コンテンツ*の 2 つのサブカテゴリに分類されます。

*ポータル コンテンツ*はポータルに固有のものであり、次のものが含まれます。

- ページ - たとえば、ランディング ページ、API チュートリアル、ブログ投稿など
- メディア - 画像、アニメーション、およびその他のファイルベースのコンテンツ
- レイアウト - URL に対して照合され、ページの表示方法を定義するテンプレート
- スタイル - フォント、色、罫線などのスタイル定義の値
- 設定 - お気に入りアイコン、Web サイト メタデータなどの構成

*ポータル コンテンツ*は、メディアを除き、JSON ドキュメントとして表現されます。

*API Management コンテンツ*には、API、操作、製品、サブスクリプションなどのエンティティが含まれています。

ポータルは、[Paperbits フレームワーク](https://paperbits.io/)の適合されたフォークに基づいています。 元の Paperbits の機能が拡張され、API Management 固有のウィジェット (API の一覧、製品の一覧など)、およびコンテンツを保存および取得するための API Management サービスへのコネクタが提供されるようになりました。

## <a name="faq"></a>よく寄せられる質問

このセクションでは、新しい開発者ポータルに関してよく寄せられる一般的な質問に回答します。 セルフホステッド バージョンに関する質問については、[GitHub リポジトリの wiki セクション](https://github.com/Azure/api-management-developer-portal/wiki)を参照してください。

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> ポータルのプレビューバージョンから移行するにはどうすればよいですか。

開発者ポータルのプレビュー バージョンを使用して、API Management サービスのプレビュー コンテンツのプロビジョニングを行いました。 ユーザー エクスペリエンスを向上させるために、一般公開バージョンでは既定のコンテンツが大幅に変更されています。 また、新しいウィジェットも含まれています。

マネージド バージョンを使用している場合は、 **[操作]** メニュー セクションの **[コンテンツのリセット]** をクリックして、ポータルのコンテンツをリセットします。 この操作を確認すると、ポータルのすべてのコンテンツが削除され、新しい既定のコンテンツがプロビジョニングされます。 ポータルのエンジンは、API Management サービスで自動的に更新されています。

![ポータル コンテンツのリセット](media/api-management-howto-developer-portal/reset-content.png)

セルフホステッド バージョンを使用している場合は、GitHub リポジトリの `scripts/cleanup.bat` と `scripts/generate.bat` を使用して、既存のコンテンツを削除し、新しいコンテンツをプロビジョニングします。 必ず事前に、ポータルのコードを GitHub リポジトリから最新リリースにアップグレードしてください。

ポータルのコンテンツをリセットしたくない場合は、新しく利用可能となったウィジェットをページ全体で使用することを検討してください。 既存のウィジェットは、最新バージョンに自動的に更新されています。

一般公開の発表後にポータルをプロビジョニングした場合は、新しい既定のコンテンツが既に取り入れられているはずです。 ユーザー側の操作は必要ありません。

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-new-developer-portal"></a>以前の開発者ポータルから新しい開発者ポータルに移行するにはどうすればよいですか。

ポータルには互換性がないため、手動でコンテンツを移行する必要があります。

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>新しいポータルには古いポータルのすべての機能がありますか。

新しい開発者ポータルでは、"*アプリケーション*" と "*問題*" はサポートされていません。 以前のポータルで "*問題*" を使用していて、新しいポータルでも必要な場合は、[専用の GitHub の問題](https://github.com/Azure/api-management-developer-portal/issues/122)にコメントを投稿してください。

対話型の開発者コンソールでの OAuth による認証は、まだサポートされていません。 問題の進展状況は [GitHub](https://github.com/Azure/api-management-developer-portal/issues/208) で追跡できます。

### <a name="has-the-old-portal-been-deprecated"></a>古いポータルは非推奨となりましたか。

以前の開発者ポータルと発行者ポータルは、*レガシ機能*となりました。セキュリティ更新プログラムのみ受け取ることになります。 新しい機能は、新しい開発者ポータルにのみ実装されます。

レガシ ポータルの廃止については、あらためて発表されます。 質問、懸念事項、またはコメントがある場合は、[専用の GitHub の問題](https://github.com/Azure/api-management-developer-portal/issues/121)に提起してください。

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>必要な機能がポータルでサポートされていない

セルフホステッド バージョンを使用し、[独自のウィジェットを実装します][4]。

### <a name="how-can-i-automate-portal-deployments"></a>ポータルのデプロイを自動化するにはどうすればよいですか。

マネージド バージョンとセルフホステッド バージョンのどちらを使用している場合でも、REST API を通じて開発者ポータルのコンテンツにプログラムでアクセスし、管理することができます。

API については、[GitHub リポジトリの wiki セクション][2]に記載されています。 また、たとえばテスト環境から運用環境など、環境間でのポータル コンテンツの移行を自動化するために使用することもできます。 このプロセスの詳細については、GitHub にある[このドキュメント記事](https://aka.ms/apimdocs/migrateportal)を参照してください。

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>ポータルは、Azure Resource Manager テンプレートをサポートしていますか。また、API Management DevOps リソース キットと互換性がありますか。

いいえ。

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-new-managed-portal-dependencies"></a>新しいマネージド ポータルの依存関係に対して追加の VNet 接続を有効にする必要がありますか。

ほとんどの場合、必要ありません。

API Management サービスが内部の VNet にある場合、開発者ポータルにはネットワーク内からしかアクセスできません。 管理エンドポイントのホスト名は、ポータルの管理インターフェイスにアクセスするために使用するコンピューターから、サービスの内部 VIP に解決される必要があります。 管理エンドポイントが DNS に登録されていることを確認してください。 設定に誤りがある場合は、次のエラーが表示されます: `Unable to start the portal. See if settings are specified correctly in the configuration (...)`

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>カスタムの API Management ドメインを割り当てていますが、発行済みのポータルが正しく動作しません

ドメインを更新した後、変更を有効にするには、[ポータルを再発行](api-management-howto-developer-portal-customize.md#publish)する必要があります。

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>ID プロバイダーを追加しましたが、ポータルに表示されません

(AAD、AAD B2C などの) ID プロバイダーを構成した後、変更を有効にするには[ポータルを再発行](api-management-howto-developer-portal-customize.md#publish)する必要があります。

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>委任を設定しましたが、ポータルで使用されていません

委任を設定した後、変更を有効にするには、[ポータルを再発行](api-management-howto-developer-portal-customize.md#publish)する必要があります。

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>API Management の構成に対するその他の変更が開発者ポータルに反映されていません

(VNet、サインイン、製品条項などの) ほとんどの構成変更には[ポータルの再発行](api-management-howto-developer-portal-customize.md#publish)が必要です。

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a>対話型コンソールを使用すると、CORS エラーが発生します

対話型コンソールは、ブラウザーからクライアント側の API 要求を行います。 API に [CORS ポリシー](api-management-cross-domain-policies.md#CORS)を追加して、CORS の問題を解決することができます。 すべてのパラメーターを手動で指定するか、ワイルドカード `*` の値を使用することができます。 次に例を示します。

```XML
<cors>
    <allowed-origins>
        <origin>*</origin>
    </allowed-origins>
    <allowed-methods>
        <method>GET</method>
        <method>POST</method>
        <method>PUT</method>
        <method>DELETE</method>
        <method>HEAD</method>
        <method>OPTIONS</method>
        <method>PATCH</method>
        <method>TRACE</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
    <expose-headers>
        <header>*</header>
    </expose-headers>
</cors>
```

> [!NOTE]
> 
> API(s) スコープではなく Product スコープで CORS ポリシーを適用し、API でヘッダーを介してサブスクリプション キー認証を使用する場合、コンソールは正しく動作しません。
>
> ブラウザーは OPTIONS HTTP 要求を自動的に発行しますが、サブスクリプション キーを含むヘッダーがこの要求には含まれていません。 サブスクリプション キーがないため、API Management は OPTIONS 呼び出しを Product に関連付けることができず、したがって CORS ポリシーを適用できません。
>
> 回避策として、クエリ パラメーターでサブスクリプション キーを渡すことができます。

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>開発者ポータルを編集するにはどのようなアクセス許可が必要ですか。

管理モードでポータルを開いたときに `Oops. Something went wrong. Please try again later.` エラーが表示される場合、必要なアクセス許可 (RBAC) が不足している可能性があります。

従来のポータルでは、ユーザー管理者によるポータルへのアクセスを許可するためには、サービス スコープのアクセス許可 `Microsoft.ApiManagement/service/getssotoken/action` (`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`) が必要でした。 新しいポータルでは、スコープ `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` のアクセス許可 `Microsoft.ApiManagement/service/users/token/action` が必要です。

次の PowerShell スクリプトを使用して、必要なアクセス許可を持つロールを作成することができます。 `<subscription-id>` パラメーターを忘れずに変更してください。 

```PowerShell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
作成したロールは、Azure portal の **[Access Control (IAM)]\(アクセス制御 (IAM)\)** セクションから任意のユーザーに付与できます。 このロールをユーザーに割り当てると、サービス スコープのアクセス許可が割り当てられます。 ユーザーは、サービスの*任意の*ユーザーに代わって SAS トークンを生成できるようになります。 最低限、このロールはサービスの管理者に割り当てる必要があります。 次の PowerShell コマンドは、不必要なアクセス許可をユーザーに付与することを避けるために、最も低いスコープでユーザー `user1` にロールを割り当てる方法を示しています。 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

ユーザーにアクセス許可を付与した後、新しいアクセス許可を有効にするには、ユーザーが Azure portal から一度サインアウトし、再びサインインする必要があります。

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>`Unable to start the portal. See if settings are specified correctly (...)` エラーが表示されます

このエラーは、`https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` に対する `GET` 呼び出しが失敗すると表示されます。 この呼び出しは、ポータルの管理インターフェイスによってブラウザーから発行されます。

API Management サービスが VNet 内にある場合は、VNet 接続に関する前出の質問を参照してください。

カスタム ドメインに割り当てられているがブラウザーで信頼されていない SSL 証明書が原因で呼び出しが失敗する場合もあります。 軽減策として、管理エンドポイントのカスタム ドメインを削除することができます。API Management は、信頼できる証明書を使用して既定のエンドポイントにフォールバックします。

## <a name="next-steps"></a>次のステップ

新しい開発者ポータルの詳細を確認します:

- [マネージド開発者ポータルへのアクセスとカスタマイズ](api-management-howto-developer-portal-customize.md)
- [ポータルのセルフホステッド バージョンの設定][2]
- [独自のウィジェットの実装][4]

その他のリソースを参照します:

- [ソース コードがある GitHub リポジトリ][1]
- [プロジェクトのパブリック ロードマップ][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects
[4]: https://aka.ms/apimdevportal/extend