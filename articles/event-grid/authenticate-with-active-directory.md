---
title: Azure Active Directory を使用して Event Grid 発行クライアントを認証する (プレビュー)
description: この記事では、Azure Active Directory を使用して Azure Event Grid の発行クライアントを認証する方法について説明します。
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 6572c85fd13803372caa2c614a32cdc5f30b055e
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038093"
---
# <a name="authentication-and-authorization-with-azure-active-directory-preview"></a>Azure Active Directory を使用した認証と承認 (プレビュー)
この記事では、Azure Active Directory (Azure AD) を使用して Azure Event Grid の発行クライアントを認証する方法について説明します。

## <a name="overview"></a>概要
[Microsoft ID](../active-directory/develop/v2-overview.md) プラットフォームは、Azure Active Directory (Azure AD) を ID プロバイダーとして使用するリソースとアプリケーションに対して、統合された認証とアクセス制御の管理を提供します。 Microsoft ID プラットフォームを使用して、アプリケーションでの認証と認可のサポートを提供します。 これは OAuth 2.0 や OpenID Connect などのオープン スタンダードに基づいており、多くの認証シナリオをサポートするツールやオープンソースのライブラリを提供しています。 また、多要素認証を必要としたり、特定の場所からのアクセスを許可するなどのポリシーを設定できる[条件付きアクセス](../active-directory/conditional-access/overview.md)などの高度な機能も備えています。

