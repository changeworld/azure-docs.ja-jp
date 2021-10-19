---
title: Azure Event Grid の Azure AD を使用して WebHook 配信をセキュリティで保護する
description: Azure Event Grid を使用して Azure Active Directory によって保護された HTTPS エンドポイントにイベントを配信する方法について説明します
ms.topic: how-to
ms.date: 09/29/2021
ms.openlocfilehash: ef5bbb33f738a102277870c6227813f6ce8ad257
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616859"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Azure Active Directory で保護されたエンドポイントにイベントを発行する
この記事では、Azure Active Directory (Azure AD) を使用して、**イベント サブスクリプション** と **Webhook エンドポイント** 間の接続をセキュリティで保護する方法について説明します。 Azure AD アプリケーションとサービス プリンシパルの概要については、[Microsoft ID プラットフォーム (v2.0) の概要](../active-directory/develop/v2-overview.md)に関するページを参照してください。

この記事ではデモンストレーションのために Azure portal を使用しますが、CLI、PowerShell、または SDK を使用して機能を有効にすることもできます。

> [!IMPORTANT]
> セキュリティの脆弱性に対処するために、2021 年 3 月 30 日にイベント サブスクリプションの作成または更新の一環として追加のアクセス確認が導入されました。 サブスクライバー クライアントのサービス プリンシパルは、所有者であるか、配信先のアプリケーション サービス プリンシパルのロールが割り当てられている必要があります。 以下の新しい手順に従って、ご自分の AAD アプリケーションを再構成してください。

## <a name="single-tenant-events-with-azure-ad-and-webhooks"></a>Azure AD と Webhook を使用したシングル テナント イベント

![Azure Event Grid の Azure AD を使用して WebHook 配信をセキュリティで保護する](./media/secure-webhook-delivery/single-tenant-diagram.png)

上の図に基づいて、次の手順に従ってテナントを構成します。

### <a name="configure-the-event-subscription-by-using-azure-ad-user"></a>Azure AD ユーザーを使用してイベント サブスクリプションを設定する

1. Microsoft ディレクトリ (シングル テナント) で動作するように構成された Webhook 用の Azure AD アプリケーションを作成します。

