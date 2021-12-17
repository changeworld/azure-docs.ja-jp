---
title: API Management でバックアップと復元を使用してディザスター リカバリーを実装する
titleSuffix: Azure API Management
description: Azure API Management でバックアップと復元を使用してディザスター リカバリーを行う方法について説明します。
services: api-management
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 10/03/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b356d18c1a0c6a29d4fce142fc05e449f08f70d2
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2021
ms.locfileid: "129419147"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Azure API Management でサービスのバックアップと復元を使用してディザスター リカバリーを実装する方法

Azure API Management を介して API の発行と管理を行うことで、そうしなければ手動で設計、実装、および管理する必要のある、フォールト トレランス機能やインフラストラクチャ機能を利用できるようになります。 Azure プラットフォームにより、わずかな料金で潜在的な障害の大部分が軽減されます。

API Management サービスをホストしているリージョンに影響する可用性の問題から復旧するためには、いつも別のリージョンにサービスを再構成できるように準備しておく必要があります。 目標復旧時間に応じて、1 つまたは複数のリージョンでスタンバイ サービスを保持する必要がある場合があります。 また、目標復旧時間に従って、アクティブ サービスと同期されているそれらの構成と内容の維持を試みる場合があります。 サービスのバックアップと復元の機能により、ディザスター リカバリー戦略を実装するために必要な構成要素が提供されます。

また、バックアップおよび復元操作は、開発やステージングなど、運用環境間の API Management サービス構成のレプリケートで使用できます。 ユーザーやサブスクリプションなどのランタイム データも同様にコピーされ、望ましくない場合もあることに注意してください。

このガイドでは、バックアップおよび復元操作を自動化する方法と、Azure Resource Manager を使用して、確実にバックアップおよび復元要求の認証を成功させる方法を示します。

> [!IMPORTANT]
> 復元操作では、ターゲット サービスのカスタム ホスト名の構成は変更されません。 復元操作の完了後に、シンプルな DNS CNAME の変更で、トラフィックをスタンバイ インスタンスにリダイレクトできるように、アクティブとスタンバイの両方のサービスに対して同じカスタム ホスト名と TLS 証明書を使用することをおすすめします。
>
> バックアップ操作では、Azure portal の **Analytics** ブレードに表示されるレポートで使用される、事前に集計されたログ データはキャプチャされません。

> [!WARNING]
> 各バックアップの有効期限は 30 日間です。 30 日の有効期限が切れた後にバックアップを復元しようとしても、"`Cannot restore: backup expired`" というメッセージが表示されて復元は失敗します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Azure Resource Manager の要求の認証

> [!IMPORTANT]
> バックアップと復元用の REST API では、Azure Resource Manager が使用されます。API Management のエンティティ管理には REST API 以外の認証メカニズムも用意されています。 このセクションの手順では、Azure Resource Manager の要求を認証する方法について説明します。 詳細については、「[Azure REST API Reference (Azure REST API リファレンス)](/rest/api/azure)」を参照してください。

Azure Resource Manager を使用してリソースに実行するすべてのタスクは、Azure Active Directory で以下の手順に従って認証する必要があります。

-   Azure Active Directory テナントにアプリケーションを追加する。
-   追加したアプリケーションのアクセス許可を設定する。
-   Azure Resource Manager への要求を認証するためのトークンを取得する。

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory アプリケーションを作成する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. API Management サービス インスタンスが含まれているサブスクリプションを使用して、[Azure portal の [アプリの登録]](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) に移動し、Active Directory にアプリを登録します。
    > [!NOTE]
    > Azure Active Directory の既定のディレクトリがアカウントに表示されない場合は、必要なアクセス許可をアカウントに付与するよう Azure サブスクリプションの管理者に連絡してください。
1. **[+ 新規登録]** を選択します。
1. **[アプリケーションの登録]** ページで、次のように値を設定します。
    
    * **[名前]** をわかりやすい名前に設定します。
    * **[サポートされているアカウントの種類]** を **[この組織のディレクトリ内のアカウントのみ]** に設定します。 
    * **[リダイレクト URI]** に、`https://resources` などのプレースホルダー URL を入力します。 これは必須フィールドですが、値が後で使用されることはありません。 
    * **[登録]** を選択します。