Azure AD を使用することで向上するセキュリティ スタンス上の利点は、認証キーなどの資格情報をコードやリポジトリに保存する必要がないことです。 代わりに、保護されたリソースへの認証時にアプリケーションから提示される Microsoft ID プラットフォームから取得する OAuth 2.0 アクセス トークンに依存します。 イベント発行アプリケーションを Azure AD に登録して、自分で管理および使用しているアプリに関連付けられたサービス プリンシパルを取得できます。 代わりに、システム割り当てまたはユーザー割り当ての[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用すると、ID ライフサイクルの一部がユーザーに代わって管理されるため、さらにシンプルな ID 管理モデルになります。 

[ロールベースのアクセス制御](../active-directory/develop/custom-rbac-for-developers.md) (RBAC) を使用すると、特定のセキュリティ プリンシパル (ユーザー、グループ、またはアプリの ID) が Azure リソース上で操作を実行するための特定の権限を持つように、権限を設定することができます。 この方法では、Event Grid にイベントを送信するクライアント アプリケーションが使用するセキュリティ プリンシパルには、RBAC ロール **EventGrid データ送信者** が関連付けられている必要があります。 

### <a name="security-principals"></a>セキュリティ プリンシパル
Event Grid の発行クライアントの認証には、大きく分けて 2 つのカテゴリのセキュリティ プリンシパルがあります。 

- **マネージド ID**。 マネージド ID には、Azure リソース上で有効にしてそのリソースのみに関連付けられるシステム割り当てと、明示的に作成して名前を付けるユーザー割り当てがあります。 ユーザー割り当てマネージド ID は、複数のリソースに関連付けることが可能です。
- **アプリケーション セキュリティ プリンシパル**。 セキュリティ プリンシパルの一種で、Azure AD で保護されたリソースにアクセスするアプリケーションを表します。 

使用されるセキュリティ プリンシパル、マネージド ID、またはアプリケーション セキュリティ プリンシパルに関わらず、クライアントはその ID を使用して Azure AD の前に認証を行い、Event Grid にイベントを送信するときに要求と一緒に送信される [OAuth 2.0 アクセス トークン](../active-directory/develop/access-tokens.md)を取得します。 このトークンは暗号で署名され、Event Grid で受け取られた後、トークンが検証されます。 例えば、対象ユーザー (トークンの意図する受信者) が Event Grid (`https://eventgrid.azure.net`) であることなどが確認されます。 トークンには、クライアント ID に関する情報が含まれます。 Event Grid はその ID を受け取り、クライアントに **EventGrid データ送信者** というロールが割り当てられているか検証します。 より正確には、Event Grid は、イベント発行要求の完了を許可する前に、ID に関連付けられた RBAC ロールで ``Microsoft.EventGrid/events/send/action`` のアクセス許可を持っていることを検証します。 
 
Event Grid SDK を使用している場合、アクセス トークンの取得をどのように実装するか、また Event Grid へのすべての要求にそれをどのように含めるかといった詳細については、[Event Grid データプレーン SDK](#publish-events-using-event-grids-client-sdks) が行うため、ユーザーがこれについて心配する必要はありません。 

### <a name="client-configuration-steps-to-use-azure-ad-authentication"></a>Azure AD 認証を使用するためのクライアントの構成手順
トピック、ドメイン、またはパートナーの名前空間にイベントを送信する際に Azure AD 認証を使用するようクライアントを構成するには、次の手順を実行します。

1. 認証に使用するセキュリティ プリンシパルを作成または使用します。 [マネージド ID](#authenticate-using-a-managed-identity) または[アプリケーション サービス プリンシパル](#authenticate-using-a-security-principal-of-a-client-application)を使用することができます。
2. **EventGrid データ送信者 [ロールをセキュリティ プリンシパルに割り当て、](#assign-permission-to-a-security-principal-to-publish-events)セキュリティ プリンシパルにアクセス許可を付与してイベントを発行** します。
3. Event Grid SDK を使用して、Event Grid にイベントを発行します。

## <a name="authenticate-using-a-managed-identity"></a>マネージド ID を使用して認証する

マネージド ID は、Azure リソースに関連付けられている ID です。 マネージド ID は、Azure AD 認証をサポートする Azure リソースに使用する ID をアプリケーションに提供します。 アプリケーションでは、仮想マシンや Azure App サービスなどのホスティング リソースのマネージド ID を使用して、Event Grid にイベントを発行する際に要求とともに提示される Azure AD トークンを取得することができます。 アプリケーションが接続されると、Event Grid によってマネージド エンティティのコンテキストがクライアントにバインドされます。 マネージド ID に関連付けられると、Event Grid の発行クライアントは承認済みのすべての操作を実行できます。 承認は、マネージド エンティティを Event Grid RBAC ロールに関連付けることで付与されます。

マネージド ID により、Azure AD で自動的に管理される ID が Azure サービスに提供されます。 他の認証方法とは異なり、ID 自体やアクセスする必要があるリソースに対して、アクセス キーや Shared Access Signatures (SAS) をアプリケーション コードや構成に格納して保護する必要はありません。

マネージド ID を使用してイベント発行クライアントを認証するには、まず、クライアント アプリケーションのホスティング Azure サービスを決定してから、その Azure サービス インスタンスでシステム割り当てまたはユーザー割り当てマネージド ID を有効にしてください。 例えば、[VM](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)、[Azure App Service、または Azure Functions](../app-service/overview-managed-identity.md?tabs=dotnet) でマネージド ID を有効にすることができます。 

ホスティング サービスでマネージド ID を構成したら、[その ID にイベントを発行するアクセス許可を割り当て](#assign-permission-to-a-security-principal-to-publish-events)ます。

## <a name="authenticate-using-a-security-principal-of-a-client-application"></a>クライアント アプリケーションのセキュリティ プリンシパルを使用して認証する

マネージド ID の他にも、別の ID オプションとして、クライアント アプリケーションのセキュリティ プリンシパルを作成することもできます。 その場合は、アプリケーションを Azure AD に登録する必要があります。 アプリケーションの登録は、ID とアクセス管理の制御を Azure AD に委ねるジェスチャです。 「[アプリケーションを登録する](../active-directory/develop/quickstart-register-app.md#register-an-application)」セクションと「[クライアント シークレットの追加](../active-directory/develop/quickstart-register-app.md#add-a-client-secret)」セクションの手順に従います。 開始前に[前提条件](../active-directory/develop/quickstart-register-app.md#prerequisites)を確認するようにしてください。

アプリケーション セキュリティ プリンシパルを作成し、上記の手順に従ったら、[その ID にイベントを発行するアクセス許可を割り当てます](#assign-permission-to-a-security-principal-to-publish-events)。

> [!NOTE]
> ポータルでアプリケーションを登録すると、ホーム テナントに[アプリケーション オブジェクト](../active-directory/develop/app-objects-and-service-principals.md#application-object)と[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)が自動的に作成されます。 または、Microsoft Graph を使用アプリケーションを登録することもできます。 ただし、Microsoft Graph API を使用してアプリケーションを登録または作成する場合、サービス プリンシパル オブジェクトの作成は別の手順で行います。 

## <a name="assign-permission-to-a-security-principal-to-publish-events"></a>セキュリティ プリンシパルにアクセス許可を割り当ててイベントを発行する

イベントを Event Grid に発行するために使用する ID には、イベントを Event Grid に送信することを許可する ``Microsoft.EventGrid/events/send/action`` アクセス許可が付与されている必要があります。 このアクセス許可は、組み込みの RBAC ロール [EventGrid データ送信者](../role-based-access-control/built-in-roles.md#eventgrid-data-sender)に含まれています。 このロールは、管理グループ、Azure サブスクリプション、リソース グループ、または特定のイベント グリッド トピック、ドメイン、またはパートナー名前空間を指定できる、特定の[スコープ](../role-based-access-control/overview.md#scope)の[セキュリティ プリンシパル](../role-based-access-control/overview.md#security-principal)に割り当てることができます。 [Azure ロールの割り当て](../role-based-access-control/role-assignments-portal.md?tabs=current)に関するページの手順に従って **EventGrid データ送信者** ロールをセキュリティ プリンシパルに割り当て、そのセキュリティ プリンシパルを使用するアプリケーションにイベントを送信するためのアクセスを許可します。 または、``Microsoft.EventGrid/events/send/action`` アクセス許可を含む[カスタム ロール](../role-based-access-control/custom-roles.md)を定義し、そのカスタム ロールをセキュリティ プリンシパルに割り当てることもできます。

RBAC のアクセス許可の準備ができたので、これで[イベントを Event Grid に送信するクライアント アプリケーションを構築する](#publish-events-using-event-grids-client-sdks)ことができるようになりました。

> [!NOTE]
> Event Grid では、イベントの送信だけでなく、様々な RBAC ロールがサポートされています。 詳細については、[Event Grid の組み込みロール](security-authorization.md#built-in-roles)に関するページを参照してください。


## <a name="publish-events-using-event-grids-client-sdks"></a>Event Grid のクライアント SDK を使用してイベントを発行する

[Event Grid データ プレーン SDK](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/) を使用して、Event Grid にイベントを発行します。 Event Grid SDK では、Azure AD 認証を含むすべての認証方法がサポートされています。 

### <a name="prerequisites"></a>前提条件

Event Grid に対する認証を行うための前提条件は以下の通りです。

- アプリケーションに SDK をインストールします。
   - [Java](/java/api/overview/azure/messaging-eventgrid-readme#include-the-package)
   - [.NET](/dotnet/api/overview/azure/messaging.eventgrid-readme-pre#install-the-package)
   - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventgrid/eventgrid#install-the-azureeventgrid-package)
   - [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/eventgrid/azure-eventgrid#install-the-package)
- Azure ID クライアント ライブラリをインストールします。 Event Grid SDK では、認証に Azure ID クライアント ライブラリを使用しています。 
   - [Java 用 Azure ID クライアント ライブラリ](/java/api/overview/azure/identity-readme)
   - [.NET 用 Azure ID クライアント ライブラリ](/dotnet/api/overview/azure/identity-readme)
   - [JavaScript 用 Azure ID クライアント ライブラリ](/javascript/api/overview/azure/identity-readme)
   - [Python 用 Azure ID クライアント ライブラリ](/python/api/overview/azure/identity-readme)
- アプリケーションから送信されるイベントの送信先となるトピック、ドメイン、またはパートナー名前空間を作成します。

### <a name="publish-events-using-azure-ad-authentication"></a>Azure AD Authentication を使用してイベントを発行する

イベントをトピック、ドメイン、またはパートナー名前空間に送信するには、次のようにクライアントを構築します。 Azure AD 認証のサポートが初めて提供された API のバージョンは、``2021-06-01-preview`` です。 アプリケーションでは、その API バージョン、またはより新しいバージョンを使用します。

```java 
        DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
        EventGridPublisherClient cloudEventClient = new EventGridPublisherClientBuilder()
                .endpoint("<your-event-grid-topic-domain-or-partner-namespace-endpoint>?api-version=2021-06-01-preview")
                .credential(credential)
                .buildCloudEventPublisherClient();
```
クライアント発行アプリケーションに関連付けられているセキュリティ プリンシパルを使用している場合は、[Java SDK の readme の記事](/java/api/overview/azure/identity-readme#environment-variables)に記載されているように、環境変数を構成する必要があります。 `DefaultCredentialBuilder` は、適切な ID を使用するためにこれらの環境変数を読み取ります。 詳細については、[Java API の概要](/java/api/overview/azure/identity-readme#defaultazurecredential)に関する記事を参照してください。


詳細については、次の記事を参照してください。

- [Java 用 Azure Event Grid クライアント ライブラリ](/java/api/overview/azure/messaging-eventgrid-readme)
- [.NET 用 Azure Event Grid クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/eventgrid/Azure.Messaging.EventGrid#authenticate-using-azure-active-directory)
- [JavaScript 用 Azure Event Grid クライアント ライブラリ](/javascript/api/overview/azure/eventgrid-readme)
- [Python 用 Azure Event Grid クライアント ライブラリ](/python/api/overview/azure/eventgrid-readme)

## <a name="disable-key-and-shared-access-signature-authentication"></a>キーと Shared Access Signature 認証の無効化

Azure AD 認証は、アクセス キーや Shared Access Signature (SAS) トークン認証よりも優れた認証サポートを提供します。 Azure AD 認証では、ID は Azure AD ID プロバイダーに対して検証されます。 Azure AD 認証を使用する場合、開発者がコード内のキーを処理する必要はありません。 また、[条件付きアクセス](../active-directory/conditional-access/overview.md)など、Microsoft ID プラットフォームに組み込みのすべてのセキュリティ機能の恩恵を受けることができ、アプリケーションのセキュリティ スタンスを向上させることができます。 

Azure AD 認証を使用することを決定したら、アクセス キーや SAS トークンに基づく認証を無効にすることができます。 

> [!NOTE]
> アクセス キーまたは SAS トークン認証は、**ローカル認証** の一種です。 Azure AD に依存しないこのような種類の認証メカニズムについて話をする際に、"ローカル認証" という言葉を耳にすることがあります。 ローカル認証を無効にするための API パラメーターは、適切には ``disableLocalAuth`` と呼ばれています。

次の CLI コマンドは、ローカル認証が無効になっているカスタム トピックを作成する方法を示しています。 ローカル認証の無効化機能は現在プレビューとして使用でき、これには API バージョン ``2021-06-01-preview`` を使用する必要があります。

```cli
az resource create --subscription <subscriptionId> --resource-group <resourceGroup> --resource-type Microsoft.EventGrid/topics --api-version 2021-06-01-preview --name <topicName> --location <location> --properties "{ \"disableLocalAuth\": true}"
```

参考までに、作成または更新するトピックに応じて使用できるリソースの種類の値を以下に示します。

| トピックの種類        | リソースの種類                        |
| ------------------| :------------------------------------|
| ドメイン           | Microsoft.EventGrid/domains          |
| パートナー名前空間 | Microsoft.EventGrid/partnerNamespaces|
| カスタム トピック      | Microsoft.EventGrid/topics           |

PowerShell を使用している場合は、次のコマンドレットを使用して、ローカル認証を無効にしたカスタム トピックを作成します。 

```PowerShell

Set-AzContext -SubscriptionId <SubscriptionId>

New-AzResource -ResourceGroupName <ResourceGroupName> -ResourceType Microsoft.EventGrid/topics -ApiVersion 2021-06-01-preview -ResourceName <TopicName> -Location <Location> -Properties @{disableLocalAuth=$true}
```

> [!NOTE]
> - アクセス キーまたは Shared Access Signature 認証の使用に関する詳細については、[キーまたは SAS トークンを使用してクライアントを発行する認証](security-authenticate-publishing-clients.md)に関する記事を参照してください
> - この記事では、イベントを Event Grid に発行する (イベント イングレス) 際の認証について説明します。 イベントを配信する際の Event Grid の認証 (イベント エグレス) については、[イベンド ハンドラーへのイベント配信の認証](security-authentication.md)に関する記事を参照してください。 

## <a name="resources"></a>リソース
- データ プレーン SDK
    - Java SDK: [github](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventgrid/azure-messaging-eventgrid) | [サンプル](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventgrid/azure-messaging-eventgrid/src/samples/java/com/azure/messaging/eventgrid) | [以前の SDK バージョンからの移行ガイド](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventgrid/azure-messaging-eventgrid/migration-guide.md)
    - .NET SDK: [github](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventgrid/Azure.Messaging.EventGrid) | [サンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventgrid/Azure.Messaging.EventGrid/samples) | [以前の SDK バージョンからの移行ガイド](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventgrid/Azure.Messaging.EventGrid/MigrationGuide.md)
    - Python SDK: [github](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventgrid/azure-eventgrid) | [サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventgrid/azure-eventgrid/samples) | [以前の SDK バージョンからの移行ガイド](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventgrid/azure-eventgrid/migration_guide.md)
    - JavaScript SDK: [github](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventgrid/eventgrid/) | [サンプル](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventgrid/eventgrid/samples) | [以前の SDK バージョンからの移行ガイド](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/eventgrid/eventgrid/MIGRATION.md)
- [Event Grid SDK ブログ](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/)
- Azure ID クライアント ライブラリ
   - [Java](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/identity/azure-identity/README.md)
   - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/identity/Azure.Identity/README.md)  
   - [Python](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/identity/azure-identity/README.md)
   - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/identity/identity/README.md)
- [マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) について確認する
- 「[App Service と Azure Functions でマネージド ID を使用する方法](../app-service/overview-managed-identity.md?tabs=dotnet)」を確認する
- [アプリケーションとサービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)について確認する
- [Microsoft ID プラットフォームにアプリケーションを登録する](../active-directory/develop/quickstart-register-app.md)方法について説明します。
- [承認](../role-based-access-control/overview.md) (RBAC アクセス制御) のしくみについて説明します。
- [EventGrid データ送信者](../role-based-access-control/built-in-roles.md#eventgrid-data-sender)ロールを含む Event Grid の組み込み RBAC ロールについて説明します。 [Event Grid のロールの一覧](security-authorization.md#built-in-roles)。
- [RBAC ロールを ID に割り当てる](../role-based-access-control/role-assignments-portal.md?tabs=current)方法について説明します。
- [カスタム RBAC ロール](../role-based-access-control/custom-roles.md)を定義する方法について説明します。
- [Azure AD のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)について説明します。
- 「[Microsoft ID プラットフォーム アクセス トークン](../active-directory/develop/access-tokens.md)」について説明します。
- [OAuth 2.0 認証コード フローと Microsoft ID プラットフォーム](../active-directory/develop/v2-oauth2-auth-code-flow.md)について説明します
