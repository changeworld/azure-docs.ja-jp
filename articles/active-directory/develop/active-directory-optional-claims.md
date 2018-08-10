---
title: Azure AD アプリケーションに省略可能な要求を提供する方法 | Microsoft Docs
description: Azure Active Directory から発行された SAML 2.0 トークンおよび JSON Web トークン (JWT) にカスタムまたは追加の要求を追加する方法について説明します。
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: d924c1fc9697bff77f12f7f0bf33a1654d1e7d6e
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597975"
---
# <a name="optional-claims-in-azure-ad-preview"></a>Azure AD の省略可能な要求 (プレビュー)

この機能は、アプリケーション開発者が、自作のアプリケーションに送信されるトークンに必要な要求を指定するために使用します。 次の処理に省略可能な要求を使用できます。
-   アプリケーションのトークンに含める追加の要求を選択する。
-   Azure AD からトークンで返される特定の要求の動作を変更する。
-   アプリケーションのカスタムの要求を追加してアクセスする。 

> [!Note]
> この機能は現在パブリック プレビューの段階です。 変更を元に戻すか、削除できるように準備しておいてください。 この機能は、パブリック プレビュー期間中、すべての Azure AD サブスクリプションで使用できます。 ただし、この機能が一般公開された後は、機能の一部で Azure AD Premium サブスクリプションが必要になる場合があります。

標準的な要求の一覧とそのトークンの使用方法については、[Azure AD から発行されるトークンの基本](v1-id-and-access-tokens.md)に関するページを参照してください。 

[v2.0 Azure AD エンドポイント](active-directory-appmodel-v2-overview.md)の目標の 1 つは、クライアントの最適なパフォーマンスを確保するために、トークン サイズをより小さくすることです。  その結果、以前のバージョンではアクセス トークンと ID トークンに含まれていた一部の要求は、v2.0 トークンでは削除されたため、アプリケーションごとに具体的に要求する必要があります。  

**表 1: 適用性**

| アカウントの種類 | v1.0 エンドポイント                      | v2.0 エンドポイント  |
|--------------|------------------------------------|----------------|
| 個人用 Microsoft アカウント  | 該当なし - RPS チケットが代わりに使用されます | サポート予定 |
| Azure AD アカウント          | サポートされています                          | サポートされています      |