### <a name="add-permissions"></a>Add permissions

1. アプリケーションが作成されたら、 **[API のアクセス許可]**  >  **[+ アクセス許可の追加]** を選択します。
1. **[Microsoft API]** を選択します。
1. **[Azure Service Management]** を選択します。

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/add-app-permission.png" alt-text="アプリのアクセス許可を追加する方法を示すスクリーンショット。"::: 

1. 新しく追加されたアプリケーションの横にある **[委任されたアクセス許可]** をクリックし、 **[組織ユーザーとして Azure Service Management にアクセスする (プレビュー)]** のチェック ボックスをオンにします。

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/delegated-app-permission.png" alt-text="委任されたアプリのアクセス許可の追加を示すスクリーンショット。":::

1. **[アクセス許可の追加]** を選択します.

### <a name="configure-your-app"></a>アプリの構成

サービス インスタンスをバックアップおよび復元する API を呼び出す前に、トークンを取得する必要があります。 次の例では、[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet パッケージを使用して、トークンを取得します。

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

`{tenant id}`、`{application id}`、および `{redirect uri}` を、次の指示に従って置き換えます。

1. `{tenant id}` を、作成した Azure Active Directory アプリケーションのテナント ID に置き換えます。 ID にアクセスするには、 **[アプリの登録]**  ->  **[エンドポイント]** をクリックします。

    ![エンドポイント][api-management-endpoint]

2. **[設定]** ページに移動して、`{application id}` を取得した値に置き換えます。
3. `{redirect uri}`を Azure Active Directory アプリケーションの **[リダイレクト URI]** タブの値に置き換えます。

    値を指定したら、次の例のようなコードによってトークンが返されます。

    ![トークン][api-management-arm-token]

    > [!NOTE]
    > トークンは、一定期間後に失効することがあります。 新しいトークンを生成するには、コード サンプルを再度実行します。

## <a name="accessing-azure-storage"></a>Azure Storage へのアクセス

API Management では、バックアップと復元操作用に指定した Azure Storage アカウントが使用されます。 バックアップまたは復元操作を実行する場合は、ストレージ アカウントへのアクセスを構成する必要があります。 API Management では、Azure Storage アクセス キー (既定) または API Management マネージド ID の 2 つのストレージ アクセス メカニズムがサポートされています。

### <a name="configure-storage-account-access-key"></a>ストレージ アカウントへのアクセス キーを構成する

手順については、「[ストレージ アカウント アクセス キーを管理する](../storage/common/storage-account-keys-manage.md?tabs=azure-portal)」をご覧ください。

### <a name="configure-api-management-managed-identity"></a>API Management マネージド ID を構成する

> [!NOTE]
> バックアップと復元中のストレージ操作に API Management マネージド ID を使用するには、API Management REST API バージョン `2021-04-01-preview` 以降が必要です。

1. API Management インスタンスで、システムによって割り当てられた、またはユーザーが割り当てた [API Management 用のマネージド ID](api-management-howto-use-managed-service-identity.md) を有効にします。

    * ユーザー割り当てマネージド ID を有効にする場合は、ID の **クライアント ID** をメモします。
    * 異なる API Management インスタンスにバックアップして復元する場合、ソースとターゲット インスタンスの両方でマネージド ID を有効にしてください。
1. バックアップと復元に使用されるストレージ アカウントを対象とする **ストレージ BLOB データ共同作成者** ロールに ID を割り当てます。 ロールは、[Azure portal](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md) または他の Azure ツールを使用して割り当てることができます。

## <a name="calling-the-backup-and-restore-operations"></a>バックアップおよび復元操作の呼び出し

REST API は [API Management Service - Backup](/rest/api/apimanagement/2020-12-01/api-management-service/backup) と [API Management Service - Restore](/rest/api/apimanagement/2020-12-01/api-management-service/restore) です。

> [!NOTE]
> バックアップと復元の操作は、それぞれ PowerShell の [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) コマンドと [_Restore-AzApiManagement_](/powershell/module/az.apimanagement/restore-azapimanagement) コマンドでも実行できます。

以降のセクションで説明されている "バックアップおよび復元" の操作を呼び出す前に、REST 呼び出しに承認要求ヘッダーを設定します。

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>API Management サービスのバックアップ

API Management サービスをバックアップするには、次の HTTP 要求を発行します。

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

各値の説明:

-   `subscriptionId` - バックアップ対象の API Management サービスを保持するサブスクリプションの ID
-   `resourceGroupName` - Azure API Management サービスのリソース グループの名前
-   `serviceName` - バックアップを作成する API Management サービスの、作成時に指定された名前
-   `api-version` - `2020-12-01` や `2021-04-01-preview` などの、有効な REST API のバージョン。

要求の本文に、ターゲットの ストレージ アカウント名、BLOB コンテナー名、バックアップ名、ストレージ アクセス種類を指定します。 ストレージ コンテナーが存在しない場合は、バックアップ操作によって作成されます。

#### <a name="access-using-storage-access-key"></a>ストレージ アクセス キーを使用したアクセス

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessKey": "{access key for the account}"
}
```

#### <a name="access-using-managed-identity"></a>マネージド ID を使用したアクセス

> [!NOTE]
> バックアップと復元中のストレージ操作に API Management マネージド ID を使用するには、API Management REST API バージョン `2021-04-01-preview` 以降が必要です。

**システム割り当てマネージド ID を使用したアクセス**

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessType": "SystemAssignedManagedIdentity"
}
```

