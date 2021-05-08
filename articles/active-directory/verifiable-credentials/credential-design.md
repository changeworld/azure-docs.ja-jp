---
title: Azure Active Directory Verifiable Credentials (プレビュー) をカスタマイズする方法
description: この記事では、独自の検証可能な資格情報を作成する方法について説明します
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: a43e734c0a5bfa7c3698dcde5cb5b17f15575d90
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222930"
---
# <a name="how-to-customize-your-verifiable-credentials-preview"></a>Verifiable Credentials (プレビュー) をカスタマイズする方法

検証可能な資格情報は、規則ファイルと表示ファイルの 2 つのコンポーネントで構成されます。 規則ファイルは、検証可能な資格情報を受け取る前にユーザーが提供する必要があるものを決定します。 表示ファイルは、資格情報のブランド化とクレームのスタイル設定を制御します。 このガイドでは、両方のファイルを変更して組織の要件を満たす方法について説明します。 

> [!IMPORTANT]
> Azure Active Directory Verifiable Credentials は、現在パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="rules-file-requirements-from-the-user"></a>規則ファイル: ユーザーからの要件

規則ファイルは、検証可能な資格情報の重要なプロパティを記述する単純な JSON ファイルです。 具体的には、検証可能な資格情報を設定するためにクレームがどのように使用されるかが記載されています。

規則ファイルで構成できる入力の種類は現在 3 つあります。 これらの種類は、検証可能な資格情報の発行サービスによって、検証可能な資格情報にクレームを挿入し、その情報を DID で証明するために使用されます。 次の 3 つの種類について説明します。

- ID トークン
- 検証可能なプレゼンテーションを使用した検証可能な資格情報。
- 自己証明クレーム

**ID トークン:** このサンプル アプリとチュートリアルでは、ID トークンを使用します。 このオプションが構成されている場合は、Open ID Connect 構成 URI を指定し、VC に含める必要があるクレームを含める必要があります。 ユーザーは、この要件を満たし、関連するクレームをアカウントから追加するために、Authenticator アプリに "サインイン" するよう求められます。 

**検証可能な資格情報:** 発行フローの最終的な結果として、検証可能な資格情報が生成されますが、これを発行するために検証可能な資格情報を提示するようにユーザーに要求することもできます。 規則ファイルは、提示された検証可能な資格情報から特定のクレームを取得し、組織から新しく発行された検証可能な資格情報にそれらのクレームを含めることができます。 

**自己証明クレーム:** このオプションを選択すると、ユーザーは直接 Authenticator に情報を入力できます。 現時点では、自己証明クレームでサポートされている唯一の入力は文字列です。 

![検証可能な資格情報カードの詳細ビュー](media/credential-design/issuance-doc.png) 

**静的クレーム:** また、規則ファイルで静的クレームを宣言することもできますが、この入力はユーザーからは取得されません。 発行者は規則ファイルで静的クレームを定義し、検証可能な資格情報の他のクレームと同様に表示されます。 単純に、vc.type の後に credentialSubject を追加し、属性とクレームを宣言します。 

```json
"vc": {
    "type": [ "StaticClaimCredential" ],
    "credentialSubject": {
      "staticClaim": true,
      "anotherClaim": "Your Claim Here"
    },
  }
}
```


## <a name="input-type-id-token"></a>入力の種類: ID トークン

ID トークンを入力として取得するには、規則ファイルで、OIDC と互換性のある ID システムの既知のエンドポイントを構成する必要があります。 そのシステムでは、[発行者サービスの通信の例](issuer-openid.md)に記載されている適切な情報を使用してアプリケーションを登録する必要があります。 また、client_id を規則ファイルに配置する必要があるほか、スコープ パラメーターに正しいスコープを入力する必要があります。 たとえば、ID トークンで電子メール クレームを返す場合、Azure Active Directory には電子メール スコープが必要です。
```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
            "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["https://schema.org/EducationalCredential", "https://schemas.ed.gov/universityDiploma2020", "https://schemas.contoso.edu/diploma2020" ]
      }
    }
```

