---
title: Azure Web PubSub サービスでのマネージド ID
description: Azure Web PubSub サービスでのマネージド ID のしくみと、サーバーレス シナリオでマネージド ID を使用する方法について説明します。
author: chenyl
ms.service: azure-web-pubsub
ms.topic: article
ms.date: 10/22/2021
ms.author: chenyl
ms.openlocfilehash: c328e0e3bbff8c6e25e6f7b2df1c38cc710ace6b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269737"
---
# <a name="managed-identities-for-azure-web-pubsub-service"></a>Azure Web PubSub サービスでのマネージド ID

この記事では、Azure Web PubSub サービス用のマネージド ID を作成する方法と、それを使用する方法について説明します。

> [!Important] 
> Azure Web PubSub サービスでは、1 つのマネージド ID のみをサポートできます。 つまり、システム割り当て ID とユーザー割り当て ID のどちらかを追加できます。 

## <a name="add-a-system-assigned-identity"></a>システム割り当て ID を追加する

Azure portal でマネージド ID を設定するには、まず Azure Web PubSub サービスのインスタンスを作成してから、この機能を有効にします。

1. 通常どおり、ポータルで Azure Web PubSub サービスのインスタンスを作成します。 ポータルでこのインスタンスを参照します。

2. **[ID]** を選択します。

4. **[システム割り当て済み]** タブで、 **[状態]** を **[オン]** に切り替えます。 **[保存]** を選択します。

    :::image type="content" source="media/howto-use-managed-identity/system-identity-portal.png" alt-text="ポータルでシステム割り当て ID を追加する":::

## <a name="add-a-user-assigned-identity"></a>ユーザー割り当て ID を追加する

ユーザー割り当て ID を持つ Azure Web PubSub サービスのインスタンスを作成するには、ID を作成してから、そのリソース ID をサービスに追加する必要があります。

1. [以下の手順](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)に従って、ユーザー割り当てマネージド ID リソースを作成します。

2. 通常どおり、ポータルで Azure Web PubSub サービスのインスタンスを作成します。 ポータルでこのインスタンスを参照します。

3. **[ID]** を選択します。

4. **[ユーザー割り当て済み]** タブで、 **[追加]** を選択します。

5. 先ほど作成した ID を検索して選択します。 **[追加]** を選択します。

    :::image type="content" source="media/howto-use-managed-identity/user-identity-portal.png" alt-text="ポータルでユーザー割り当て ID を追加する":::

## <a name="use-a-managed-identity-in-client-events-scenarios"></a>クライアント イベントのシナリオでマネージド ID を使用する

Azure Web PubSub サービスはフル マネージド サービスであるため、マネージド ID を使用してトークンを手動で取得することはできません。 代わりに、Azure Web PubSub サービスにより、イベント ハンドラーにイベントを送信するときに、マネージド ID を使用してアクセス トークンが取得されます。 その後、サービスによって、http 要求の `Authorization` ヘッダーにアクセス トークンが設定されます。

### <a name="enable-managed-identity-authentication-in-event-handler-settings"></a>イベント ハンドラーの設定でマネージド ID 認証を有効にする

1. システム割り当て ID またはユーザー割り当て ID を追加します。

2. 規則に移動し、 **[認証]** を有効にします。

    :::image type="content" source="media/howto-use-managed-identity/msi-settings.png" alt-text="msi-setting":::

3. アプリケーションを選びます。 アプリケーション ID は取得されたアクセス トークンの `aud` クレームになり、イベント ハンドラーでの検証の一部として使用できます。 次のいずれかを選択できます。
    - 既定の AAD アプリケーションを使用します。
    - 既存の AAD アプリケーションから選択します。 選んだもののアプリケーション ID が使用されます。
    - AAD アプリケーションを指定します。 値は、[Azure サービスのリソース ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) である必要があります

    > [!NOTE]
    > サービスでアクセス トークンを自分で検証する場合は、任意のリソース形式を選択できます。 データ プレーンに対して Azure ロールベースのアクセス制御 (Azure RBAC) を使用する場合は、サービス プロバイダーが要求するリソースを使用する必要があります。

### <a name="validate-access-tokens"></a>アクセス トークンを検証する

`Authorization` ヘッダー内のトークンは、[Microsoft ID プラットフォームのアクセス トークン](../active-directory/develop/access-tokens.md#validating-tokens)です。

アクセス トークンを検証するには、アプリで対象ユーザーと署名トークンも検証する必要があります。 これらの検証は、OpenID 探索ドキュメント内の値に対して行ってください。 たとえば、[テナントに依存しないバージョンのドキュメント](https://login.microsoftonline.com/common/.well-known/openid-configuration)を確認してください。

Azure Active Directory (Azure AD) ミドルウェアには、アクセス トークンを検証するための機能が組み込まれています。 [サンプル](../active-directory/develop/sample-v2-code.md)を参照して、任意の言語で検索することができます。

トークンの検証を処理する方法を示すライブラリとコード サンプルが用意されています。 また、JSON Web Token (JWT) 検証に使用できるオープンソースのパートナー ライブラリもいくつかあります。 ほとんどすべてのプラットフォームと言語に、少なくとも 1 つのオプションがあります。 Azure AD 認証ライブラリとコード サンプルの詳細については、「[Microsoft ID プラットフォームの認証ライブラリ](../active-directory/develop/reference-v2-libraries.md)」を参照してください。

具体的には、イベント ハンドラーが Azure 関数または Web Apps でホストされている場合は、[Azure AD ログインを構成する](../app-service/configure-authentication-provider-aad.md)のが簡単な方法です。

## <a name="use-a-managed-identity-for-key-vault-reference"></a>Key Vault 参照にマネージド ID を使用する

Web PubSub サービスでは、Key Vault にアクセスし、マネージド ID を使用してシークレットを取得することができます。

1. Azure Web PubSub サービス用のシステム割り当て ID またはユーザー割り当て ID を追加します。

2. Key Vault のアクセス ポリシーで、マネージド ID に対する読み取りアクセス許可をシークレットに付与します。 [Azure portal を使用した Key Vault アクセス ポリシーの割り当て](../key-vault/general/assign-access-policy-portal.md)に関する説明を参照してください

現在、この機能は次のシナリオで使用できます。

- イベント ハンドラー URL テンプレートの設定で KeyVault からシークレットを取得するには、`{@Microsoft.KeyVault(SecretUri=<secret-identity>)}` という構文を使用します。

## <a name="next-steps"></a>次のステップ

- [チュートリアル: Azure Functions と Azure Web PubSub サービスを使用してサーバーレスのリアルタイム チャット アプリを作成する](quickstart-serverless.md)