**ユーザー割り当てマネージド ID を使用したアクセス**

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessType": "UserAssignedManagedIdentity",
    "clientId": "{client ID of user-assigned identity}"
}
```


`Content-Type` 要求ヘッダーの値を `application/json` に設定します。

バックアップは完了までに 1 分以上かかることもある、長時間の処理です。 要求が成功してバックアップ処理が開始されると、`Location` ヘッダーのある `202 Accepted` 応答状態コードを受け取ります。 `Location` ヘッダー内の URL に `GET` 要求を出して、処理のステータスを確認します。 バックアップの進行中は、`202 Accepted` 状態コードの受け取りが続きます。 応答コードの `200 OK` は、バックアップ処理が正常に終了したことを示します。

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>API Management サービスの復元

以前に作成されたバックアップから API Management サービスを復元するには、次の HTTP 要求を行います。

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

各値の説明:

-   `subscriptionId` - バックアップの復元先の API Management サービスを保持するサブスクリプションの ID
-   `resourceGroupName` - バックアップの復元先の Azure API Management サービスを保持するリソース グループの名前
-   `serviceName` - 復元先の API Management サービスの、作成時に指定された名前
-   `api-version` - `2020-12-01` や `2021-04-01-preview` などの、有効な REST API のバージョン

要求の本文に、存在するストレージ アカウント名、BLOB コンテナー名、バックアップ名、ストレージ アクセス種類を指定します。 

#### <a name="access-using-storage-access-key"></a>ストレージ アクセス キーを使用したアクセス

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessKey": "{access key for the account}"
}
```

#### <a name="access-using-managed-identity"></a>マネージド ID を使用したアクセス

> [!NOTE]
> バックアップと復元中のストレージ操作に API Management マネージド ID を使用するには、API Management REST API バージョン `2021-04-01-preview` 以降が必要です。