| プロパティ | 説明 |
| -------- | ----------- |
| `attestations.idTokens` | ユーザー情報のソーシングでサポートされている OpenID Connect ID プロバイダーの配列。 |
| `...mapping` | 各 ID トークン内のクレームが、結果として得られる検証可能な資格情報の属性にどのようにマップされるかを記述するオブジェクト。 |
| `...mapping.{attribute-name}` | 結果として得られる検証可能な資格情報に設定される属性。 |
| `...mapping.{attribute-name}.claim` | 属性を設定するために使用する値を持つ ID トークン内のクレーム。 |
| `...configuration` | ID プロバイダーの構成ドキュメントの場所。 この URL は、[ID プロバイダー メタデータの OpenID Connect 標準](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata)に準拠している必要があります。 構成ドキュメントには `issuer`、`authorization_endpoint`、`token_endpoint`、および`jwks_uri` の各フィールドが含まれている必要があります。 |
| `...client_id` | クライアント登録プロセス中に取得されるクライアント ID。 |
| `...scope` | IDP が ID トークン内の正しいクレームを返すことができる必要があるスコープのスペース区切りリスト。 |
| `...redirect_uri` | 常に `vcclient://openid/` 値を使用する必要があります。 |
| `validityInterval` | 検証可能な資格情報の有効期間を表す秒単位の時間。 この期間が経過すると、検証可能な資格情報は有効ではなくなります。 この値を省略すると、各検証可能な資格情報は、明示的に失効するまで有効のままになります。 |
| `vc.type` | 検証可能な資格情報が満たすスキーマを示す文字列の配列。 詳細については、この後のセクションをご覧ください。 |

### <a name="vctype-choose-credential-types"></a>vc.type: 資格情報の種類を選択する 

検証可能なすべての資格情報は、規則ファイルで "type" を宣言する必要があります。 資格情報の種類によって、ユーザーの検証可能な資格情報が他の組織から発行された資格情報と区別され、発行者と検証者の間の相互運用性が確保されます。 資格情報の種類を示すには、資格情報が満たす 1 つまたは複数の資格情報の種類を指定する必要があります。 それぞれの種類は一意の文字列で表されます。多くの場合、グローバルな一意性を確保するために URI が使用されます。 URI はアドレス指定可能である必要はなく、文字列として扱われます。 

例として、Contoso 大学によって発行された卒業証書の資格情報は、次の型を宣言する場合があります。

| Type | 目的 |
| ---- | ------- |
| `https://schema.org/EducationalCredential` | Contoso 大学によって発行された卒業証書に、schema.org の `EducationaCredential` オブジェクトによって定義された属性が含まれていることを宣言します。 |
| `https://schemas.ed.gov/universityDiploma2020` | Contoso 大学によって発行された卒業証書に、米国教育省によって定義された属性が含まれていることを宣言します。 |
| `https://schemas.contoso.edu/diploma2020` | Contoso 大学によって発行された卒業証書に、Contoso 大学によって定義された属性が含まれていることを宣言します。 |

3 種類すべてを宣言することで、Contoso 大学の卒業証書を使用して、検証者からのさまざまな要求を満たすことができます。 銀行はユーザーに `EducationCredential` のセットを要求でき、要求を満たすために卒業証書を使用することができます。 しかし、Contoso 大学の同窓会は、`https://schemas.contoso.edu/diploma2020` の種類の資格情報を要求することができ、卒業証書もその要求を満たします。

資格情報の相互運用性を確保するために、関連する組織と密接に連携して、業界で使用する資格情報の種類、スキーマ、および URI を定義することをお勧めします。 多くの業界団体では、検証可能な資格情報の内容を定義するために転用できる正式文書の構造に関するガイダンスを提供しています。 また、資格情報の検証者と密接に連携して、検証者がどのような意図で、検証可能な資格情報を要求して使用するのかを理解する必要があります。

## <a name="input-type-verifiable-credential"></a>入力の種類: 検証可能な資格情報

