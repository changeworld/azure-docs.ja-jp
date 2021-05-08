---
title: ドメインを分散識別子 (DID) (プレビュー) にリンクする - Azure Active Directory Verifiable Credentials
description: DNS バインドの方法について学習しますか?
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: ad5bb6e45479b4cccfa0b002427066439135e468
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588447"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did"></a>ドメインを分散識別子 (DID) にリンクする

> [!IMPORTANT]
> Azure Active Directory Verifiable Credentials は、現在パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事の内容:
> [!div class="checklist"]
> * ドメインに DID をリンクする必要があるのはなぜですか?
> * DID とドメインをリンクするにはどうすればよいですか?
> * ドメインのリンク プロセスのしくみはどのようなものですか?
> * 検証/未検証ドメイン ロジックのしくみはどのようなものですか?

## <a name="prerequisites"></a>前提条件

ドメインに DID をリンクするには、次の作業を完了している必要があります。

- [作業の開始](get-started-verifiable-credentials.md)と以降の[チュートリアル セット](enable-your-tenant-verifiable-credentials.md)を完了する。

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>ドメインに DID をリンクする必要があるのはなぜですか?

DID は、既存のシステムに固定されていない識別子として始まります。 DID は、ユーザーまたは組織が所有して制御することができるため便利です。 組織とやり取りしているエンティティで DID の所属 ''先''が認識されていない場合、DID は役に立ちません。

DID をドメインにリンクすることで、最初の信頼問題が解決されます。これにより、任意のエンティティで、DID とドメインの間の関係を暗号で検証できるようになります。

## <a name="how-do-we-link-dids-and-domains"></a>DID とドメインをリンクするにはどうすればよいですか?

ドメインと DID の間のリンクを作成するには、[既知の DID 構成](https://identity.foundation/.well-known/resources/did-configuration/)という、Decentralized Identity Foundation によって記述されたオープン標準を実装します。 Azure Active Directory (Azure AD) の検証可能な資格情報サービスは、組織が、DID に指定されたドメイン情報を含め、既知の構成ファイルを生成することで、DID とドメインの間のリンクを作成するのに役立ちます。

1. Azure AD により、組織のセットアップ時に指定されたドメイン情報を使用して、DID ドキュメント内にサービス エンドポイントを書き込まれます。 DID を操作するすべてのパーティは、DID により宣言されるドメインが関連付けられることを確認できます。  

    ```json
        "service": [
          {
            "id": "#linkeddomains",
            "type": "LinkedDomains",
            "serviceEndpoint": {
              "origins": [
                "https://www.contoso.com/"
              ]
            }
          }
    ```

2. Azure AD の Verifiable Credentials サービスによって、ドメインでホストできる準拠している既知の構成リソースが生成されます。 構成ファイルには、ドメインの原点がある DID で署名された credentialType ''DomainLinkageCredential'' の自己発行された検証可能な資格情報が含まれています。 ルート ドメインの URL に格納されている構成ドキュメントの例を以下に示します。


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

既知の構成ファイルを作成した後、検証可能な資格情報を AAD で有効にするときに指定したドメイン名を使って、ファイルを使用できるようにする必要があります。

* 既知の構成ファイルをドメインのルートでホストします。
* リダイレクトは使用しないでください。
* https を使用して構成ファイルを配布します。

>[!IMPORTANT]
>Microsoft Authenticator ではリダイレクトが考慮されません。指定された URL は最終的な送信先 URL である必要があります。

## <a name="user-experience"></a>ユーザー エクスペリエンス 

ユーザーは、発行フローを通過するか、検証可能な資格情報を提示するときに、組織とその DID についてある程度知っておく必要があります。 ドメインの場合、検証可能な資格情報ウォレットである Microsoft Authenticator により、DID と、その DID ドキュメント内のドメインとの関係が検証され、結果に応じて 2 つの異なるエクスペリエンスがユーザーに提示されます。

## <a name="verified-domain"></a>確認済みドメイン

Microsoft Authenticator で **[検証済み]** アイコンが表示される前に、次のいくつかの事項に該当する必要があります。

* SIOP (Self-Issued Open ID) 要求に署名する DID に、リンク ドメインのサービス エンドポイントが必要である。
* ルート ドメインでリダイレクトを使用せず、https を使用する。
* DID ドキュメントに一覧表示されているドメインに、解決可能な既知のリソースがある。
* 既知のリソースの検証可能な資格情報は、Microsoft Authenticator によってフローの開始に使用された SIOP の署名に使用されたものと同じ DID で署名される。

上記のすべてに該当する場合は、Microsoft Authenticator に検証済みのページが表示され、検証されたドメインが含まれます。

![新しいアクセス許可の要求](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>未検証のドメイン

上記のいずれかに該当しない場合、Microsoft Authenticator では、ドメインが未検証であること、ユーザーが危険なトランザクションの途中にあり、慎重に進める必要があることをユーザーに示す警告がページ全体に表示されます。 次の理由により、このルートを選びました。

* DID がいずれもドメインに固定されていない。
* 構成が正しく設定されていない。
* ユーザーが操作している DID が悪意のあるものであり、実際にドメインを所有していることを証明できない (実際にそうではないため)。 この最後の点により、警告メッセージの伝達を避けるために、ユーザーが使い慣れているドメインに DID をリンクすることが不可欠である。

![[資格情報の追加] 画面の未検証ドメインに関する警告](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>既知の構成を配布する

1. Verifiable Credentials の [設定] ページに移動し、 **[Verify this domain]\(このドメインを検証する\)** を選択します

   ![[設定] の [Verify this domain]\(このドメインを検証する\)](media/how-to-dnsbind/settings-verify.png) 

2. 下の画像に示されているように did-configuration.json ファイルをダウンロードします。

   ![既知の構成をダウンロードする](media/how-to-dnsbind/verify-download.png) 

3. JWT をコピーし、[jwt.ms](https://www.jwt.ms) を開いて、ドメインが正しいことを検証します。

4. DID をコピーし、[ION Network Explorer](https://identity.foundation/ion/explorer) を開いて、同じドメインが DID ドキュメントに含まれていることを検証します。 

5. 指定された場所で既知の構成リソースをホストします。 例: `https://www.example.com/.well-known/did-configuration.json`

6. 検証のために Microsoft Authenticator による発行または提示をテストします。 Authenticator の [安全でないアプリについて警告する] の設定がオンになっていることを確認します。

>[!NOTE]
>既定では、[安全でないアプリについて警告する] がオンになっています。

お疲れ様でした。これで、DID との信頼関係がある Web がブートストラップされました。

## <a name="next-steps"></a>次の手順

オンボード中に、変更することにした間違ったドメイン情報を入力した場合は、[オプトアウト](how-to-opt-out.md)が必要になります。現時点では、DID ドキュメントの更新はサポートされていません。 オプトアウトしてから再びオプトインすると、新しい DID が作成されます。
