---
title: Azure AD テナントのアプリ要求をカスタマイズする (PowerShell)
titleSuffix: Microsoft identity platform
description: このページでは、Azure Active Directory の要求のマッピングについて説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: e77155f8a6efd3916ae90fcb562d688bb5b5126f
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598892"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>方法:テナントの特定のアプリケーションに対するトークンに出力された要求のカスタマイズ (プレビュー)

> [!NOTE]
> この機能は、現在ポータルで提供されている[要求のカスタマイズ](active-directory-saml-claims-customization.md)に取って代わり、そのカスタマイズに優先します。 このドキュメントで詳しく説明している Graph/PowerShell の方法に加えて、ポータルを使用して、同じアプリケーションの要求をカスタマイズすると、そのアプリケーションに対して発行されたトークンは、ポータルの構成を無視します。 このドキュメントで詳しく説明した方法で行った構成は、ポータルには反映されません。

> [!NOTE]
> この機能は現在パブリック プレビューの段階です。 変更を元に戻すか、削除できるように準備しておいてください。 機能は、パブリック プレビュー期間中、すべての Azure Active Directory (Azure AD) サブスクリプションで使用できます。 ただし、この機能が一般公開された後は、機能の一部で Azure AD Premium サブスクリプションが必要になる場合があります。 この機能は、WS-Fed、SAML、OAuth、OpenID Connect の各プロトコルについて、要求のマッピング ポリシーの構成をサポートしています。

この機能は、テナント管理者が、テナントの特定のアプリケーションに対するトークンに出力された要求をカスタマイズするときに使用します。 要求のマッピング ポリシーを使用すると、次の操作を行うことができます。

- トークンに含める要求を選択する。
- まだ存在しない要求の種類を作成する。
- 特定の要求で出力されたデータのソースを選択または変更する。

この記事では、[要求のマッピング ポリシーの種類](reference-claims-mapping-policy-type.md)を使用する方法を理解するうえで役に立つ、一般的なシナリオをいくつか取り上げて説明します。

要求のマッピングポリシーを作成するときに、トークンのディレクトリ スキーマ拡張属性から要求を出力することもできます。 `ClaimsSchema` 要素の *ID* ではなく、拡張属性の *ExtensionID* を使用します。  拡張属性の詳細については、[ディレクトリ スキーマ拡張属性の使用](active-directory-schema-extensions.md)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

次の例では、サービス プリンシパルのポリシーを作成、更新、リンク、および削除します。 要求のマッピング ポリシーは、サービス プリンシパル オブジェクトにのみ割り当てることができます。 Azure AD に慣れていない場合は、こうした例を確認する前に、[Azure AD テナントを取得する方法](quickstart-create-new-tenant.md)について学習することをお勧めします。

使用を開始するには、次の手順を実行します。