>[!NOTE]
>検証可能な資格情報を要求する規則ファイルでは、資格情報の要求にプレゼンテーション交換形式は使用されません。 これは発行サービスが標準の資格情報マニフェストをサポートする場合に更新されます。 

```json
{
    "attestations": {
      "presentations": [
        {
          "mapping": {
            "first_name": {
              "claim": "$.vc.credentialSubject.firstName",
            },
            "last_name": {
              "claim": "$.vc.credentialSubject.lastName",
              "indexed": true
            }
          },
          "credentialType": "VerifiedCredentialNinja",
          "contracts": [
            "https://beta.did.msidentity.com/v1.0/3c32ed40-8a10-465b-8ba4-0b1e86882668/verifiableCredential/contracts/VerifiedCredentialNinja"
          ],
          "issuers": [
            {
              "iss": "did:ion:123"
            }
          ]
        }
      ]
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }
  ```

| プロパティ | 説明 |
| -------- | ----------- |
| `attestations.presentations` | 入力として要求される検証可能な資格情報の配列。 |
| `...mapping` | 提示されるそれぞれの検証可能な資格情報内のクレームが、結果として得られる検証可能な資格情報の属性にどのようにマップされるかを記述するオブジェクト。 |
| `...mapping.{attribute-name}` | 結果として得られる検証可能な資格情報に設定される属性。 |
| `...mapping.{attribute-name}.claim` | 属性を設定するために使用する値を持つ検証可能な資格情報内のクレーム。 |
| `...mapping.{attribute-name}.indexed` | 失効のために保存する目的で、検証可能な資格情報ごとに 1 つだけを有効にすることができます。 詳細については、[資格情報を失効させる方法に関する記事](how-to-issuer-revoke.md)を参照してください。 |
| `credentialType` | ユーザーに提示を求める検証可能な資格情報の credentialType。 |
| `contracts` | 検証可能な資格情報サービスのポータルにおけるコントラクトの URI。 |
| `issuers.iss` | ユーザーに求める検証可能な資格情報の発行者 DID。 |
| `validityInterval` | 検証可能な資格情報の有効期間を表す秒単位の時間。 この期間が経過すると、検証可能な資格情報は有効ではなくなります。 この値を省略すると、各検証可能な資格情報は、明示的に失効するまで有効のままになります。 |
| `vc.type` | 検証可能な資格情報が満たすスキーマを示す文字列の配列。 |


## <a name="input-type-self-attested-claims"></a>入力の種類: 自己証明クレーム

発行フロー中に、ユーザーが一部の自己証明情報を入力するように求められることがあります。 現時点では、入力の種類は 'string' だけです。 
```json
{
  "attestations": {
    "selfIssued": {
      "mapping": {
        "alias": {
          "claim": "name"
        }
      },
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }



```
| プロパティ | 説明 |
| -------- | ----------- |
| `attestations.selfIssued` | ユーザーからの入力を必要とする自己発行のクレームの配列。 |
| `...mapping` | 自己発行のクレームが、結果として得られる検証可能な資格情報の属性にどのようにマップされるかを記述するオブジェクト。 |
| `...mapping.alias` | 結果として得られる検証可能な資格情報に設定される属性。 |
| `...mapping.alias.claim` | 属性を設定するために使用する値を持つ検証可能な資格情報内のクレーム。 |
| `validityInterval` | 検証可能な資格情報の有効期間を表す秒単位の時間。 この期間が経過すると、検証可能な資格情報は有効ではなくなります。 この値を省略すると、各検証可能な資格情報は、明示的に失効するまで有効のままになります。 |
| `vc.type` | 検証可能な資格情報が満たすスキーマを示す文字列の配列。 |


## <a name="display-file-verifiable-credentials-in-microsoft-authenticator"></a>表示ファイル: Microsoft Authenticator での検証可能な資格情報

検証可能な資格情報では、ブランドを反映するために使用できる限定されたオプションのセットが提供されます。 この記事では、資格情報をカスタマイズする方法と、ユーザーに発行された際に見栄えの良い資格情報を設計するためのベスト プラクティスについて説明します。

