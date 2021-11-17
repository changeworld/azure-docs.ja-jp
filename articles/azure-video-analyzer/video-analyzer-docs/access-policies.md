---
title: Azure Video Analyzer のアクセス ポリシー
description: この記事では、Azure Video Analyzer のアクセス ポリシーで JWT トークンを使用してビデオをセキュリティで保護する方法について説明します。
ms.topic: reference
ms.date: 11/04/2021
ms.openlocfilehash: 9d9a0f4db83d132fae5bafb2a25d25075296b4ad
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486136"
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

## <a name="creating-a-token"></a>トークンを作成する

このセクションでは、この記事で後ほど使用する JWT トークンを作成します。  JWT トークンを生成し、アクセス ポリシーを作成するために必要なすべてのフィールドを提供するサンプル アプリケーションを使用します。

> [!NOTE] 
> RSA または ECC 証明書に基づいて JWT トークンを生成する方法についてよく理解している場合は、このセクションを省略できます。

1. [AVA C# サンプル リポジトリ](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp)を複製します。 次に、JWTTokenIssuer アプリケーション フォルダー *src/jwt-token-issuer* に移動して、JWTTokenIssuer アプリケーションを見つけます。
2. Visual Studio Code を開き、JWTTokenIssuer アプリケーションをダウンロードしたフォルダーに移動します。 このフォルダーには、 *\*.csproj* ファイルが含まれています。
3. エクスプローラー ペインで *program.cs* ファイルに移動します。
4. 77 行目で、対象ユーザーをお使いの Video Analyzer エンドポイントに変更し、/videos/\* と続けます。その結果、次のようになります。

   ```
   https://{Azure Video Analyzer Account ID}.api.{Azure Long Region Code}.videoanalyzer.azure.net/videos/*
   ```

   > [!NOTE] 
   > Video Analyzer エンドポイントは、Azure portal の Video Analyzer リソースの概要セクションで確認できます。

   :::image type="content" source="media/player-widget/client-api-url.png" alt-text="プレーヤー ウィジェットのエンドポイントを示すスクリーンショット。":::
    
5. 行 78 で、発行者を証明書の発行者の値に変更します。 例: `https://contoso.com`
6. ファイルを保存します。    

   > [!NOTE]
   > 「`Required assets to build and debug are missing from 'jwt token issuer'. Add them?` Select `Yes`」というメッセージが表示される場合があります。
   
   :::image type="content" source="media/player-widget/visual-studio-code-required-assets.png" alt-text="必要なアセット示す Visual Studio Code のプロンプトのスクリーンショット。":::
   
7. コマンド プロンプト ウィンドウを開き、JWTTokenIssuer ファイルが格納されているフォルダーに移動します。 `dotnet build` と `dotnet run` の 2 つのコマンドを続けて実行します。 Visual Studio Code に C# の拡張機能がある場合は、F5 キーを押して JWTTokenIssuer アプリケーションを実行することもできます。

アプリケーションがビルドされ、実行されます。 ビルドが完了すると、自己署名証明書が作成され、その証明書から JWT トークンの情報が生成されます。 JWTTokenIssuer がビルドされたディレクトリのデバッグ フォルダーにある JWTTokenIssuer.exe ファイルを実行することもできます。 アプリケーションを実行するメリットは、次のように入力オプションを指定できるという点です。

- `JwtTokenIssuer [--audience=<audience>] [--issuer=<issuer>] [--expiration=<expiration>] [--certificatePath=<filepath> --certificatePassword=<password>]`

JWTTokenIssuer によって、JWT トークンと、次の必要なコンポーネントが作成されます。

- `Issuer`, `Audience`, `Key Type`, `Algorithm`, `Key Id`, `RSA Key Modulus`, `RSA Key Exponent`, `Token`

後で使用するためにこれらの値をコピーしておいてください。


## <a name="creating-an-access-policy"></a>アクセス ポリシーを作成する

アクセス ポリシーを作成する方法は 2 つあります。

### <a name="in-the-azure-portal"></a>Azure Portal で次の操作を行います。

1. Azure portal にサインインし、ご自身の Video Analyzer アカウントがあるリソース グループに移動します。
1. Video Analyzer リソースを選択します。
1. **Video Analyzer** の **[アクセス ポリシー]** を選択します。

   :::image type="content" source="./media/player-widget/portal-access-policies.png" alt-text="プレーヤー ウィジェット - ポータル アクセス ポリシー。":::
   
1. **[新規]** を選択し、次の情報を入力します。

   > [!NOTE] 
   > これらの値は、前の手順で作成した JWTTokenIssuer アプリケーションから取得されます。

   - アクセス ポリシー名 - 任意の名前

   - 発行者 - JWT トークン発行者と一致する必要があります 

   - 対象ユーザー - JWT トークンの対象ユーザー - `${System.Runtime.BaseResourceUrlPattern}` が既定値です。

   - キーの種類 - RSA 

   - アルゴリズム - サポートされる値は RS256、RS384、RS512 です

   - キー ID - 証明書から生成されます。 詳細については、「[トークンを作成する](#creating-a-token)」を参照してください。

   - RSA キー モジュラス - 証明書から生成されます。 詳細については、「[トークンを作成する](#creating-a-token)」を参照してください。

   - RSA キー指数 - 証明書から生成されます。 詳細については、「[トークンを作成する](#creating-a-token)」を参照してください。

   :::image type="content" source="./media/player-widget/access-policies-portal.png" alt-text="プレーヤー ウィジェット - ポータルのアクセス ポリシー"::: 
   
1. **[保存]** を選択します。
### <a name="create-access-policy-via-api"></a>アクセス ポリシーを API を使用して作成する

Azure Resource Manager (ARM) API を参照してください 

## <a name="next-steps"></a>次のステップ

[概要](overview.md)
