---
title: 開発者ポータル - よく寄せられる質問
titleSuffix: Azure API Management
description: API Management の開発者ポータルについてよく寄せられる質問。 開発者ポータルは、API のコンシューマーが API を探索できるカスタマイズ可能な Web サイトです。
services: api-management
documentationcenter: API Management
author: dlepow
ms.service: api-management
ms.topic: troubleshooting
ms.date: 07/30/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0c8757211bf11e97cde091d49eb6512bb0e4f7cc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656343"
---
# <a name="api-management-developer-portal---frequently-asked-questions"></a>API Management 開発者ポータル - よく寄せられる質問

## <a name="what-if-i-need-functionality-that-isnt-supported-in-the-portal"></a>ポータルでサポートされていない機能が必要な場合はどうすればよいですか。

[GitHub リポジトリ](https://github.com/Azure/api-management-developer-portal)で機能要求を開いたり、[不足している機能を自分で実装](developer-portal-implement-widgets.md)したりすることができます。 詳細については、開発者ポータルの[拡張性](api-management-howto-developer-portal.md#managed-vs-self-hosted)に関するページを参照してください。


## <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>1 つの API Management サービスに複数の開発者ポータルを含めることはできますか。

1 つのマネージド ポータルと複数のセルフホステッド ポータルを使用できます。 すべてのポータルのコンテンツは同じ API Management サービスに格納されるため、同じになります。 ポータルの外観と機能を区別する場合は、実行時にたとえば URL に基づいてページを動的にカスタマイズする独自のカスタム ウィジェットを使用して、セルフホストすることができます。

## <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>ポータルは、Azure Resource Manager テンプレートをサポートしていますか。また、API Management DevOps リソース キットと互換性がありますか。

いいえ。

## <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>ポータルのコンテンツは、API Management のバックアップ/復元機能を使用して保存されていますか。

いいえ。

## <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>マネージド ポータルの依存関係に対して追加の VNET 接続を有効にする必要がありますか。

ほとんどの場合、必要ありません。

API Management サービスが内部の VNet にある場合、開発者ポータルにはネットワーク内からしかアクセスできません。 管理エンドポイントのホスト名は、ポータルの管理インターフェイスにアクセスするために使用するコンピューターから、サービスの内部 VIP に解決される必要があります。 管理エンドポイントが DNS に登録されていることを確認してください。 設定に誤りがある場合は、次のエラーが表示されます: `Unable to start the portal. See if settings are specified correctly in the configuration (...)`

API Management サービスが内部 VNet にあり、インターネットから Application Gateway 経由でアクセスする場合は、開発者ポータルと API Management の管理エンドポイントへの接続を有効にしてください。 Web アプリケーションのファイアウォール規則を無効にする必要がある場合があります。 詳細については、[このドキュメントの記事](api-management-howto-integrate-internal-vnet-appgateway.md)を参照してください。

## <a name="i-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>カスタムの API Management ドメインを割り当てていますが、発行済みのポータルが正しく動作しません

ドメインを更新した後、変更を有効にするには、[ポータルを再発行](api-management-howto-developer-portal-customize.md#publish)する必要があります。

## <a name="i-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>ID プロバイダーを追加しましたが、ポータルに表示されません

(Azure AD、Azure AD B2C などの) ID プロバイダーを構成した後、変更を有効にするには[ポータルを再発行](api-management-howto-developer-portal-customize.md#publish)する必要があります。 開発者ポータルのページに OAuth ボタン ウィジェットが含まれていることを確認してください。

## <a name="i-set-up-delegation-and-the-portal-doesnt-use-it"></a>委任を設定しましたが、ポータルで使用されていません

委任を設定した後、変更を有効にするには、[ポータルを再発行](api-management-howto-developer-portal-customize.md#publish)する必要があります。

## <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>API Management の構成に対するその他の変更が開発者ポータルに反映されていません

(VNet、サインイン、製品条項などの) ほとんどの構成変更には[ポータルの再発行](api-management-howto-developer-portal-customize.md#publish)が必要です。

## <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> 対話型コンソールを使用すると、CORS エラーが発生します

対話型コンソールは、ブラウザーからクライアント側の API 要求を行います。 API に [CORS ポリシー](api-management-cross-domain-policies.md#CORS)を追加して、CORS の問題を解決します。

CORS ポリシーの状態は、Azure portal の API Management サービスの **[ポータルの概要]** セクションで確認できます。 警告ボックスは、ポリシーが存在しないか、構成が正しくないことを示します。

> [!NOTE]
> 
> 1 つの CORS ポリシーのみが実行されます。 複数の CORS ポリシーを指定した場合 (たとえば、API レベルとすべての API レベル)、対話型コンソールが期待どおりに動作しないことがあります。

![CORS ポリシーの状態を確認できる場所を示すスクリーンショット。](media/developer-portal-faq/cors-azure-portal.png)

**[CORS を有効にする]** ボタンをクリックして、CORS ポリシーを自動的に適用します。

CORS は手動で有効にすることもできます。

1. 生成されたポリシー コードを表示するには、 **[手動でグローバル レベルに適用する]** リンクを選択します。
2. Azure portal で API Management サービスの **[API]** セクションにある **[すべての API]** に移動します。
3. **[受信処理]** セクションで **[</>]** アイコンを選択します。
4. XML ファイルの **[\<inbound\>]** セクションにポリシーを挿入します。 **\<origin\>** 値が、開発者ポータルのドメインと一致していることを確認します。

> [!NOTE]
> 
> API(s) スコープではなく Product スコープで CORS ポリシーを適用し、API でヘッダーを介してサブスクリプション キー認証を使用する場合、コンソールは正しく動作しません。
>
> ブラウザーは `OPTIONS` HTTP 要求を自動的に発行しますが、サブスクリプション キーを含むヘッダーがこの要求には含まれていません。 サブスクリプション キーがないため、API Management は `OPTIONS` 呼び出しを Product に関連付けることができず、したがって CORS ポリシーを適用できません。
>
> 回避策として、クエリ パラメーターでサブスクリプション キーを渡すことができます。

## <a name="what-is-the-cors-proxy-feature-and-when-should-i-use-it"></a>CORS プロキシ機能とは何ですか。どのような場合に使用する必要がありますか。

API 操作の詳細ウィジェットの構成で **[Use CORS proxy]\(CORS プロキシの使用\)** オプションを選択し、API Management サービスのポータルのバックエンドを介して対話型コンソールの API 呼び出しをルーティングします。 この構成にすると、API に CORS ポリシーを適用する必要がなくなり、ローカル マシンからゲートウェイ エンドポイントに接続する必要もなくなります。 API がセルフホステッド ゲートウェイを介して公開されている場合、またはサービスが仮想ネットワーク内にある場合は、API Management のバックエンド サービスからゲートウェイへの接続が必要です。 セルフホステッド ポータルを使用する場合は、構成ファイルの `backendUrl` オプションを使用してポータルのバックエンド エンドポイントを指定します。 そうしないと、セルフホステッド ポータルによってバックエンド サービスの場所が認識されません。

## <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>開発者ポータルを編集するにはどのようなアクセス許可が必要ですか。

管理モードでポータルを開いたときに `Oops. Something went wrong. Please try again later.` エラーが表示される場合、必要なアクセス許可 (Azure RBAC) が不足している可能性があります。

従来のポータルでは、ユーザー管理者によるポータルへのアクセスを許可するためには、サービス スコープのアクセス許可 `Microsoft.ApiManagement/service/getssotoken/action` (`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`) が必要でした。 新しいポータルでは、スコープ `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` のアクセス許可 `Microsoft.ApiManagement/service/users/token/action` が必要です。

次の PowerShell スクリプトを使用して、必要なアクセス許可を持つロールを作成することができます。 `<subscription-id>` パラメーターを忘れずに変更してください。 

```powershell
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
 
作成したロールは、Azure portal の **[Access Control (IAM)]\(アクセス制御 (IAM)\)** セクションから任意のユーザーに付与できます。 このロールをユーザーに割り当てると、サービス スコープのアクセス許可が割り当てられます。 ユーザーは、サービスの *任意の* ユーザーに代わって SAS トークンを生成できるようになります。 最低限、このロールはサービスの管理者に割り当てる必要があります。 次の PowerShell コマンドは、不必要なアクセス許可をユーザーに付与することを避けるために、最も低いスコープでユーザー `user1` にロールを割り当てる方法を示しています。 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

ユーザーにアクセス許可を付与した後、新しいアクセス許可を有効にするには、ユーザーが Azure portal から一度サインアウトし、再びサインインする必要があります。

## <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>`Unable to start the portal. See if settings are specified correctly (...)` エラーが表示されます

このエラーは、`https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` に対する `GET` 呼び出しが失敗すると表示されます。 この呼び出しは、ポータルの管理インターフェイスによってブラウザーから発行されます。

API Management サービスが VNet 内にある場合は、[VNet 接続に関する質問](#do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies)を参照してください。

カスタム ドメインに割り当てられているがブラウザーで信頼されていない TLS/SSL 証明書が原因で呼び出しが失敗する場合もあります。 軽減策として、管理エンドポイントのカスタム ドメインを削除することができます。API Management は、信頼できる証明書を使用して既定のエンドポイントにフォールバックします。

## <a name="whats-the-browser-support-for-the-portal"></a>ポータルのブラウザー サポートとは何ですか？

| Browser                     | サポートされています       |
|-----------------------------|-----------------|
| Apple Safari                | 可<sup>1</sup> |
| Google Chrome               | 可<sup>1</sup> |
| Microsoft Edge              | 可<sup>1</sup> |
| Microsoft Internet Explorer | いいえ              |
| Mozilla Firefox             | 可<sup>1</sup> |

 <small><sup>1</sup> 2 つの最新の生産バージョンでサポートされています。</small>

## <a name="local-development-of-my-self-hosted-portal-is-no-longer-working"></a>セルフホステッド ポータルのローカル開発が機能しなくなりました

ローカル バージョンの開発者ポータルでストレージ アカウントまたは API Management インスタンスから情報を保存または取得できない場合は、SAS トークンの有効期限が切れている可能性があります。 新しいトークンを生成することでこれを解決できます。 手順については、[開発者ポータルのセルフホスト](developer-portal-self-host.md#step-2-configure-json-files-static-website-and-cors-settings)に関するチュートリアルを参照してください。

## <a name="how-do-i-disable-sign-up-in-the-developer-portal"></a>開発者ポータルでサインアップを無効にするにはどうすればよいですか?

開発者ポータルでサインアップ機能を既定で有効にする必要がない場合は、次の手順で無効にすることができます。

1. Azure portal で、API Management インスタンスに移動します。
1. メニューの **[開発者ポータル]** で **[ID]** を選択します。
1. 一覧に表示されている各 ID プロバイダーを削除します。 各 ID プロバイダーを指定してコンテキスト メニュー ( **...** ) を選択し、 **[削除]** を選択します。
 
   :::image type="content" source="media/developer-portal-faq/delete-identity-providers.png" alt-text="ID プロバイダーの削除":::
 
1. 開発者ポータルの管理インターフェイスに移動します。
1. ポータル コンテンツの **[サインアップ]** リンクとナビゲーション項目を削除します。 ポータル コンテンツのカスタマイズ方法の詳細については、「[チュートリアル: 開発者ポータルへのアクセスとそのカスタマイズ](api-management-howto-developer-portal-customize.md)」を参照してください。
 
   :::image type="content" source="media/developer-portal-faq/delete-navigation-item.png" alt-text="ナビゲーション項目の削除":::
 
1. ユーザーが直接移動する場合は、 **[サインアップ]** ページ コンテンツを変更して、ID データの入力に使用するフィールドを削除します。
   
   必要に応じて、 **[サインアップ]** ページを削除します。 現時点では、このページの一覧表示と削除には [contentItem](/rest/api/apimanagement/2021-01-01-preview/content-item) REST API を使用します。
 
1. 変更を保存して、[ポータルを再発行](api-management-howto-developer-portal-customize.md#publish)します。

## <a name="how-can-i-remove-the-developer-portal-content-provisioned-to-my-api-management-service"></a>API Management サービスにプロビジョニングされた開発者ポータルのコンテンツを削除するにはどうすればよいですか。

開発者ポータルの [GitHub リポジトリ](https://github.com/Azure/api-management-developer-portal)の `scripts.v3/cleanup.bat` スクリプトに必要なパラメーターを指定し、スクリプトを実行します

```sh
cd scripts.v3
.\cleanup.bat
cd ..
```

## <a name="how-do-i-enable-single-sign-on-sso-authentication-to-self-hosted-developer-portal"></a>セルフホステッド開発者ポータルに対してシングル サインオン (SSO) 認証を有効にするにはどうすればよいですか。

認証方法のうち、開発者ポータルではシングル サインオン (SSO) がサポートされています。 この方法で認証するには、クエリ パラメーターでトークンを使用して `/signin-sso` を呼び出す必要があります。

```html
https://contoso.com/signin-sso?token=[user-specific token]
```
### <a name="generate-user-tokens"></a>ユーザー トークンを生成する
[API Management REST API](/rest/api/apimanagement/apimanagementrest/api-management-rest) の「[共有アクセス トークンの取得](/rest/api/apimanagement/2020-12-01/user/get-shared-access-token)」操作を使用して、"*ユーザー固有のトークン*" (管理者トークンを含む) を生成することができます。

> [!NOTE]
> トークンは URL エンコードされている必要があります。


## <a name="next-steps"></a>次のステップ

新しい開発者ポータルの詳細を確認します:

- [マネージド開発者ポータルへのアクセスとカスタマイズ](api-management-howto-developer-portal-customize.md)
- [ポータルのセルフホステッド バージョンの設定](developer-portal-self-host.md)
- [独自のウィジェットの実装](developer-portal-implement-widgets.md)

その他のリソースを参照します:

- [ソース コードがある GitHub リポジトリ](https://github.com/Azure/api-management-developer-portal)