ユーザーに発行された検証可能な資格情報は、Microsoft Authenticator にカードとして表示されます。 管理者は、組織のブランドに合わせてカードの色、アイコン、テキスト文字列を選択できます。

![発行ドキュメント](media/credential-design/detailed-view.png) 

カードにはカスタマイズ可能なフィールドも含まれています。このフィールドを使用すると、カードの目的や、カードに含まれる属性などをユーザーに知らせることができます。

## <a name="create-a-credential-display-file"></a>資格情報の表示ファイルを作成する

表示ファイルは規則ファイルとよく似ており、検証可能な資格情報の内容を Microsoft Authenticator アプリにどのように表示するかを説明する単純な JSON ファイルです。 

>[!NOTE]
> 現時点では、この表示モデルは Microsoft Authenticator によってのみ使用されます。

表示ファイルの構造は次のとおりです。

```json
{
    "default": {
      "locale": "en-US",
      "card": {
        "title": "University Graduate",
        "issuedBy": "Contoso University",
        "backgroundColor": "#212121",
        "textColor": "#FFFFFF",
        "logo": {
          "uri": "https://contoso.edu/images/logo.png",
          "description": "Contoso University Logo"
        },
        "description": "This digital diploma is issued to students and alumni of Contoso University."
      },
      "consent": {
        "title": "Do you want to get your digital diploma from Contoso U?",
        "instructions": "Please log in with your Contoso U account to receive your digital diploma."
      },
      "claims": {
        "vc.credentialSubject.name": {
          "type": "String",
          "label": "Name"
        }
      }
    }
}
```

| プロパティ | 説明 |
| -------- | ----------- |
| `locale` | 検証可能な資格情報の言語。 将来利用するために予約されています。 | 
| `card.title` | ユーザーに対する資格情報の種類を表示します。 推奨される最大の長さは 25 文字です。 | 
| `card.issuedBy` | ユーザーに対する発行元組織の名前を表示します。 推奨される最大の長さは 40 文字です。 |
| `card.backgroundColor` | カードの背景色を 16 進形式で決定します。 すべてのカードには、かすかなグラデーションが適用されます。 |
| `card.textColor` | カードのテキストの色を 16 進形式で決定します。 黒または白を使用することをお勧めします。 |
| `card.logo` | カードに表示されるロゴ。 指定された URL は、パブリックにアドレス指定可能である必要があります。 電話のサイズに関係なく、推奨される最大の高さは 36 px で、最大の幅は 100 px です。 背景が透明の PNG を推奨します。 | 
| `card.description` | 各カードの横に表示される補助テキスト。 あらゆる目的で使用できます。 推奨される最大の長さは 100 文字です。 |
| `consent.title` | カードの発行時に表示される補足テキスト。 発行プロセスに関する詳細を説明するために使用されます。 推奨される長さは 100 文字です。 |
| `consent.instructions` | カードの発行時に表示される補足テキスト。 発行プロセスに関する詳細を説明するために使用されます。 推奨される長さは 100 文字です。 |
| `claims` | 各資格情報に含まれる属性のラベルを指定できます。 |
| `claims.{attribute}` | ラベルが適用される資格情報の属性を示します。 |
| `claims.{attribute}.type` | 属性の種類を示します。 現時点では、'String' のみがサポートされています。 |
| `claims.{attribute}.label` | 属性のラベルとして使用する必要がある値。これは Authenticator に表示されます。 これは、規則ファイルで使用されたラベルとは異なる可能性があります。 推奨される最大の長さは 40 文字です。 |

>[!NOTE]
  >クレームが規則ファイルに含まれていて、表示ファイルで省略されている場合は、2 つの異なる種類のエクスペリエンスがあります。 iOS では、クレームは上の図で示す詳細セクションに表示されませんが、Android では、クレームは表示されます。  

## <a name="next-steps"></a>次の手順

これで、検証可能な資格情報の設計と、ニーズに合わせて独自の資格情報を作成する方法に関する理解が深まりました。

- [発行者サービスの通信の例](issuer-openid.md)
