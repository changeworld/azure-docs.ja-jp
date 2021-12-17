---
title: Azure API Management インスタンスのカスタム ドメイン名を構成する
titleSuffix: Azure API Management
description: このトピックでは、Azure API Management インスタンスのカスタム ドメイン名を構成する方法について説明します。
services: api-management
documentationcenter: ''
author: dlepow
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/24/2021
ms.author: danlep
ms.openlocfilehash: 4cdd269c0acb7568695cbc63b47422ad9b42085b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598767"
---
# <a name="configure-a-custom-domain-name-for-your-azure-api-management-instance"></a>Azure API Management インスタンスのカスタム ドメイン名を構成する

Azure API Management サービス インスタンスを作成すると、Azure によってそれに `azure-api.net` サブドメイン (例: `apim-service-name.azure-api.net`) が割り当てられます。 また、独自のカスタム ドメイン名 (例: **`contoso.com`** ) を使用して API Management エンドポイントを公開することもできます。 このチュートリアルでは、既存のカスタム DNS 名を、API Management インスタンスによって公開されるエンドポイントにマップする方法を説明します。

> [!IMPORTANT]
> API Management は、[ホスト ヘッダー](https://tools.ietf.org/html/rfc2616#section-14.23)の値が一致する要求のみを受け入れます。
>
>* 既定のドメイン名
>* 任意の構成済みカスタム ドメイン名

> [!WARNING]
> 証明書のピン留めを使用してアプリケーションのセキュリティを強化したい場合は、既定の証明書ではなく、カスタム ドメイン名と証明書を使用して自分で管理する必要があります。 既定の証明書をピン留めすると、ユーザーには管理できない証明書のプロパティに強く依存するようになるので、お勧めしません。

## <a name="prerequisites"></a>前提条件

-   有効な Azure サブスクリプション [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
-   API Management インスタンス。 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。
-   自分または自分の所属する組織が所有しているカスタム ドメイン名。 このトピックでは、カスタム ドメイン名を取得する手順は説明しません。
-   [DNS サーバーでホストされている CNAME レコード](#dns-configuration)。カスタム ドメイン名を API Management インスタンスの既定のドメイン名にマップします。 このトピックでは、CNAME レコードをホストする手順については説明しません。
-   有効な証明書と公開キーおよび秘密キー (.PFX)。 サブジェクトやサブジェクトの別名 (SAN) は、ドメイン名と一致している必要があります (これによって API Management インスタンスでは、TLS を通して URL を安全に公開することができます)。

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Azure ポータルを使用してカスタム ドメイン名を設定する

1. [Azure portal](https://portal.azure.com/) で API Management インスタンスに移動します。
1. **[カスタム ドメイン]** をクリックします。

    カスタム ドメイン名を割り当てることができるエンドポイントは多数あります。 現時点では、次のエンドポイントを利用できます。

    | エンドポイント | Default |
    | -------- | ----------- |
    | **ゲートウェイ** | 既定値: `<apim-service-name>.azure-api.net`。 従量課金レベルの構成で利用できるエンドポイントはゲートウェイだけです。 |
    | **開発者ポータル (レガシ)** | 既定値: `<apim-service-name>.portal.azure-api.net` |
    | **開発者ポータル** | 既定値: `<apim-service-name>.developer.azure-api.net` |
    | **管理** | 既定値: `<apim-service-name>.management.azure-api.net` |
    | **SCM** | 既定値: `<apim-service-name>.scm.azure-api.net` |

    > [!NOTE]
    > どのエンドポイントも更新できます。 通常、お客様は、**ゲートウェイ** (この URL は、API Management を通じて公開される API を呼び出すために使用されます) と **ポータル** (開発者ポータルの URL) を更新します。
    > 
    > **管理** と **SCM** のエンドポイントを内部的に使用できるのは、API Management インスタンスの所有者のみです。 これらのエンドポイントにカスタム ドメイン名を割り当てることはあまりありません。
    >
    > **Premium** レベルと **Developer** レベルでは、**ゲートウェイ** エンドポイントに対する複数のホスト名の設定がサポートされます。

1. **[+ 追加]** を選択するか、更新する既存のエンドポイントを選択します。
1. 右側のウィンドウで、カスタム ドメインのエンドポイントの **[種類]** を選択します。
1. **[ホスト名]** フィールドで、使用する名前を指定します。 たとえば、「 `api.contoso.com` 」のように入力します。
1. **[証明書]** で、 **[キー コンテナー]** または **[カスタム]** を選択します。
    - **Key Vault**
        - **[選択]** をクリックします。
        - ドロップダウン リストから、 **[サブスクリプション]** を選択します。
        - ドロップダウン リストから、 **[キー コンテナー]** を選択します。
        - 証明書が読み込まれたら、ドロップダウン リストから **[証明書]** を選択します。
        - **[選択]** をクリックします。
    - **Custom**
        - **[証明書ファイル]** フィールドを選択し、証明書を選択してアップロードします。
        - 証明書がパスワードで保護されている場合は、有効な .PFX ファイルをアップロードし、その **[パスワード]** を指定します。
1. ゲートウェイ エンドポイントを構成する場合は、[必要に応じて他のオプション](#clients-calling-with-server-name-indication-sni-header)を選択または選択解除します ( **[クライアント証明書のネゴシエート]** や **[既定の SSL バインディング]** など)。
1. **[更新]** を選択します。

    > [!NOTE]
    > ワイルドカードを使用したドメイン名 (`*.contoso.com` など) は、従量課金レベル以外のすべてのレベルでサポートされています。

    > [!TIP]
    > [Azure Key Vault を使用して証明書を管理](../key-vault/certificates/about-certificates.md)し、それらを `autorenew` に設定することをお勧めします。
    >
    > Azure Key Vault を使用してカスタム ドメインの TLS/SSL 証明書を管理する場合は、証明書が、_シークレット_ ではなく [_証明書_ として](/rest/api/keyvault/createcertificate/createcertificate) Key Vault に挿入されていることを確認してください。
    >
    > TLS/SSL 証明書を取得するには、証明書が格納されている Azure Key Vault に対するシークレットの一覧表示および取得のアクセス許可が、API Management に付与されている必要があります。 
    >
    >* Azure portal を使用すると、必要なすべての構成手順が自動的に行われます。 
    >* コマンド ライン ツールまたは管理 API を使用するときは、これらのアクセス許可を手動で付与する必要があります (2 ステップ)。
    >    * API Management インスタンスの **[マネージド ID]** ページを使用して、マネージド ID が有効になっていることを確認し、そのページのプリンシパル ID を記録しておきます。 
    >    * このプリンシパル ID に、証明書が格納されている Azure Key Vault に対する一覧表示とシークレット取得のアクセス許可を付与します。
    >
    > 証明書が `autorenew` に設定されていて、API Management のレベルに (つまり、Developer レベルを除くすべてのレベルに) SLA がある場合は、API Management によって、サービスにダウンタイムを発生させることなく、最新バージョンが自動的に取得されます。

1. [適用] をクリックします。

    > [!NOTE]
    > 証明書を割り当てる処理は、デプロイのサイズによっては、15 分以上かかる場合があります。 Developer SKU にはダウンタイムがありますが、Basic 以上の SKU にはありません。

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS の構成

カスタム ドメイン名の DNS を構成するときは、次のいずれかをできます。

-   構成したカスタム ドメイン名のエンドポイントを指す CNAME レコードを構成する。
-   API Management ゲートウェイの IP アドレスを指す A レコードを構成する。

CNAME レコード (またはエイリアス レコード) と A レコードのどちらを使用しても、ドメイン名を特定のサーバーまたはサービスに関連付けることができますが、機能は異なります。 

### <a name="cname-or-alias-record"></a>CNAME レコードまたはエイリアス レコード
CNAME レコードの場合は、"*特定の*" ドメイン (`contoso.com` や www\.contoso.com など) が正規のドメイン名にマップされます。 作成すると、CNAME によってドメインのエイリアスが作成されます。 CNAME エントリはカスタム ドメイン サービスの IP アドレスに自動的に解決されるため、IP アドレスが変更されても、何もする必要はありません。

> [!NOTE]
> 一部のドメイン レジストラーでは、CNAME レコードを使用する場合、サブドメイン (www\.contoso.com など) だけをマップすることができ、ルート名 (contoso.com など) はマップできません。 CNAME レコードの詳細については、レジストラーが提供するドキュメント、[CNAME レコードに関するウィキペディアの項目](https://en.wikipedia.org/wiki/CNAME_record)、または [IETF ドメイン名 - 実装と仕様書](https://tools.ietf.org/html/rfc1035)に関するドキュメントを参照してください。

### <a name="a-record"></a>A レコード
A レコードの場合は、ドメイン (`contoso.com` や **www\.contoso.com** など) "*またはワイルドカード ドメイン*" ( **\*.contoso.com** など) が、IP アドレスにマップされます。 A レコードは静的 IP アドレスにマップされるため、変更を IP アドレスに自動的に解決することはできません。 A レコードではなく、より安定した CNAME レコードを使用することをお勧めします。

> [!NOTE]
> API Management インスタンスの IP アドレスは静的ですが、いくつかのシナリオで変更される可能性があります。 DNS の構成方法を選択する場合、カスタム ドメインを構成するときに CNAME レコードを使用することをお勧めします。その方が、IP が変更される場合に A レコードより安定しています。 詳細については、[IP に関するドキュメント記事](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses)と [API Management の FAQ](./api-management-faq.yml#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services-) を参照してください。

## <a name="next-steps"></a>次のステップ

[サービスのアップグレードとスケーリングを行う](upgrade-and-scale.md)