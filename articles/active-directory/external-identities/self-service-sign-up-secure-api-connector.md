---
title: Azure AD のセルフサービス サインアップのユーザー フローで API コネクタとして使用される API をセキュリティで保護する
description: セルフサービス サインアップのユーザー フローで API コネクタとして使用されるカスタム RESTful API をセキュリティで保護します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 07/16/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9086e3df2022822e890af4d187f35a55bad5ae03
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074116"
---
# <a name="secure-your-api-used-an-api-connector-in-azure-ad-external-identities-self-service-sign-up-user-flows"></a>Azure AD External Identities セルフサービス サインアップのユーザー フローで API コネクタとして使用される API をセキュリティで保護する

Azure AD External Identities セルフサービス サインアップのユーザー フロー内で REST API を統合する場合は、認証を使用して REST API エンドポイントを保護する必要があります。 REST API 認証により、Azure AD などの適切な資格情報を備えたサービスだけが REST API エンドポイントを呼び出すことができます。 この記事では、REST API をセキュリティで保護する方法について説明します。 

## <a name="prerequisites"></a>必須コンポーネント
チュートリアル「[API コネクタをサインアップ ユーザー フローに追加する](self-service-sign-up-add-api-connector.md)」ガイドの手順を完了している。

API エンドポイントを保護するには、HTTP 基本認証または HTTPS クライアント証明書認証を使用します。 どちらの場合も、API エンドポイントを呼び出すときに Azure AD によって使用される資格情報を指定します。 次に、API エンドポイントは資格情報を確認し、承認の決定を行います。


## <a name="http-basic-authentication"></a>HTTP 基本認証

