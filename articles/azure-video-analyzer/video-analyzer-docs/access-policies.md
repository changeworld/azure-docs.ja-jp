---
title: Azure Video Analyzer のアクセス ポリシー
description: この記事では、Azure Video Analyzer のアクセス ポリシーで JWT トークンを使用してビデオをセキュリティで保護する方法について説明します。
ms.topic: reference
ms.date: 05/10/2021
ms.openlocfilehash: 4c0a4cf4ed02f6c1ddbcecd2351e8ffa0f4ad887
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110387248"
---
# <a name="access-policies"></a>アクセス ポリシー

アクセス ポリシーには、Video Analyzer の特定のビデオ リソースへのアクセス許可とアクセス期間を定義します。 これらのアクセス ポリシーにより、サードパーティ (AAD 以外のクライアント) の JWT トークンを使用して、クライアント API に承認を提供して以下を可能にすることで、より高い制御と柔軟性を実現できます。 

- ビデオ メタデータへのアクセス。 
- ビデオ ストリーミングへのアクセス。 

## <a name="access-policy-definition"></a>ポリシーの定義にアクセスする

```json
"name": "accesspolicyname1", 
"properties": { 
    "role": "Reader", 
    "authentication": { 
        "@type": "#Microsoft.VideoAnalyzer.JwtAuthentication", 
        "issuers": [ 
            "issuer1", 
            "issuer2" 
        ], 
        "audiences": [ 
            "audience1" 
        ], 
        "claims": [ 
            { 
                "name":"claimname1", 
                "value":"claimvalue1" 
            }, 
            { 
                "name":"claimname2", 
                "value":"claimvalue2" 
            } 
        ], 
        "keys": [ 
            { 
                "@type": "#Microsoft.VideoAnalyzer.RsaTokenKey", 
                "alg": "RS256", 
                "kid": "123", 
                "n": "YmFzZTY0IQ==", 
                "e": "ZLFzZTY0IQ==" 
            }, 
            { 
                "@type": "#Microsoft.VideoAnalyzer.EccTokenKey", 
                "alg": "ES256", 
                "kid": "124", 
                "x": "XX==", 
                "y": "YY==" 
            } 
        ] 
    } 
} 
```

> [!NOTE] 
> 必要なキーの種類は 1 つのみです。 

### <a name="roles"></a>ロール

現時点では、閲覧者ロールのみがサポートされています。

### <a name="issuer-matching-rules"></a>発行者照合ルール

ポリシーには複数の問題を指定でき、トークンに単一の発行者を指定できます。  発行者は、トークン発行者がポリシーで指定された発行者の中に含まれる場合に一致します。

### <a name="audience-matching-rules"></a>対象ユーザー照合ルール

ビデオ リソースの対象ユーザー値が ${System.Runtime.BaseResourceUrlPattern} の場合、JWT トークンで提供される対象ユーザーはベース リソース URL と一致する必要があります。 そうではない場合、トークンの対象ユーザーはアクセス ポリシーの対象ユーザーと一致する必要があります。

### <a name="claims-matching-rules"></a>要求照合ルール

アクセス ポリシーと JWT トークンでは、複数の要求を指定できます。  検証に合格するには、アクセス ポリシーからのすべての要求をトークンに指定する必要がありますが、JWT トークンには、アクセス ポリシーに記載されていない追加の要求を含めることができます。

### <a name="keys"></a>[キー]

RSA と ECC の 2 種類のキーがサポートされています。

[RSA](https://wikipedia.org/wiki/RSA_(cryptosystem))

* @type- \#Microsoft.VideoAnalyzer.RsaTokenKey
* alg - アルゴリズム。  256、384、または 512 を指定できます 
* kid - キー ID
* n - 剰余
* e - 公開指数 

[ECC](https://wikipedia.org/wiki/Elliptic-curve_cryptography)        

* @type- \#Microsoft.VideoAnalyzer.EccTokenKey
* alg - アルゴリズム。  256、384、または 512 を指定できます
* kid - キー ID
* x - 座標値。
* y - 座標値。

### <a name="token-validation-process"></a>トークン検証プロセス

お客様は独自の JWT トークンを作成する必要があり、次の方法を使用して検証されます。

- Microsoft が検証するキー ID と一致するポリシーの一覧から:
  - トークン署名
  - トークンの有効期限
  - 発行者
  - 対象ユーザー
  - その他の要求

### <a name="policy-audience-and-token-matching-examples"></a>ポリシーの対象ユーザーとトークン照合の例:

| **ポリシーの対象ユーザー**                      | 要求した URL                         | [トークンURL]                            | 結果 |
| ---------------------------------------- | ------------------------------------- | ------------------------------------ | ------ |
| (任意のリテラル)                            | (任意)                                 | (一致)                              | Grant  |
| (任意のリテラル)                            | (任意)                                 | (一致しない)                          | 拒否   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos                   | https://fqdn/videos/*                | Grant  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos                   | https://fqdn/videos/{videoName}      | 拒否   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/vid*                    | Grant  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/*                | Grant  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/{baseVideoName}* | Grant  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/{videoName}      | Grant  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}Suffix | https://fqdn/videos/{videoName}      | 拒否   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{otherVideoName}  | https://fqdn/videos/{videoName}      | 拒否   |

> [!NOTE]  
> Video Analyzer では、最大 20 のポリシーがサポートされます。  ${System.Runtime.BaseResourceUrlPattern} を使用すると、1 つのアクセス ポリシーと複数のトークンを使用して、特定のリソースにより柔軟にアクセスできます。  これらのトークンを使用すると、対象ユーザーに基づいて異なる Video Analyzer リソースにアクセスできます。 

## <a name="creating-an-access-policy"></a>アクセス ポリシーを作成する

アクセス ポリシーを作成する方法は 2 つあります。

### <a name="in-the-azure-portal"></a>Azure Portal で次の操作を行います。

1. Azure portal にログインし、ご自分の Video Analyzer アカウントがあるリソース グループに移動します。
2. Video Analyzer リソースを選択します。
3. Video Analyzer の [アクセス ポリシー] を選択します

   :::image type="content" source="./media/access-policies/access-policies-menu.png" alt-text="Azure portal の [アクセス ポリシー] メニュー":::
4. [新規] をクリックし、次を入力します。

   - アクセス ポリシー名 - 任意の名前
   - 発行者 - JWT トークン発行者と一致する必要があります 
   - 対象ユーザー - JWT トークンの対象ユーザー -- ${System.Runtime.BaseResourceUrlPattern} が既定値です。 
   - キーの種類 - kty 
   - アルゴリズム - alg
   - キー ID - kid 
   - N/X 値 
   - E/Y 値 

   :::image type="content" source="./media/access-policies/access-policies-portal.png" alt-text="Azure portal のアクセス ポリシー":::
5. [`Save`] をクリックします。

### <a name="create-access-policy-via-api"></a>アクセス ポリシーを API を使用して作成する

Azure Resource Manager (ARM) API を参照してください 

## <a name="next-steps"></a>次のステップ

[概要](overview.md)
