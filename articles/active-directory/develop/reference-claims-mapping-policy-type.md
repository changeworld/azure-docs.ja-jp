---
title: 要求のマッピング ポリシー
titleSuffix: Microsoft identity platform
description: 要求のマッピング ポリシーの種類について説明します。これは、特定のアプリケーション用に発行されたトークンに出力される要求を変更するために使用されます。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: reference
ms.date: 07/01/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 52de257461c796f7a477091801f4bdc52f46f5c8
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101534"
---
# <a name="claims-mapping-policy-type"></a>要求のマッピング ポリシーの種類

Azure AD では、**ポリシー** オブジェクトは、組織の個々のアプリケーションまたはすべてのアプリケーションに適用される規則のセットを表します。 それぞれのポリシーの種類は、割り当てられているオブジェクトに適用されるプロパティのセットを含む一意の構造体を持ちます。

要求のマッピング ポリシーは、**ポリシー** オブジェクトの一種であり、これで、特定のアプリケーションに対して発行された [トークンに出力される要求を変更](active-directory-claims-mapping.md)します。

## <a name="claim-sets"></a>要求セット

トークンで使用する方法とタイミングを定義する要求のセットがあります。

| 要求セット | 説明 |
|---|---|
| コア要求セット | ポリシーに関係なく、すべてのトークンに提示されます。 また、この要求は制限付きと見なされ、変更できません。 |
| 基本要求セット | コア要求セットの他にトークンに対して既定で出力される要求が含まれます。 要求のマッピング ポリシーを使用して、[基本要求を省略したり変更したりする](active-directory-claims-mapping.md#omit-the-basic-claims-from-tokens)ことができます。 |
| 制限付き要求セット | ポリシーを使用して変更することはできません。 データ ソースを変更できず、要求を生成するときに変換が適用されません。 |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>表 1:JSON Web トークン (JWT) 制限付き要求セット

| 要求の種類 (名前) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| actor |
| actortoken |
| aio |
| altsecid |
| amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| appid |
| appidacr |
| assertion |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| cnf |
| code |
| controls |
| credential_keys |
| csr |
| csr_type |
| deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| email |
| endpoint |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| graph |
| group_sids |
| groups |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| iat |
| identityprovider |
| idp |
| in_corp |
| instance |
| ipaddr |
| isbrowserhostedapp |
| iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid |
| nbf |
| netbios_name |
| nonce |
| oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| password |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| resource |
| role |
| roles |
| scope |
| scp |
| sid |
| signature |
| signin_state |
| src1 |
| src2 |
| sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| tid |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| upn |
| user_setting_sync_url |
| username |
| uti |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>表 2:SAML 制限付き要求セット

| 要求の種類 (URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>要求のマッピング ポリシーのプロパティ

出力される要求内容とデータの送信元を制御するには、要求のマッピング ポリシーのプロパティを使用します。 ポリシーが設定されていない場合は、アプリケーションが受信するように選んだコア要求セット、基本要求セット、[省略可能な要求](active-directory-optional-claims.md)を含むトークンが発行されます。

> [!NOTE]
> コア要求セット内の要求については、このプロパティの設定に関係なく、すべてのトークンに提示されます。

### <a name="include-basic-claim-set"></a>Include 基本要求セット

**文字列:** IncludeBasicClaimSet

**データ型**: ブール値 (True または False)

**概要:** このプロパティは、基本要求セットが、このポリシーの影響を受けるトークンに含まれるかどうかを判断します。

- True に設定されている場合、基本要求セット内のすべての要求が、ポリシーの影響を受けるトークンに出力されます。
- False に設定されている場合、基本要求セット内の要求は、同じポリシーの要求スキーマ プロパティに個別に追加されない限り、トークンに追加されません。



### <a name="claims-schema"></a>要求スキーマ

**文字列:** ClaimsSchema

**データ型**: 1 つ以上の要求スキーマ エントリを含む JSON BLOB

**概要:** このプロパティは、基本要求セットやコア要求セットのほか、ポリシーの影響を受けるトークンにどの要求を提示するかを定義します。
このプロパティで定義されている要求スキーマ エントリごとに、特定の情報が必要です。 データのソース (**Value**、**Source/ID ペア**、または **Source/ExtensionID ペア**) と、どの要求としてデータを出力するか (**要求の種類**) を指定します。

### <a name="claim-schema-entry-elements"></a>要求スキーマ エントリ要素

**値:** 値要素では、静的な値が、要求に出力されるデータとして定義されます。

**ソース/ID ペア:** ソース要素と ID 要素では、要求内のデータのソースが定義されます。

**Source/ExtensionID ペア:** Source と ExtensionID 要素は、要求内のデータのソースであるディレクトリ スキーマ拡張属性を定義します。 詳細については、「[要求でディレクトリ スキーマ拡張属性を使用する](active-directory-schema-extensions.md)」を参照してください。

ソース要素を次のいずれかの値に設定します。

- "user":要求のデータは、ユーザー オブジェクトのプロパティです。
- "application":要求のデータは、アプリケーション (クライアント) のサービス プリンシパルのプロパティです。
- "resource":要求のデータは、リソースのサービス プリンシパルのプロパティです。
- "audience":要求のデータは、トークン (クライアントかリソースのいずれかのサービス プリンシパル) の対象ユーザーであるサービス プリンシパルのプロパティです。
- "company":要求のデータは、リソース テナントの会社のオブジェクトのプロパティです。
- "transformation":要求のデータは、要求からの変換です (この記事の後述の「要求の変換」セクションを参照してください)。

ソースが transformation の場合、**TransformationID** 要素も、この要求の定義に含める必要があります。

ID 要素により、ソースのどのプロパティが要求の値を提供するかが特定されます。 次の表は、ソースの各値に対して有効な ID の値を示しています。

> [!WARNING]
> 現在、ユーザー オブジェクトで利用可能な複数値要求のソースは、AADConnect から同期された複数値の拡張属性のみです。  その他のプロパティ (OtherMails、タグなど) は複数値ではありますが、ソースとして選択されたときに生成される値は 1 つだけです。

#### <a name="table-3-valid-id-values-per-source"></a>表 3:ソースごとに有効な ID 値

| source | id | 説明 |
|-----|-----|-----|
| User | surname | surname |
| User | givenname | 名 |
| User | displayName | 表示名 |
| User | objectid | ObjectID |
| User | mail | 電子メール アドレス |
| User | userprincipalname | ユーザー プリンシパル名 |
| User | department|部署|
| User | onpremisessamaccountname | オンプレミスの SAM のアカウント名 |
| User | netbiosname| NetBios 名 |
| User | dnsdomainname | DNS ドメイン名 |
| User | onpremisesecurityidentifier | オンプレミスのセキュリティ ID |
| User | companyname| 組織名 |
| User | streetaddress | 番地 |
| User | postalcode | 郵便番号 |
| User | preferredlanguage | 優先言語 |
| User | onpremisesuserprincipalname | オンプレミスの UPN |
| User | mailNickname | メールのニックネーム |
| User | extensionattribute1 | 拡張属性 1 |
| User | extensionattribute2 | 拡張属性 2 |
| User | extensionattribute3 | 拡張属性 3 |
| User | extensionattribute4 | 拡張属性 4 |
| User | extensionattribute5 | 拡張属性 5 |
| User | extensionattribute6 | 拡張属性 6 |
| User | extensionattribute7 | 拡張属性 7 |
| User | extensionattribute8 | 拡張属性 8 |
| User | extensionattribute9 | 拡張属性 9 |
| User | extensionattribute10 | 拡張属性 10 |
| User | extensionattribute11 | 拡張属性 11 |
| User | extensionattribute12 | 拡張属性 12 |
| User | extensionattribute13 | 拡張属性 13 |
| User | extensionattribute14 | 拡張属性 14 |
| User | extensionattribute15 | 拡張属性 15 |
| User | othermail | その他のメール |
| User | country | 国/リージョン |
| User | city | City |
| User | state | State |
| User | jobtitle | 役職 |
| User | employeeid | 従業員 ID |
| User | facsimiletelephonenumber | ファックスの電話番号 |
| User | assignedroles | ユーザーに割り当てられたアプリ ロールの一覧|
| アプリケーション、リソース、対象ユーザー | displayName | 表示名 |
| アプリケーション、リソース、対象ユーザー | objectid | ObjectID |
| アプリケーション、リソース、対象ユーザー | tags | サービス プリンシパル タグ |
| [会社] | tenantcountry | テナントの国/リージョン |

**TransformationID:** TransformationID 要素は、ソース要素が "transformation" に設定されている場合にのみ指定する必要があります。

- この要素は、この要求のデータを生成する方法を定義する、**ClaimsTransformation** プロパティの変換エントリの ID 要素と一致する必要があります。

**要求の種類:** **JwtClaimType** 要素と **SamlClaimType** 要素は、この要求スキーマ エントリが、どの要求を参照するかを定義します。

- JwtClaimType には、JWT に出力する要求の名前を含める必要があります。
- SamlClaimType には、SAML トークンに出力する要求の URI を含める必要があります。

* **onPremisesUserPrincipalName 属性:** 代替 ID を使用する場合、オンプレミスの userPrincipalName 属性は Azure AD の onPremisesUserPrincipalName 属性と同期されます。 この属性は、代替 ID が構成されている場合にのみ使用できますが、MS Graph ベータ版 (https://graph.microsoft.com/beta/me/ ) から使用することもできます。

> [!NOTE]
> 制限付き要求セット内の要求の名前と URI を、要求の種類の要素に使用することはできません。 詳細については、この記事の後述の「例外と制限事項」セクションを参照してください。

### <a name="claims-transformation"></a>要求の変換

**文字列:** ClaimsTransformation

**データ型**: 1 つ以上の変換エントリを含む JSON BLOB

**概要:** このプロパティを使用して、共通の変換をソース データに適用し、要求スキーマで指定された要求の出力データを生成します。

**ID:** ID 要素を使用して、TransformationID 要求スキーマ エントリのこの変換エントリを参照します。 この値は、このポリシー内の変換エントリごとに一意である必要があります。

**TransformationMethod:** TransformationMethod 要素は、要求のデータを生成するときに実行される操作を特定します。

選択した方法に基づいて、一連の入力と出力が想定されます。 **InputClaims** 要素、**InputParameters** 要素、**OutputClaims** 要素を使用して入出力を定義します。

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>表 4:変換方法と想定される入出力

|TransformationMethod|想定される入力|想定される出力|説明|
|-----|-----|-----|-----|
|Join|string1, string2, separator|outputClaim|入力文字列の間に区切り記号を使用して、その文字列を結合します。 例: string1:"foo@bar.com" , string2:"sandbox" , separator:"." の結果は outputClaim:"foo@bar.com.sandbox" になります|
|ExtractMailPrefix|電子メールまたは UPN|抽出された文字列|ExtensionAttributes 1-15 または、UPN やユーザーのメール アドレス値 (johndoe@contoso.com など) を格納するその他のあらゆるスキーマ拡張。 メール アドレスのローカル部分を抽出します。 例: mail:"foo@bar.com" の結果は outputClaim:"foo" になります。 \@ 記号がない場合、元の入力文字列がそのまま返されます。|

**InputClaims:** InputClaims 要素を使用して、要求スキーマ エントリから変換にデータを渡します。 この要素には 2 つの属性があります。**ClaimTypeReferenceId** と **TransformationClaimType** です。

- **ClaimTypeReferenceId** は要求スキーマ エントリの ID 要素と結合され、該当する入力要求を検索します。
- **TransformationClaimType** は、この入力に一意の名前を指定するときに使用されます。 この名前は、変換方法に対する想定される入力のいずれかと一致する必要があります。

**InputParameters:** InputParameters 要素を使用して、定数値を変換に渡します。 この要素には 2 つの属性があります。**Value** と **ID** です。

- **Value** は、渡される実際の定数値です。
- **ID** は、入力に一意の名前を指定するときに使用されます。 この名前は、変換方法に対する想定される入力のいずれかと一致する必要があります。

**OutputClaims:** OutputClaims 要素を使用して、変換によって生成されたデータを保持し、そのデータを要求スキーマ エントリに関連付けます。 この要素には 2 つの属性があります。**ClaimTypeReferenceId** と **TransformationClaimType** です。

- **ClaimTypeReferenceId** は要求スキーマ エントリの ID と結合され、該当する出力要求を検索します。
- **TransformationClaimType** は、出力に一意の名前を指定するときに使用されます。 この名前は、変換方法に対する想定される出力のいずれかと一致する必要があります。

### <a name="exceptions-and-restrictions"></a>例外と制限事項

**SAML NameID と UPN:** NameID と UPN の値のソース属性と、許可される要求変換には、制限があります。 表 5 と表 6 を参照して、使用できる値を確認してください。

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>表 5:SAML NameID のデータ ソースとして許可されている属性

|source|id|説明|
|-----|-----|-----|
| User | mail|電子メール アドレス|
| User | userprincipalname|ユーザー プリンシパル名|
| User | onpremisessamaccountname|オンプレミスの Sam のアカウント名|
| User | employeeid|従業員 ID|
| User | extensionattribute1 | 拡張属性 1 |
| User | extensionattribute2 | 拡張属性 2 |
| User | extensionattribute3 | 拡張属性 3 |
| User | extensionattribute4 | 拡張属性 4 |
| User | extensionattribute5 | 拡張属性 5 |
| User | extensionattribute6 | 拡張属性 6 |
| User | extensionattribute7 | 拡張属性 7 |
| User | extensionattribute8 | 拡張属性 8 |
| User | extensionattribute9 | 拡張属性 9 |
| User | extensionattribute10 | 拡張属性 10 |
| User | extensionattribute11 | 拡張属性 11 |
| User | extensionattribute12 | 拡張属性 12 |
| User | extensionattribute13 | 拡張属性 13 |
| User | extensionattribute14 | 拡張属性 14 |
| User | extensionattribute15 | 拡張属性 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>表 6:SAML NameID の許可されている変換方法

| TransformationMethod | 制限 |
| ----- | ----- |
| ExtractMailPrefix | なし |
| Join | 結合されているサフィックスは、リソース テナントの確認済みドメインである必要があります。 |

## <a name="next-steps"></a>次の手順

- PowerShell を使用して、テナント内の特定のアプリケーションのトークンに出力される要求をカスタマイズする方法については、「[方法: テナント内の特定のアプリのトークンに出力される要求をカスタマイズする](active-directory-claims-mapping.md)」を参照してください
- SAML トークンで発行された要求を Azure portal でカスタマイズする方法については、「[方法: エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](active-directory-saml-claims-customization.md)」を参照してください。
- 拡張属性の詳細については、[要求でのディレクトリ スキーマ拡張属性の使用](active-directory-schema-extensions.md)に関するページをご覧ください。