2. テナントで [Azure Shell](https://portal.azure.com/#cloudshell/) を開き、PowerShell 環境を選択します。

3. テナントに接続するために、 **$webhookAadTenantId** の値を変更します。

    - Variables:
        - **$webhookAadTenantId**: Azure テナント ID

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

4. [以下のスクリプト](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-user.md)を開き、 **$webhookAppObjectId** と **$eventSubscriptionWriterUserPrincipalName** の値を実際の識別子に置き換えて更新し、スクリプトの実行に進みます。

    - Variables:
        - **$webhookAppObjectId**: Webhook 用に作成された　Azure AD アプリケーション ID
        - **$eventSubscriptionWriterUserPrincipalName**: イベント サブスクリプションを作成するユーザーの Azure ユーザー プリンシパル名

    > [!NOTE]
    > **$eventGridAppId** の値を変更する必要はありません。このスクリプトでは、 **$eventGridRoleName** の値として **AzureEventGridSecureWebhookSubscriber** を設定しています。 このスクリプトを実行するには、[Azure AD アプリケーション管理者ロール](../active-directory/roles/permissions-reference.md#all-roles)のメンバーである必要があることに注意してください。

5. ポータルでイベント サブスクリプションを作成するときは、次の手順を実行します。

    1. エンドポイントの種類として **[Webhook]** を選択します。
    2. エンドポイントの **URI** を指定します。
    
        ![エンドポイントの種類で Webhook を選択します。](./media/secure-webhook-delivery/select-webhook.png)
    3. **[イベント サブスクリプションの作成]** ページの上部にある **[追加機能]** タブを選択します。
    4. **[追加機能]** タブで、次の手順を実行します。
        1. **[AAD 認証を使用する]** を選択し、テナント ID とアプリケーション ID を構成します。
        2. スクリプトの出力から Azure AD のテナント ID をコピーし、 **[AAD テナント ID]** フィールドに入力します。
        3. スクリプトの出力から Azure AD のアプリケーション ID をコピーし、 **[AAD アプリケーション ID]** フィールドに入力します。 または、AAD のアプリケーション ID URI を使用することもできます。 アプリケーション ID URI の詳細については、[こちらの記事](../app-service/configure-authentication-provider-aad.md)を参照してください。
    
            ![Secure Webhook アクション](./media/secure-webhook-delivery/aad-configuration.png)

### <a name="configure-the-event-subscription-by-using-azure-ad-application"></a>Azure AD アプリケーションを使用してイベント サブスクリプションを設定する

1. Microsoft ディレクトリ (シングル テナント) で動作するように構成された Event Grid サブスクリプション ライター用の Azure AD アプリケーションを作成します。

2. 以前に作成した Azure AD アプリケーションのシークレットを作成し、値を保存します (この値は後で必要になります)。

3. Event Grid トピックのアクセス制御 (IAM) に移動し、Event Grid サブスクリプション ライターのロールの割り当てを Event Grid 共同作成者として追加します。この手順により、Azure CLI を使用して Azure AD アプリケーションで Azure にログインしたときに、Event Grid リソースにアクセスできるようになります。

4. Microsoft ディレクトリ (シングル テナント) で動作するように構成された Webhook 用の Azure AD アプリケーションを作成します。

5. テナントで [Azure Shell](https://portal.azure.com/#cloudshell/) を開き、PowerShell 環境を選択します。

6. テナントに接続するために、 **$webhookAadTenantId** の値を変更します。

    - Variables:
        - **$webhookAadTenantId**: Azure テナント ID

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

7. [以下のスクリプト](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-app.md)を開き、 **$webhookAppObjectId** と **$eventSubscriptionWriterAppId** の値を実際の識別子に置き換えて更新し、スクリプトの実行に進みます。

    - Variables:
        - **$webhookAppObjectId**: Webhook 用に作成された　Azure AD アプリケーション ID
        - **$eventSubscriptionWriterAppId**: Event Grid サブスクリプション ライターの Azure AD アプリケーション ID

    > [!NOTE]
    > **```$eventGridAppId```** の値を変更する必要はありません。このスクリプトでは、 **```$eventGridRoleName```** の値として **AzureEventGridSecureWebhookSubscriber** を設定しています。 このスクリプトを実行するには、[Azure AD アプリケーション管理者ロール](../active-directory/roles/permissions-reference.md#all-roles)のメンバーである必要があることに注意してください。

8. コマンドを実行して、Event Grid サブスクリプション ライター Azure AD アプリケーションとしてログインします。

    ```Shell
    PS /home/user>az login --service-principal -u [REPLACE_WITH_EVENT_GRID_SUBSCRIPTION_WRITER_APP_ID] -p [REPLACE_WITH_EVENT_GRID_SUBSCRIPTION_WRITER_APP_SECRET_VALUE] --tenant [REPLACE_WITH_TENANT_ID]
    ```

9. 次のコマンドを実行して、サブスクリプションを作成します。

    ```Shell
    PS /home/user>az eventgrid system-topic event-subscription create --name [REPLACE_WITH_SUBSCRIPTION_NAME] -g [REPLACE_WITH_RESOURCE_GROUP] --system-topic-name [REPLACE_WITH_SYSTEM_TOPIC] --endpoint [REPLACE_WITH_WEBHOOK_ENDPOINT] --event-delivery-schema [REPLACE_WITH_WEBHOOK_EVENT_SCHEMA] --azure-active-directory-tenant-id [REPLACE_WITH_TENANT_ID] --azure-active-directory-application-id-or-uri [REPLACE_WITH_APPLICATION_ID_FROM_SCRIPT] --endpoint-type webhook
    ```

    > [!NOTE]
    > このシナリオでは、Event Grid システム トピック使用しています。 カスタム トピックや Event Grid ドメインのサブスクリプションを、Azure CLI を使用して作成する場合は、[こちら](/cli/azure/eventgrid)を参照してください。

10. すべてが正しく構成されていれば、Event Grid トピックに Webhook サブスクリプションを正常に作成することができます。

    > [!NOTE]
    > この時点で、Event Grid はすべてのメッセージで Azure AD ベアラー トークンを Webhook クライアントに渡すようになっているため、Webhook で認証トークンを検証する必要があります。

## <a name="multitenant-events-with-azure-ad-and-webhooks"></a>Azure AD と Webhook を使用したマルチテナント イベント

セキュリティで保護された Webhook サブスクリプションを複数のテナントで有効にするには、Azure AD アプリケーションを使用してこのタスクを実行する必要があります。このプロセスは現在、ポータルから Azure AD ユーザーを使用して利用することはできません。

![Azure AD と Webhook を使用したマルチテナント イベント](./media/secure-webhook-delivery/multitenant-diagram.png)

上の図に基づいて、次の手順に従って両方のテナントを構成します。

1. すべての Azure AD ディレクトリ (マルチテナント) で動作するように構成された Event Grid サブスクリプション ライター用の Azure AD アプリケーションを、**テナント A** に作成します。

2. 以前作成した Azure AD アプリケーションのシークレットを **テナント A** に作成し、値を保存します (この値は後で必要になります)。

3. **テナント A** で、Event Grid トピックのアクセス制御 (IAM) に移動し、Event Grid サブスクリプション ライターの Azure AD アプリケーションのロールの割り当てを Event Grid 共同作成者として追加します。この手順により、Azure CLI を使用して Azure AD アプリケーションで Azure にログインしたときに、Event Grid リソースにアクセスできるようになります。

4. Microsoft ディレクトリ (シングル テナント) で動作するように構成された Webhook 用の Azure AD アプリケーションを **テナント B** に作成します。

5. **テナント B** で [Azure Shell](https://portal.azure.com/#cloudshell/) を開き、PowerShell 環境を選択します。

6. **テナント B** に接続するために、 **$webhookAadTenantId** の値を変更します。

    - Variables:
        - **$webhookAadTenantId**: **テナント B** の Azure テナント ID

    ```Shell
    PS /home/user>$webhookAadTenantId = "[REPLACE_WITH_YOUR_TENANT_ID]"
    PS /home/user>Connect-AzureAD -TenantId $webhookAadTenantId
    ```

7. [以下のスクリプト](scripts/event-grid-powershell-webhook-secure-delivery-azure-ad-app.md)を開き、 **$webhookAppObjectId** と **$eventSubscriptionWriterAppId** の値を実際の識別子に置き換えて更新し、スクリプトの実行に進みます。

    - Variables:
        - **$webhookAppObjectId**: Webhook 用に作成された　Azure AD アプリケーション ID
        - **$eventSubscriptionWriterAppId**: Event Grid サブスクリプション ライターの Azure AD アプリケーション ID

    > [!NOTE]
    > **```$eventGridAppId```** の値を変更する必要はありません。このスクリプトでは、 **```$eventGridRoleName```** の値として **AzureEventGridSecureWebhookSubscriber** を設定しています。 このスクリプトを実行するには、[Azure AD アプリケーション管理者ロール](../active-directory/roles/permissions-reference.md#all-roles)のメンバーである必要があることに注意してください。

8. **テナント A** で [Azure Shell](https://portal.azure.com/#cloudshell/) を開いたら、コマンドを実行して、Event Grid サブスクリプション ライター Azure AD アプリケーションとしてログインします。

    ```Shell
    PS /home/user>az login --service-principal -u [REPLACE_WITH_APP_ID] -p [REPLACE_WITH_SECRET_VALUE] --tenant [REPLACE_WITH_TENANT_ID]
    ```

9. 次のコマンドを実行して、サブスクリプションを作成します。

    ```Shell
    PS /home/user>az eventgrid system-topic event-subscription create --name [REPLACE_WITH_SUBSCRIPTION_NAME] -g [REPLACE_WITH_RESOURCE_GROUP] --system-topic-name [REPLACE_WITH_SYSTEM_TOPIC] --endpoint [REPLACE_WITH_WEBHOOK_ENDPOINT] --event-delivery-schema [REPLACE_WITH_WEBHOOK_EVENT_SCHEMA] --azure-active-directory-tenant-id [REPLACE_WITH_TENANT_B_ID] --azure-active-directory-application-id-or-uri [REPLACE_WITH_APPLICATION_ID_FROM_SCRIPT] --endpoint-type webhook
    ```

    > [!NOTE]
    > このシナリオでは、Event Grid システム トピック使用しています。 カスタム トピックや Event Grid ドメインのサブスクリプションを、Azure CLI を使用して作成する場合は、[こちら](/cli/azure/eventgrid)を参照してください。

10. すべてが正しく構成されていれば、Event Grid トピックに Webhook サブスクリプションを正常に作成することができます。

    > [!NOTE]
    > この時点で、Event Grid はすべてのメッセージで Azure AD ベアラー トークンを Webhook クライアントに渡すようになっているため、Webhook で認証トークンを検証する必要があります。

## <a name="next-steps"></a>次のステップ

* イベント配信の監視について詳しくは、「[Event Grid メッセージ配信の監視](monitor-event-delivery.md)」をご覧ください。
* 認証キーについて詳しくは、「[Event Grid のセキュリティと認証](security-authentication.md)」をご覧ください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。