1. 最新版の [Azure AD PowerShell モジュール パブリック プレビュー リリース](https://www.powershellgallery.com/packages/AzureADPreview)をダウンロードします。
1. Connect コマンドを実行して、Azure AD 管理者アカウントにサインインします。 新しいセッションを開始するたびにこのコマンドを実行します。

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. 組織に作成されているすべてのポリシーを表示するには、次のコマンドを実行します。 ポリシーが想定どおりに作成されていることを確認するために、次のシナリオのほとんどの操作の後にこのコマンドを実行することをお勧めします。

   ``` powershell
   Get-AzureADPolicy
   ```

## <a name="omit-the-basic-claims-from-tokens"></a>トークンから基本要求を除外する

この例では、リンク サービス プリンシパルに対して発行されたトークンから、[基本要求セット](reference-claims-mapping-policy-type.md#claim-sets)を削除するポリシーを作成します。

1. 要求のマッピング ポリシーを作成します。 このポリシーは、特定のサービス プリンシパルにリンクされ、トークンから基本要求セットを削除します。
   1. ポリシーを作成するには、このコマンドを実行します。

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. 新しいポリシーを表示し、ポリシーの ObjectId を取得するには、次のコマンドを実行します。

      ``` powershell
      Get-AzureADPolicy
      ```
1. サービス プリンシパルにポリシーを割り当てます。 サービス プリンシパルの ObjectId も取得する必要があります。
   1. 組織のすべてのサービス プリンシパルを表示するには、[Microsoft Graph API にクエリを実行](/graph/traverse-the-graph)します。 または、[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) で、Azure AD アカウントにサインインします。
   2. サービス プリンシパルの ObjectId がある場合は、次のコマンドを実行します。

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>EmployeeID と TenantCountry を要求としてトークンに含める

この例では、リンクされたサービス プリンシパルに対して発行されたトークンに、EmployeeID と TenantCountry を追加するポリシーを作成します。 EmployeeID は、SAML トークンと JWT の両方で名前要求の種類として出力されます。 TenantCountry は、SAML トークンと JWT の両方で国/リージョン要求の種類として出力されます。 この例では、操作を続行し、トークンに基本要求セットを含めます。

1. 要求のマッピング ポリシーを作成します。 このポリシーは、特定のサービス プリンシパルにリンクされ、EmployeeID 要求と TenantCountry 要求をトークンに追加します。
   1. ポリシーを作成するには、次のコマンドを実行します。

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. 新しいポリシーを表示し、ポリシーの ObjectId を取得するには、次のコマンドを実行します。

      ``` powershell
      Get-AzureADPolicy
      ```
1. サービス プリンシパルにポリシーを割り当てます。 サービス プリンシパルの ObjectId も取得する必要があります。
   1. 組織のすべてのサービス プリンシパルを表示するには、[Microsoft Graph API にクエリを実行](/graph/traverse-the-graph)します。 または、[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) で、Azure AD アカウントにサインインします。
   2. サービス プリンシパルの ObjectId がある場合は、次のコマンドを実行します。

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="use-a-claims-transformation-in-tokens"></a>トークンで要求変換を使用する

この例では、リンクされたサービス プリンシパルに対して発行された JWT に、カスタム要求 "JoinedData" を出力するポリシーを作成します。 この要求には、ユーザー オブジェクトの extensionattribute1 属性に格納されたデータと ".sandbox" を結合して作成された値が追加されます。 この例では、トークンで基本要求セットを除外します。

1. 要求のマッピング ポリシーを作成します。 このポリシーは、特定のサービス プリンシパルにリンクされ、EmployeeID 要求と TenantCountry 要求をトークンに追加します。
   1. ポリシーを作成するには、次のコマンドを実行します。

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. 新しいポリシーを表示し、ポリシーの ObjectId を取得するには、次のコマンドを実行します。

      ``` powershell
      Get-AzureADPolicy
      ```
1. サービス プリンシパルにポリシーを割り当てます。 サービス プリンシパルの ObjectId も取得する必要があります。
   1. 組織のすべてのサービス プリンシパルを表示するには、[Microsoft Graph API にクエリを実行](/graph/traverse-the-graph)します。 または、[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) で、Azure AD アカウントにサインインします。
   2. サービス プリンシパルの ObjectId がある場合は、次のコマンドを実行します。

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>セキュリティに関する考慮事項

トークンを受信するアプリケーションは、クレーム値が Azure AD によって正式に発行され、改ざんできないという事実に依存します。 ただし、クレーム マッピング ポリシーを使用してトークンの内容を変更すると、これらの想定が正しくなくなる可能性があります。 アプリケーションは、悪意のあるアクターによって作成されたクレーム マッピング ポリシーからそれ自体を保護するために、トークンがクレーム マッピング ポリシーの作成者によって変更されたことを明示的に認識する必要があります。 これは、次の方法で実現できます。

- カスタム署名キーを構成する
- マップされたクレームを受け入れるようにアプリケーション マニフェストを更新する。
 
これを行わないと、Azure AD は[`AADSTS50146` エラー コード](reference-aadsts-error-codes.md#aadsts-error-codes)を返します。

### <a name="custom-signing-key"></a>カスタム署名キー

サービス プリンシパル オブジェクトにカスタム署名キーを追加するには、Azure PowerShell コマンドレット [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) を使用して、ご自身のアプリケーション オブジェクトの証明書キー資格情報を作成します。

要求のマッピングが有効なアプリでは、[OpenID Connect メタデータ要求](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)に `appid={client_id}` を追加して、トークン署名キーを検証する必要があります。 使用する必要がある OpenID Connect メタデータ ドキュメントのフォーマットは次のとおりです。

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>アプリケーション マニフェストを更新する

別の方法として、[アプリケーション マニフェスト](reference-app-manifest.md)で `acceptMappedClaims` プロパティを `true` に設定することもできます。 「[apiApplication リソースの種類](/graph/api/resources/apiapplication#properties)」に記載されているように、これにより、カスタム署名キーを指定せずに、アプリケーションでクレーム マッピングが使用できます。

これを行うには、要求されたトークンの対象ユーザーが Azure AD テナントの検証済みドメイン名を使用する必要があります。つまり、(アプリケーション マニフェスト内で `identifierUris` によって表される) `Application ID URI` を例えば `https://contoso.com/my-api` に設定するか、(単に既定のテナント名を使用して) `https://contoso.onmicrosoft.com/my-api` に設定する必要があります。

検証済みのドメインを使用していない場合、Azure AD は `AADSTS501461` エラー コードを、 *[AcceptMappedClaims is only supported for a token audience matching the application GUID or an audience within the tenant's verified domains. Either change the resource identifier, or use an application-specific signing key]\(AcceptMappedClaims は、アプリケーション GUID に一致するトークン対象ユーザー、またはテナントの検証済みドメイン内の対象ユーザーでのみサポートされています。リソース識別子を変更するか、アプリケーション固有の署名キーを使用してください\)* というメッセージと共に返します。

## <a name="next-steps"></a>次のステップ

- 詳細については、[要求のマッピング ポリシーの種類](reference-claims-mapping-policy-type.md)に関するリファレンス記事を参照してください。
- SAML トークンで発行された要求を Azure portal でカスタマイズする方法については、「[方法: エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](active-directory-saml-claims-customization.md)」を参照してください。
- 拡張属性の詳細については、[要求でのディレクトリ スキーマ拡張属性の使用](active-directory-schema-extensions.md)に関するページをご覧ください。