**システム割り当てマネージド ID を使用したアクセス**

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessType": "SystemAssignedManagedIdentity"
}
```

**ユーザー割り当てマネージド ID を使用したアクセス**

```json
{
    "storageAccount": "{storage account name for the backup}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}",
    "accessType": "UserAssignedManagedIdentity",
    "clientId": "{client ID of user-assigned identity}"
}
```

`Content-Type` 要求ヘッダーの値を `application/json` に設定します。

復元は、完了までに 30 分以上かかることのある長時間の操作です。 要求が成功して復元処理が開始されると、`Location` ヘッダーのある `202 Accepted` 応答状態コードを受け取ります。 `Location` ヘッダー内の URL に "GET" 要求を出して、処理のステータスを確認します。 復元の進行中は、`202 Accepted` 状態コードの受け取りが続きます。 応答コードの `200 OK` は、復元処理が正常に終了したことを示します。

> [!IMPORTANT]
> 復元先のサービスの **SKU** は、復元されるバックアップ サービスの SKU と **一致しなければなりません**。
>
> 復元処理の進行中にサービス構成 (API、ポリシー、開発者ポータルの外観など) に対して行われる **変更** は、**上書きされることがあります**。

## <a name="constraints-when-making-backup-or-restore-request"></a>バックアップまたは復元要求を行う際の制約

-   バックアップの進行中は、**サービスでの管理の変更は避けてください** (SKU のアップグレードやダウングレード、ドメイン名の変更など)。
-   バックアップの復元は、作成されたときから **30 日間だけ保証されます**。
-   バックアップ処理の進行中にサービス構成 (API、ポリシー、開発者ポータルの外観など) に対して行われた **変更** は、**バックアップ対象から除外され、その結果失われる可能性があります**。

-   [クロス オリジン リソース共有 (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) は、Azure ストレージ アカウントの Blob service で有効に **しない** でください。
-   復元先のサービスの **SKU** は、復元されるバックアップ サービスの SKU と **一致しなければなりません**。

## <a name="storage-networking-constraints"></a>ストレージ ネットワークの制約

### <a name="access-using-storage-access-key"></a>ストレージ アクセス キーを使用したアクセス

ストレージ アカウントで **[ファイアウォール][azure-storage-ip-firewall]が有効** になっており、アクセスにストレージ キーを使用する場合、お客様は、バックアップまたは復元が機能するように、そのストレージ アカウントで [Azure API Management コントロール プレーンの IP アドレス][control-plane-ip-address]のセットを **許可する** 必要があります。 ストレージ アカウントは、API Management サービスが配置されている場所を除く任意の Azure リージョンに置くことができます。 たとえば、API Management サービスが米国西部にある場合に、Azure ストレージ アカウントは米国西部 2 に置くことができます。お客様は、ファイアウォール内でコントロール プレーンの IP 13.64.39.16 (米国西部の API Management コントロール プレーンの IP) を開く必要があります。 これは、Azure Storage への要求が、同じ Azure リージョン内のコンピューティング (Azure API Management コントロール プレーン) からパブリック IP に SNAT によって変換されないためです。 リージョン間のストレージ要求は、SNAT によってパブリック IP アドレスに変換されます。

### <a name="access-using-managed-identity"></a>マネージド ID を使用したアクセス

API Management システム割り当てマネージド ID を使用してファイアウォールが有効なストレージ アカウントにアクセスする場合は、ストレージ アカウントが[信頼された Azure サービスのアクセスを許可](../storage/common/storage-network-security.md?tabs=azure-portal#grant-access-to-trusted-azure-services)するようにします。

## <a name="what-is-not-backed-up"></a>バックアップされないもの
-   分析レポートの生成に使用される **使用状況データ** は、バックアップに **含まれません**。 [Azure API Management REST API][azure api management rest api] を使用して、分析レポートを保管用に定期的に取り出します。
-   [カスタム ドメインの TLS/SSL](configure-custom-domain.md) 証明書。
-   お客様によってアップロードされた中間またはルートの証明書を含む[カスタム CA 証明書](api-management-howto-ca-certificates.md)。
-   [仮想ネットワーク](api-management-using-with-vnet.md)の統合設定
-   [マネージド ID](api-management-howto-use-managed-service-identity.md) の構成。
-   [Azure Monitor 診断ログ](api-management-howto-use-azure-monitor.md)の構成。
-   [プロトコルと暗号](api-management-howto-manage-protocols-ciphers.md)の設定。
-   [開発者ポータル](developer-portal-faq.md#is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management) コンテンツ。

サービス バックアップを実行する頻度は、復旧ポイントの目標に影響を与えます。 その頻度を最小限に抑えるため、定期的なバックアップを実装すると共に、API Management サービスに対して変更を行った後のオンデマンドのバックアップを実行することをお勧めします。

## <a name="next-steps"></a>次の手順

バックアップまたは復元プロセスについては、次の関連リソースを確認してください。

-   [Logic Apps を使用した API Management Backup および Restore の自動化](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
- [Azure API Management をリージョン間で移動する方法](api-management-howto-migrate.md)

API Management **Premium** レベルでは、[ゾーン冗長](zone-redundancy.md)もサポートされており、特定の Azure リージョン (場所) 内のサービス インスタンスに対する回復性と高可用性が提供されます。

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: /rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ip-addresses
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