HTTP 基本認証は [RFC 2617](https://tools.ietf.org/html/rfc2617) で定義されています。 基本認証は、次のように動作します。Azure AD によって、クライアントの資格情報 (`username` と `password`) が `Authorization` ヘッダーに含まれた HTTP 要求が送信されます。 資格情報は、Base64 でエンコードされた文字列 `username:password` として書式設定されます。 その後、お使いの API によってこれらの値がチェックされ、他の承認決定が実行されます。

HTTP 基本認証を使用して API コネクタを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[Azure サービス]** で、 **[Azure AD]** を選択します。
3. **[API コネクタ]** を選択し、構成する **[API コネクタ]** を選択します。
4. **[認証の種類]** で、 **[基本]** を選択します。
5. REST API エンドポイントの **[ユーザー名]** と **[パスワード]** を指定します。
    :::image type="content" source="media/secure-api-connector/api-connector-config.png" alt-text="API コネクタの基本的な認証構成を提供する。":::
6. **[保存]** を選択します。

## <a name="https-client-certificate-authentication"></a>HTTPS クライアント証明書認証

クライアント証明書の認証は証明書ベースの相互認証であり、クライアントである Azure AD がクライアント証明書をサーバーに提供し、その ID を証明します。 これは、SSL ハンドシェイクの一部として発生します。 お使いの API によって、証明書が有効なクライアント (Azure AD など) に属していることが確認され、承認の決定が実行されます。 クライアント証明書は、X.509 デジタル証明書です。 

> [!IMPORTANT]
> 運用環境では、この証明書は証明機関によって署名されている必要があります。

### <a name="create-a-certificate"></a>証明書を作成する

#### <a name="option-1-use-azure-key-vault-recommended"></a>オプション 1: Azure Key Vault を使用する (推奨)

証明書を作成するには、[Azure Key Vault](../../key-vault/certificates/create-certificate.md) を使用できます。これには、自己署名証明書のオプションと、署名された証明書の証明書発行者プロバイダーとの統合があります。 推奨設定には次が含まれます。
- **[サブジェクト]** : `CN=<yourapiname>.<tenantname>.onmicrosoft.com`
- **[コンテンツの種類]** : `PKCS #12`
- **[有効期間のアクション タイプ]** : `Email all contacts at a given percentage lifetime` または `Email all contacts a given number of days before expiry`
- **[キーの種類]** : `RSA`
- **[キー サイズ]** : `2048`
- **エクスポート可能な秘密キー**: `Yes` (`.pfx` ファイルをエクスポートできるようにするため)

これで、[証明書をエクスポート](../../key-vault/certificates/how-to-export-certificate.md)できます。

#### <a name="option-2-prepare-a-self-signed-certificate-using-powershell"></a>オプション 2: PowerShell を使用して自己署名証明書を準備する

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="configure-your-api-connector"></a>API コネクタを構成する

クライアント証明書認証を使用して API コネクタを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[Azure サービス]** で、 **[Azure AD]** を選択します。
3. **[API コネクタ]** を選択し、構成する **[API コネクタ]** を選択します。
4. **[認証の種類]** で **[証明書]** を選択します。
5. **[証明書のアップロード]** ボックスで、秘密キーを備えた証明書の .pfx ファイルを選択します。
6. **[パスワードの入力]** ボックスに、証明書のパスワードを入力します。
  :::image type="content" source="media/secure-api-connector/api-connector-upload-cert.png" alt-text="API コネクタの証明書認証構成を提供する。":::
7. **[保存]** を選択します。

### <a name="perform-authorization-decisions"></a>承認の決定を実行する 
API エンドポイントを保護するために、API では、送信されたクライアント証明書に基づいて承認が実装される必要があります。 Azure App Service と Azure Functions については、[TLS 相互認証の構成](../../app-service/app-service-web-configure-tls-mutual-auth.md)に関するページで、*API コードから証明書を有効化および検証* する方法をご覧ください。  または、Azure API Management をすべての API サービスの前のレイヤーとして使用して、[クライアント証明書のプロパティを目的の値と照合する](
../../api-management/api-management-howto-mutual-certificates-for-clients.md)こともできます。

### <a name="renewing-certificates"></a>証明書を書き換える
証明書の有効期限がまもなく切れることを知らせるリマインダー アラートを設定することをお勧めします。 使用されている証明書の有効期限が迫っている場合は、新しい証明書を生成し、上の手順を繰り返す必要があります。 新しい証明書の使用を "ロール" するために、新しい証明書がデプロイされている間、一時的に、お使いの API サービスで引き続き古い証明書と新しい証明書を受け入れることができます。 

既存の API コネクタに新しい証明書をアップロードするには、 **[API コネクタ]** で API コネクタを選択し、 **[新しい証明書のアップロード]** をクリックします。 直近でアップロードされ、開始日を過ぎていて有効期限が切れていない証明書が、Azure AD によって自動的に使用されます。

  :::image type="content" source="media/secure-api-connector/api-connector-renew-cert.png" alt-text="API コネクタが既に存在する場合は、このコネクタに新しい証明書を提供する。":::

## <a name="api-key-authentication"></a>API キー認証

一部のサービスは、"API キー" メカニズムを使用して、呼び出し元に HTTP ヘッダーまたは HTTP クエリ パラメーターとして一意のキーを含めることを要求することにより、開発中に HTTP エンドポイントへのアクセスを難読化します。 [Azure Functions](../../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) に対しては、お使いの API コネクタの **エンドポイント URL** に `code` をクエリ パラメーターとして含めることで、これを実現できます。 たとえば、`https://contoso.azurewebsites.net/api/endpoint`<b>`?code=0123456789`</b> です。 

これは、運用環境で単独で使用する必要があるメカニズムではありません。 そのため、基本認証または証明書認証の構成は常に必要です。 開発上の目的により、いずれの認証方法も実装しない場合 (推奨されません)、API コネクタの構成で "基本" 認証を選択し、適切な承認を実装する間、API が無視できる一時的な値を `username` と `password` に使用します。

## <a name="next-steps"></a>次のステップ
- [クイックスタートのサンプル](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)からご覧ください。