## <a name="standard-optional-claims-set"></a>標準の省略可能な要求セット
既定でアプリケーションが使用できる省略可能な要求セットを以下に示します。  アプリケーションにカスタムの省略可能な要求を追加する方法については、後述の[ディレクトリ拡張](active-directory-optional-claims.md#Configuring-custom-claims-via-directory-extensions)に関するセクションを参照してください。 

> [!Note]
>このような要求の大部分は v1.0 および v2.0 トークンの JWT に含めることができますが、「トークンの種類」列に記載されている場合を除き、SAML トークンに含めることはできません。  また、省略可能な要求は現在 AAD ユーザーに対してのみサポートされていますが、MSA のサポートが追加される予定です。  MSA が v2.0 エンドポイントで省略可能な要求をサポートした場合は、「ユーザーの種類」列に AAD ユーザーまたは MSA ユーザーが要求を使用できるかどうかを記載する予定です。  

**表 2: 標準の省略可能な要求セット**

| Name                        | 説明   | トークンの種類 | ユーザーの種類 | メモ  |
|-----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | ユーザーが最後に認証された時刻。  OpenID Connect の仕様を参照してください。| JWT        |           |  |
| `tenant_region_scope`      | リソースのテナントのリージョン | JWT        |           | |
| `signin_state`             | サインイン状態要求   | JWT        |           | フラグとして 6 個の戻り値があります。<br> "dvc_mngd": デバイスが管理されているかどうか<br> "dvc_cmp": デバイスが準拠しているかどうか<br> "dvc_dmjd": デバイスがドメインに参加しているかどうか<br> "dvc_mngd_app": デバイスが MDM 経由で管理されているかどうか<br> "inknownntwk": デバイスが既知のネットワーク内にあるかどうか。<br> "kmsi": [サインインしたままにする] が使用されたかどうか <br> |
| `controls`                 | 条件付きアクセス ポリシーによって適用されるセッション コントロールを含む複数値の要求。  | JWT        |           | 3 個の値:<br> "app_res": アプリはより細かい制限を適用する必要があります。 <br> "ca_enf": 条件付きアクセスの適用は延期されましたが、現在も必要です。 <br> "no_cookie": ブラウザー内のクッキーを交換するにはこのトークンは不十分です。 <br>  |
| `home_oid`                 | ゲスト ユーザーの場合、ユーザーのホーム テナント内のユーザーのオブジェクト ID。| JWT        |           | |
| `sid`                      | セッション ID。セッションごとのユーザーのサインアウトに使用されます。 | JWT        |           |         |
| `platf`                    | デバイスのプラットフォーム    | JWT        |           | デバイスの種類を検証できる、マネージド デバイスに制限されます。|
| `verified_primary_email`   | ユーザーの PrimaryAuthoritativeEmail が送信元です      | JWT        |           |         |
| `verified_secondary_email` | ユーザーの SecondaryAuthoritativeEmail が送信元です   | JWT        |           |        |
| `enfpolids`                | 適用されたポリシー ID。 現在のユーザーに対して評価されたポリシー ID の一覧。  | JWT |  |  |
| `vnet`                     | VNET 指定子情報。    | JWT        |           |      |
| `fwd`                      | IP アドレス。| JWT    |   | 要求側クライアントの元の IPv4 アドレスを追加します (VNET 内の場合) |
| `ctry`                     | ユーザーの国 | JWT |           | Azure AD は、存在する場合、`ctry` の省略可能な要求を返します。要求の値は、FR、JP、SZ などの標準の 2 文字の国番号です。 |
| `tenant_ctry`              | リソース テナントの国 | JWT | | |
| `xms_pdl`          | 優先されるデータの場所   | JWT | | Multi-Geo テナントの場合、ユーザーがどの地域にいるかを示す 3 文字のコードです。  詳細については、[優先されるデータの場所に関する Azure AD Connect のドキュメント](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation)を参照してください。 <br> たとえば、アジア太平洋の場合は `APC` です。 |
| `xms_pl`                   | ユーザーの優先する言語  | JWT ||ユーザーの優先する言語 (設定されている場合)。  ゲスト アクセスのシナリオの場合、ソースはホーム テナントです。  LL-CC ("en-us") という形式です。 |
| `xms_tpl`                  | テナントの優先する言語| JWT | | テナントの優先する言語 (設定されている場合)。  LL ("en") という形式です。 |
| `ztdid`                    | ゼロタッチ デプロイ ID | JWT | | [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) に使用されるデバイス ID |
| `acct`             | テナント内のユーザー アカウントの状態。   | JWT、SAML | | ユーザーがテナントのメンバーである場合、値は `0` です。  ユーザーがゲストの場合、値は `1` です。  |
| `upn`                      | UserPrincipalName 要求。  | JWT、SAML  |           | この要求は自動的に含まれますが、省略可能な要求として、ゲスト ユーザーの場合に動作を変更するために追加のプロパティをアタッチする要求を指定することもできます。  <br> 追加のプロパティ: <br> `include_externally_authenticated_upn` <br> `include_externally_authenticated_upn_without_hash` |

### <a name="v20-optional-claims"></a>V2.0 の省略可能な要求
これらの要求は常に v1.0 トークンに含まれますが、要求されない限り v2.0 トークンには含まれません。  これらの要求は、JWT (ID トークンとアクセス トークン) にのみ適用されます。  

**表 3: V2.0 のみの省略可能な要求**

| JWT の要求     | Name                            | 説明                                                                                                                    | メモ |
|---------------|---------------------------------|--------------------------------------------------------------------------------------------------------------------------------|-------|
| `ipaddr`      | IP アドレス                      | ログインしたクライアントの IP アドレス。                                                                                      |       |
| `onprem_sid`  | オンプレミスのセキュリティ ID |                                                                                                                                |       |
| `pwd_exp`     | パスワードの有効期限        | パスワードの有効期限が切れる日時。                                                                                    |       |
| `pwd_url`     | パスワードの変更 URL             | ユーザーがパスワードを変更するためにアクセスできる URL。                                                                        |       |
| `in_corp`     | 企業ネットワーク内        | クライアントが企業ネットワークからログインしている場合に通知します。 そうでない場合、要求は含まれません                     |       |
| `nickname`    | ニックネーム                        | ユーザーの追加の名前。姓または名とは別の名前です。                                                             |       |                                                                                                                |       |
| `family_name` | 姓                       | Azure AD ユーザー オブジェクトで定義されたユーザーの姓や名字を示します。 <br>"family_name":"Miller" |       |
| `given_name`  | 名                      | Azure AD ユーザー オブジェクトに設定されたユーザーの名を示します。<br>"given_name": "Frank"                   |       |

### <a name="additional-properties-of-optional-claims"></a>省略可能な要求の追加のプロパティ

一部の省略可能な要求は、要求が返される方法を変更するように構成することができます。  このような追加のプロパティは、ほとんどの場合、異なるデータが期待されるオンプレミス アプリケーションの移行のために使用されます (たとえば、`include_externally_authenticated_upn_without_hash` は UPN 内のハッシュマーク (`#`) を処理できないクライアントで役立ちます)。

**表 4: 標準の省略可能な要求を構成する値**

| プロパティ名                                     | 追加のプロパティ名                                                                                                             | 説明 |
|---------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-------------|
| `upn`                                                 |                                                                                                                                      |  SAML 応答と JWT 応答の両方に使用できます。            |
| | `include_externally_authenticated_upn`              | リソース テナントに格納されているゲスト UPN が含まれます。  たとえば、`foo_hometenant.com#EXT#@resourcetenant.com` のように指定します。                            |             
| | `include_externally_authenticated_upn_without_hash` | 前項と同じですが、ハッシュマーク (`#`) はアンダースコア (`_`) に置き換えられます (例: `foo_hometenant.com_EXT_@resourcetenant.com`)。 |             

> [!Note]
>追加のプロパティを指定せずに省略可能な要求 upn を指定しても、動作は変わりません。トークンで発行された新しい要求を表示するには、追加のプロパティのうち少なくとも 1 つを追加する必要があります。 


#### <a name="additional-properties-example"></a>追加のプロパティの例:

```json
 "optionalClaims": 
   {
       "idToken": [ 
             { 
                "name": "upn", 
            "essential": false,
                "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ]
}
```

この OptionalClaims オブジェクトにより、ID トークンがクライアントに返され、追加のホーム テナントとリソース テナントの情報を持つ追加の upn が含められます。  その結果、ユーザーが (認証に異なる IDP を使用する) テナントのゲストである場合にのみ、トークンの `upn` 要求が変更されます。 

## <a name="configuring-optional-claims"></a>省略可能な要求の構成

アプリケーション マニフェストを変更して、アプリケーションの省略可能な要求を構成することができます (以下の例を参照してください)。 詳細については、[Azure AD アプリケーション マニフェストの概要に関する記事](reference-app-manifest.md)を参照してください。

**スキーマ例:**

```json
"optionalClaims":  
   {
       "idToken": [
             { 
                   "name": "auth_time", 
                   "essential": false
              }
        ],
 "accessToken": [ 
             {
                    "name": "ipaddr", 
                    "essential": false
              }
        ],
"saml2Token": [ 
              { 
                    "name": "upn", 
                    "essential": true
               },
               { 
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": true
               }
       ]
   }
```

### <a name="optionalclaims-type"></a>OptionalClaims 型

アプリケーションから要求する省略可能な要求を宣言します。 アプリケーションは、セキュリティ トークン サービスから受信できる 3 種類の各トークン (ID トークン、アクセス トークン、SAML 2 トークン) で返される省略可能な要求を構成できます。 アプリケーションは、トークンの種類ごとに返される異なる省略可能な要求セットを構成できます。 アプリケーション エンティティの OptionalClaims プロパティは、OptionalClaims オブジェクトです。

**表 5: OptionalClaims 型のプロパティ**

| Name        | type                       | 説明                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | コレクション (OptionalClaim) | JWT ID トークンで返される省略可能な要求。     |
| `accessToken` | コレクション (OptionalClaim) | JWT アクセス トークンで返される省略可能な要求。 |
| `saml2Token`  | コレクション (OptionalClaim) | JWT SAML トークンで返される省略可能な要求。       |


### <a name="optionalclaim-type"></a>OptionalClaim 型

アプリケーションまたはサービス プリンシパルに関連付けられている省略可能な要求が含まれます。 [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) 型の idToken、accessToken、および saml2Token プロパティは、OptionalClaim のコレクションです。
特定の要求でサポートされている場合は、AdditionalProperties フィールドを使用して OptionalClaim の動作を変更することもできます。

**表 6: OptionalClaim 型のプロパティ**

| Name                 | type                    | 説明                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | 省略可能な要求の名前。                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | 要求のソース (ディレクトリ オブジェクト)。 定義済みの要求と、拡張プロパティのユーザー定義の要求があります。 ソース値が null の場合、この要求は定義済みの省略可能な要求です。 ソース値が user の場合、name プロパティの値はユーザー オブジェクトの拡張プロパティです。 |
| `essential`            | Edm.Boolean             | 値が true の場合、エンド ユーザーから要求された特定のタスクの承認エクスペリエンスを円滑にするために、クライアントに指定された要求が必要です。 既定値は false です。                                                                                                                 |
| `additionalProperties` | コレクション (Edm.String) | 要求の追加のプロパティ。 このコレクションにプロパティが存在する場合、name プロパティに指定された省略可能な要求の動作が変更されます。                                                                                                                                                   |

## <a name="configuring-custom-claims-via-directory-extensions"></a>ディレクトリ拡張機能を使用したカスタム要求の構成

標準の省略可能な要求セットに加え、ディレクトリ スキーマ拡張を含むようにトークンを構成することもできます (詳細については[ディレクトリ スキーマ拡張機能に関する記事](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)を参照してください)。  ユーザーが設定した追加の識別子や重要な構成オプションなど、アプリで使用できる追加のユーザー情報をアタッチする場合にこの機能が便利です。 

> [!Note]
> ディレクトリ スキーマ拡張機能は AAD のみの機能なので、アプリケーション マニフェストでカスタム拡張機能を必須にして、MSA ユーザーがアプリにログインした場合、このような拡張機能は返されません。 

### <a name="values-for-configuring-additional-optional-claims"></a>追加の省略可能な要求を構成するための値 

拡張属性の場合は、アプリケーション マニフェストで拡張機能のフル ネーム (`extension_<appid>_<attributename>` の形式) を使用します。 `<appid>` は、要求を必須にしているアプリケーションの ID と一致する必要があります。 

JWT 内では、このような要求は `extn.<attributename>` という形式の名前で発行されます。

SAML トークン内では、このような要求は `http://schemas.microsoft.com/identity/claims/extn.<attributename>` という URI 形式で発行されます。

## <a name="optional-claims-example"></a>省略可能な要求の例

このセクションでは、シナリオを使って、省略可能な要求機能をアプリケーションに使用する手順について説明します。
アプリケーションの ID 構成に関するプロパティを更新し、省略可能な要求を有効にして構成するには、複数のオプションがあります。
-   アプリケーション マニフェストを変更できます。 以下の例では、この方法を使用して構成を行います。 マニフェストの概要については、まず [Azure AD アプリケーション マニフェストの概要に関するドキュメント](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)を参照してください。
-   また、[Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) を使用してアプリケーションを更新するアプリケーションを作成することもできます。 省略可能な要求の構成については、Graph API リファレンス ガイドの[エンティティと複合型のリファレンス](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type)に関するページを参照してください。

**例:** 以下の例では、アプリケーションのマニフェストを変更して、アプリケーション用のアクセス トークン、ID トークン、および SAML トークンに要求を追加します。
1.  [Azure Portal](https://portal.azure.com) にサインインします。
2.  認証が完了したら、ページの右上隅から Azure AD テナントを選択します。
3.  左側のナビゲーション パネルで **Azure AD 拡張機能**を選択して、**[アプリの登録]** をクリックします。
4.  省略可能な要求を構成するアプリケーションを一覧から探し、クリックします。
5.  アプリケーションのページで **[マニフェスト]** をクリックして、インライン マニフェスト エディターを開きます。 
6.  このエディターを使用して、マニフェストを直接編集できます。 マニフェストは、[アプリケーション エンティティ](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)のスキーマに従っており、保存されるとマニフェストの書式が自動的に構成されます。 新しい要素が `OptionalClaims` プロパティに追加されます。

      ```json
      "optionalClaims": 
      {
            "idToken": [ 
                  { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                  }
            ],
      "accessToken": [ 
                  {
                        "name": "auth_time", 
                        "essential": false
                  }
            ],
      "saml2Token": [ 
                  { 
                        "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                        "source": "user", 
                        "essential": true
                  }
            ]
      }
      ```
      この例では、アプリケーションが受け取ることができる各種類のトークンに異なる省略可能な要求が追加されました。 ID トークンには、完全な形式 (`<upn>_<homedomain>#EXT#@<resourcedomain>`) のフェデレーション ユーザーの UPN が含まれるようになります。 アクセス トークンが auth_time 要求を受け取るようになります。 SAML トークンに skypeId ディレクトリ スキーマ拡張機能が含まれるようになります (この例では、このアプリのアプリ ID は ab603c56068041afb2f6832e2a17e237 です)。  SAML トークンは Skype ID を `extension_skypeId` として公開します。

7.  マニフェストの更新が完了したら、**[保存]** をクリックしてマニフェストを保存します。


## <a name="related-content"></a>関連コンテンツ
* Azure AD に用意されている[標準の要求](v1-id-and-access-tokens.md)について詳しく学びます。 
