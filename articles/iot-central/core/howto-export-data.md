---
title: Azure IoT Central からデータをエクスポートする | Microsoft Docs
description: 新しいデータ エクスポートを使用して Azure とカスタム クラウドの宛先に IoT データをエクスポートする方法について説明します。
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/20/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1, contperf-fy21q3
ms.openlocfilehash: a4a941d114d233e723d853d12386cb42834d3e19
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493795"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>データ エクスポートを使用してクラウドの宛先に IoT データをエクスポートする

この記事では、Azure IoT Central のデータ エクスポートを使用する方法について説明します。 この機能を使用して、フィルター処理およびエンリッチ化された IoT データを IoT Central アプリケーションから連続エクスポートします。 データ エクスポートを使用すると、ウォーム パスの分析情報、分析、およびストレージ用に、クラウド ソリューションの他の部分にほぼリアルタイムで変更がプッシュされます。

たとえば、次のように操作できます。

- テレメトリ、プロパティ変更、デバイスのライフサイクル、デバイスの接続性、デバイスのライフサイクル、およびデバイスのテンプレートのライフサイクル データを、JSON 形式で、ほぼリアルタイムで継続的にエクスポートする
- データ ストリームをフィルター処理して、カスタム条件に一致するデータをエクスポートする
- デバイスからのカスタム値とプロパティ値を使用してデータ ストリームをエンリッチ化する
- データ ストリームを変換して、シェイプとコンテンツを変更します。
- データを Azure Event Hubs、Azure Data Explorer、Azure Service Bus、Azure Blob Storage、Webhook エンドポイントなどの宛先に送信します。

> [!Tip]
> データ エクスポートを有効にすると、その時点以降のデータのみが取得されます。 現在は、データ エクスポートがオフになっていたときのデータを取得することはできません。 より多くの履歴データを保持するには、データ エクスポートを早い段階で有効にしてください。

## <a name="prerequisites"></a>前提条件

データ エクスポート機能を使用するには、[V3 アプリケーション](howto-faq.yml#how-do-i-get-information-about-my-application-)が必要です。また、[データ エクスポート](howto-manage-users-roles.md)のアクセス許可が必要です。

V2 アプリケーションをご使用の場合は、[V3 への V2 IoT Central アプリケーションの移行](howto-migrate.md)に関する記事を参照してください。

## <a name="set-up-export-destination"></a>エクスポート先の設定

データ エクスポートを構成する前に、エクスポート先が存在している必要があります。 現在、次のエクスポート先の種類を使用できます。

- Azure Event Hubs
- Azure Service Bus キュー
- Azure Service Bus トピック
- Azure Blob Storage
- Azure Data Explorer
- Webhook

### <a name="connection-options"></a>接続オプション

Azure サービスの変換先では、接続 *文字列* または [マネージド id](../../active-directory/managed-identities-azure-resources/overview.md)を使用して接続を構成することができます。 セキュリティは、マネージド ID の方が優れています。その理由は次のとおりです。

- IoT Central アプリケーションの接続文字列にリソースの資格情報が格納されません。
- 資格情報は、IoT Central アプリケーションの有効期間に自動的に関連付けられます。
- マネージド ID では、セキュリティ キーの定期的なローテーションが自動的に行われます。

IoT Central は [、現在、システムによって割り当てられたマネージド id](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)を使用します。

管理対象 id を構成すると、その構成には *スコープ* と *ロール* が含まれます。

- スコープでは、マネージド id を使用できる場所を定義します。 たとえば、Azure リソースグループをスコープとして使用できます。 この場合、IoT Central アプリケーションと宛先の両方が同じリソースグループに存在する必要があります。
- ロールは、IoT Central アプリケーションが宛先サービスで付与するアクセス許可を定義します。 たとえば、IoT Central アプリケーションでイベントハブにデータを送信するには、 **Azure Event Hubs データ送信者** のロールの割り当てが必要です。

この記事では、Azure portal でマネージド id を作成する方法について説明します。 また、Azure CLI を使用して、managed id を作成することもできます。 詳細については、「 [Azure CLI を使用したリソースへのマネージド id アクセスの割り当て](../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)」を参照してください。

### <a name="create-an-event-hubs-destination"></a>Event Hubs の宛先を作成する

# <a name="connection-string"></a>[接続文字列](#tab/connection-string)

エクスポート先となる既存の Event Hubs 名前空間がない場合は、次の手順に従います。

1. [Azure portal で新しい Event Hubs 名前空間](https://portal.azure.com/#create/Microsoft.EventHub)を作成します。 詳細については、[Azure Event Hubs のドキュメント](../../event-hubs/event-hubs-create.md)を参照してください。

1. Event Hubs 名前空間にイベント ハブを作成します。 名前空間に移動し、上部の **[+ イベント ハブ]** を選択して、イベント ハブ インスタンスを作成します。

1. IoT Central でデータ エクスポートを設定するときに使用するキーを生成します。

    - 作成したイベント ハブ インスタンスを選択します。
    - **[設定]、[共有アクセス ポリシー]** の順に選択します。
    - 新しいキーを作成するか、**送信** アクセス許可を持っている既存のキーを選択します。
    - プライマリ キーまたはセカンダリの接続文字列をコピーします。 この接続文字列は、IoT Central に新しい宛先を設定するために使用します。
    - または、Event Hubs 名前空間全体の接続文字列を生成することもできます。
        1. Azure portal で Event Hubs 名前空間にアクセスします。
        2. **[設定]** で、 **[共有アクセス ポリシー]** を選択します。
        3. 新しいキーを作成するか、**送信** アクセス許可を持っている既存のキーを選択します。
        4. プライマリまたはセカンダリの接続文字列をコピーします。

# <a name="managed-identity"></a>[管理対象 ID](#tab/managed-identity)

エクスポート先となる既存の Event Hubs 名前空間がない場合は、次の手順に従います。

1. [Azure portal で新しい Event Hubs 名前空間](https://portal.azure.com/#create/Microsoft.EventHub)を作成します。 詳細については、[Azure Event Hubs のドキュメント](../../event-hubs/event-hubs-create.md)を参照してください。

1. Event Hubs 名前空間にイベント ハブを作成します。 名前空間に移動し、上部の **[+ イベント ハブ]** を選択して、イベント ハブ インスタンスを作成します。

[!INCLUDE [iot-central-managed-identity](../../../includes/iot-central-managed-identity.md)]

アクセス許可を構成するには:

1. **[ロールの割り当ての追加]** ページで、使用するスコープとサブスクリプションを選択します。

    > [!TIP]
    > IoT Central アプリケーションとイベントハブが同じリソースグループにある場合は、 **[リソースグループ]** をスコープとして選択し、リソースグループを選択できます。

1. **ロール** として **[Azure Event Hubs データ送信者]** を選択します。

1. **[保存]** を選択します。 これで、IoT Central アプリケーションのマネージド id が構成されました。

イベントハブをさらにセキュリティで保護し、マネージド id を持つ信頼されたサービスからのアクセスのみを許可するには、以下を参照してください。

- [プライベートエンドポイントを使用して Azure Event Hubs 名前空間へのアクセスを許可する](../../event-hubs/private-link-service.md)
- [信頼できる Microsoft サービス](../../event-hubs/private-link-service.md#trusted-microsoft-services)
- [特定の仮想ネットワークから Azure Event Hubs 名前空間へのアクセスを許可する](../../event-hubs/event-hubs-service-endpoints.md)

---

### <a name="create-a-service-bus-queue-or-topic-destination"></a>Service Bus キューまたはトピックの宛先を作成する

# <a name="connection-string"></a>[接続文字列](#tab/connection-string)

エクスポート先となる既存の Service Bus 名前空間がない場合は、次の手順に従います。

1. [Azure portal で新しい Service Bus 名前空間](https://portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)を作成します。 詳細については、[Azure Service Bus のドキュメント](../../service-bus-messaging/service-bus-create-namespace-portal.md)を参照してください。

1. エクスポート先のキューまたはトピックを作成するには、Service Bus 名前空間に移動し、 **[+ キュー]** または **[+ トピック]** を選択します。

1. IoT Central でデータ エクスポートを設定するときに使用するキーを生成します。

    - 作成したキューまたはトピックをクリックします。
    - **[設定]/[共有アクセス ポリシー]** の順に選択します。
    - 新しいキーを作成するか、**送信** アクセス許可を持っている既存のキーを選択します。
    - プライマリ キーまたはセカンダリの接続文字列をコピーします。 この接続文字列は、IoT Central に新しい宛先を設定するために使用します。
    - または、Service Bus 名前空間全体の接続文字列を生成することもできます。
        1. Azure portal で、ご利用の Service Bus 名前空間に移動します。
        2. **[設定]** で、 **[共有アクセス ポリシー]** を選択します。
        3. 新しいキーを作成するか、**送信** アクセス許可を持っている既存のキーを選択します。
        4. プライマリまたはセカンダリの接続文字列をコピーします。

# <a name="managed-identity"></a>[管理対象 ID](#tab/managed-identity)

エクスポート先となる既存の Service Bus 名前空間がない場合は、次の手順に従います。

1. [Azure portal で新しい Service Bus 名前空間](https://portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)を作成します。 詳細については、[Azure Service Bus のドキュメント](../../service-bus-messaging/service-bus-create-namespace-portal.md)を参照してください。

1. エクスポート先のキューまたはトピックを作成するには、Service Bus 名前空間に移動し、 **[+ キュー]** または **[+ トピック]** を選択します。

[!INCLUDE [iot-central-managed-identity](../../../includes/iot-central-managed-identity.md)]

アクセス許可を構成するには:

1. **[ロールの割り当ての追加]** ページで、使用するスコープとサブスクリプションを選択します。

    > [!TIP]
    > IoT Central アプリケーションとキューまたはトピックが同じリソースグループ内にある場合は、 **[リソースグループ]** をスコープとして選択し、リソースグループを選択できます。

1. **ロール** として **[Azure Service Bus データ送信者]** を選択します。

1. **[保存]** を選択します。 これで、IoT Central アプリケーションのマネージド id が構成されました。

キューまたはトピックをさらにセキュリティで保護し、マネージド id を持つ信頼されたサービスからのアクセスのみを許可するには、以下を参照してください。

- [プライベートエンドポイントを使用して Azure Service Bus 名前空間へのアクセスを許可する](../../service-bus-messaging/private-link-service.md)
- [信頼できる Microsoft サービス](../../service-bus-messaging/private-link-service.md#trusted-microsoft-services)
- [特定の仮想ネットワークから Azure Service Bus 名前空間へのアクセスを許可する](../../service-bus-messaging/service-bus-service-endpoints.md)

---

### <a name="create-an-azure-blob-storage-destination"></a>Azure Blob Storage の宛先を作成する

# <a name="connection-string"></a>[接続文字列](#tab/connection-string)

エクスポート先となる既存の Azure Storage アカウントがない場合は、次の手順に従います。

1. [Azure portal で新しいストレージ アカウント](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM)を作成します。 新しい [Azure Blob Storage アカウント](../../storage/blobs/storage-quickstart-blobs-portal.md)または [Azure Data Lake Storage v2 ストレージ アカウント](../../storage/common/storage-account-create.md)の作成の詳細を確認できます。 データのエクスポートでは、ブロック BLOB をサポートするストレージ アカウントにのみデータを書き込めます。 次の一覧は、互換性のある既知のストレージ アカウントの種類を示しています。

    |パフォーマンス レベル|アカウントの種類|
    |-|-|
    |Standard|General Purpose V2|
    |Standard|General Purpose V1|
    |Standard|BLOB ストレージ|
    |Premium|ブロック BLOB ストレージ|

1. ストレージ アカウントにコンテナーを作成するには、ストレージ アカウントにアクセスします。 **[Blob service]** で **[BLOB の参照]** を選択します。 上部の **[+ コンテナー]** を選択して、新しいコンテナーを作成します。

1. **[設定] > [アクセス キー]** の順に移動してストレージ アカウントの接続文字列を生成します。 2 つの接続文字列のうち 1 つをコピーします。

# <a name="managed-identity"></a>[管理対象 ID](#tab/managed-identity)

エクスポート先となる既存の Azure Storage アカウントがない場合は、次の手順に従います。

1. [Azure portal で新しいストレージ アカウント](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM)を作成します。 新しい [Azure Blob Storage アカウント](../../storage/blobs/storage-quickstart-blobs-portal.md)または [Azure Data Lake Storage v2 ストレージ アカウント](../../storage/common/storage-account-create.md)の作成の詳細を確認できます。 データのエクスポートでは、ブロック BLOB をサポートするストレージ アカウントにのみデータを書き込めます。 次の一覧は、互換性のある既知のストレージ アカウントの種類を示しています。

    |パフォーマンス レベル|アカウントの種類|
    |-|-|
    |Standard|General Purpose V2|
    |Standard|General Purpose V1|
    |Standard|BLOB ストレージ|
    |Premium|ブロック BLOB ストレージ|

1. ストレージ アカウントにコンテナーを作成するには、ストレージ アカウントにアクセスします。 **[Blob service]** で **[BLOB の参照]** を選択します。 上部の **[+ コンテナー]** を選択して、新しいコンテナーを作成します。

[!INCLUDE [iot-central-managed-identity](../../../includes/iot-central-managed-identity.md)]

アクセス許可を構成するには:

1. **[ロールの割り当ての追加]** ページで、使用するサブスクリプションを選択し、スコープとして **Storage** します。 次に、ストレージアカウントをリソースとして選択します。

1. **ロール** として **Storage Blob データ共同作成者** を選択します。

1. **[保存]** を選択します。 これで、IoT Central アプリケーションのマネージド id が構成されました。

    > [!TIP]
    > このロールの割り当ては、Azure の **[ロールの割り当て]** ページの一覧に表示されません。

Blob コンテナーをさらにセキュリティで保護し、マネージド id を持つ信頼されたサービスからのアクセスのみを許可するには、以下を参照してください。

- [Azure Storage のプライベート エンドポイントを使用する](../../storage/common/storage-private-endpoints.md)
- [Azure リソースのマネージド ID を使用して BLOB データへのアクセスを承認する](../../storage/blobs/authorize-managed-identity.md)
- [Azure Storage ファイアウォールおよび仮想ネットワークを構成する](../../storage/common/storage-network-security.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json)

---

### <a name="create-an-azure-data-explorer-destination"></a>Azure Data Explorer の宛先を作成する

エクスポート先となる既存の [Azure Data Explorer](/azure/data-explorer/data-explorer-overview) のクラスターとデータベースがない場合は、次の手順に従います。

1. 新しい Azure Data Explorer クラスターとデータベースを作成します。 詳細については、[Azure Data Explorer のクイック スタート](/azure/data-explorer/create-cluster-database-portal)に関するページを参照してください。 作成するデータベースの名前をメモしておきます。この値は次の手順で必要になります。

1. IoT Central アプリケーションを Azure Data Explorer に接続するために使用できるサービス プリンシパルを作成します。 Azure Cloud Shell を使用して次のコマンドを実行します。

    ```azurecli
    az ad sp create-for-rbac --skip-assignment --name "My SP for IoT Central"
    ```

    コマンドの出力で、`appId`、`password`、`tenant` の値をメモしておきます。これらの値は次の手順で必要になります。

1. サービス プリンシパルをデータベースに追加するには、Azure Data Explorer ポータルに移動し、データベースに対して次のクエリを実行します。 プレースホルダーを、前に書き留めた値に置き換えます。

    ```kusto
    .add database <YourDatabaseName> admins ('aadapp=<YourAppId>;<YourTenant>');
    ```

1. エクスポートするデータに適したスキーマを使用して、データベースにテーブルを作成します。 次のクエリ例では、`smartvitalspatch` という名前のテーブルを作成します。 詳しくは、「[IoT Central アプリケーション内のデータをエクスポート用に変換する](howto-transform-data-internally.md)」を参照してください。

    ```kusto
    .create table smartvitalspatch (
      EnqueuedTime:datetime,
      Message:string,
      Application:string,
      Device:string,
      Simulated:boolean,
      Template:string,
      Module:string,
      Component:string,
      Capability:string,
      Value:dynamic
    )
    ```

1. (オプション) Azure Data Explorer データベースへのデータの取り込みを高速化するには、次のようにします。

    1. Azure Data Explorer クラスターの **[構成]** ページに移動します。 次に、 **[ストリーミング インジェスト**] オプションを有効にします。
    1. 次のクエリを実行して、テーブル ポリシーを変更してストリーミング インジェストを有効にします。

        ```kusto
        .alter table smartvitalspatch policy streamingingestion enable
        ```

1. Azure Data Explorer クラスターの URL、データベース名、テーブル名を使用して IoT Central に Azure Data Explorer の宛先を追加します。 次の表は、承認に使用するサービス プリンシパルの値を示しています。

    | サービス プリンシパルの値 | 宛先の構成 |
    | ----------------------- | ------------------------- |
    | appId                   | ClientID                  |
    | tenant                  | テナント ID                 |
    | password                | クライアント シークレット             |

    :::image type="content" source="media/howto-export-data/export-destination.png" alt-text="Azure Data Explorer のエクスポート先のスクリーンショット。":::

### <a name="create-a-webhook-endpoint"></a>Webhook エンドポイントを作成する

パブリックに使用できる HTTP Webhook エンドポイントにデータをエクスポートできます。 [RequestBin](https://requestbin.net/) を使用して、テスト用の Webhook エンドポイントを作成できます。 RequestBin は、要求の上限に達したときに、要求をスロットルします。

1. [RequestBin](https://requestbin.net/) を開きます。
2. 新しい RequestBin を作成し、**Bin URL** をコピーします。 この URL は、データ エクスポートをテストするときに使用します。

## <a name="set-up-data-export"></a>データ エクスポートの設定

# <a name="connection-string"></a>[接続文字列](#tab/connection-string)

[!INCLUDE [iot-central-create-export](../../../includes/iot-central-create-export.md)]

変換先を構成します。

1. 新しい宛先を追加するか、既に作成済みの宛先を追加します。 **[Create a new one]\(新しく作成する\)** リンクを選択し、次の情報を追加します。

    - **宛先名**: IoT Central 内の宛先の表示名
    - **宛先の種類**: 宛先の種類を選択します。 宛先をまだ設定していない場合は、「[エクスポート先の設定](#set-up-export-destination)」を参照してください。
    - **承認**: **[接続文字列]** を選択します。
    - Azure Event Hubs、Azure Service Bus キューまたはトピックの場合は、リソースの接続文字列を貼り付け、必要に応じてイベント ハブ、キュー、またはトピック名を大文字と小文字を区別して入力します。
    - Azure Blob Storage の場合は、リソースの接続文字列を貼り付け、必要に応じてコンテナー名を大文字と小文字を区別して入力します。
    - Webhook の場合は、Webhook エンドポイントのコールバック URL を貼り付けます。 必要に応じて、Webhook 認可 (OAuth 2.0 および認可トークン) を構成し、カスタム ヘッダーを追加することができます。 
        - OAuth 2.0 の場合は、クライアント資格情報フローのみがサポートされます。 宛先を保存すると、IoT Central では OAuth プロバイダーと通信して認証トークンが取得されます。 このトークンは、この送信先に送信 `Authorization` されるメッセージごとにヘッダーにアタッチされます。
        - 認証トークンの場合、 `Authorization` この宛先に送信されるすべてのメッセージのヘッダーに直接接続されるトークン値を指定できます。
    - **［作成］** を選択します

1. **[+ 宛先]** を選択し、ドロップダウンから宛先を選択します。 1 つのエクスポートに最大 5 つの宛先を追加できます。

1. エクスポートの設定が完了したら、 **[保存]** を選択します。 数分後に、エクスポート先にデータが表示されます。

# <a name="managed-identity"></a>[管理対象 ID](#tab/managed-identity)

[!INCLUDE [iot-central-create-export](../../../includes/iot-central-create-export.md)]

変換先を構成します。

1. 新しい宛先を追加するか、既に作成済みの宛先を追加します。 **[Create a new one]\(新しく作成する\)** リンクを選択し、次の情報を追加します。

    - **宛先名**: IoT Central 内の宛先の表示名
    - **宛先の種類**: 宛先の種類を選択します。 宛先をまだ設定していない場合は、「[エクスポート先の設定](#set-up-export-destination)」を参照してください。
    - **承認**: **システムによって割り当てられたマネージド id** を選択します。
    - azure Event Hubs と azure Service Bus のキューまたはトピックについては、リソースのホスト名を入力します。 次に、大文字と小文字を区別するイベントハブ、キュー、またはトピック名を入力します。 ホスト名はのように `contoso-waste.servicebus.windows.net` なります。
    - Azure Blob Storage には、ストレージアカウントのエンドポイント URI と、大文字と小文字を区別するコンテナー名を入力します。 エンドポイント URI は、のように `https://contosowaste.blob.core.windows.net` なります。
    - Webhook の場合は、Webhook エンドポイントのコールバック URL を貼り付けます。 必要に応じて、Webhook 認可 (OAuth 2.0 および認可トークン) を構成し、カスタム ヘッダーを追加することができます。
        - OAuth 2.0 の場合は、クライアント資格情報フローのみがサポートされます。 宛先を保存すると、IoT Central では OAuth プロバイダーと通信して認証トークンが取得されます。 このトークンは、この送信先に送信 `Authorization` されるメッセージごとにヘッダーにアタッチされます。
        - 認証トークンの場合、 `Authorization` この宛先に送信されるすべてのメッセージのヘッダーに直接接続されるトークン値を指定できます。
    - **［作成］** を選択します

1. **[+ 宛先]** を選択し、ドロップダウンから宛先を選択します。 1 つのエクスポートに最大 5 つの宛先を追加できます。

1. エクスポートの設定が完了したら、 **[保存]** を選択します。 数分後に、エクスポート先にデータが表示されます。

---

## <a name="monitor-your-export"></a>エクスポートの監視

エクスポートの状態は IoT Central で確認できます。 [Azure Monitor](../../azure-monitor/overview.md)を使用して、エクスポートするデータの量とエクスポートエラーを確認することもできます。 エクスポートとデバイスの正常性のメトリックには、Azure portal 内のグラフ、REST API、または PowerShell や Azure CLI のクエリを使用してアクセスできます。 現時点では、Azure Monitor で次のデータ エクスポート メトリックを監視できます。

- フィルター適用前のエクスポート対象受信メッセージの数。
- フィルターを通過したメッセージの数。
- 宛先に正常にエクスポートされたメッセージの数。
- 発生したエラーの数。

詳細については、「[アプリケーションの正常性を監視する](howto-manage-iot-central-from-portal.md#monitor-application-health)」を参照してください。

## <a name="destinations"></a>変換先

### <a name="azure-blob-storage-destination"></a>Azure Blob Storage の宛先

データは 1 分に 1 回エクスポートされ、各ファイルには、前のエクスポート以降の変更のバッチが含まれます。 エクスポートされたデータは JSON 形式で保存されます。 ストレージ アカウント内のエクスポートされたデータの既定のパスは次のとおりです。

- テレメトリ: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- プロパティ変更: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Azure portal でエクスポートされたファイルを参照するには、そのファイルに移動し、 **[BLOB の編集]** を選択します。

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure Event Hubs および Azure Service Bus の宛先

データはほぼリアルタイムでエクスポートされます。 データはメッセージ本文に含まれ、UTF-8 としてエンコードされた JSON 形式です。

メッセージの注釈またはシステム プロパティ バッグ内には、メッセージ本文の対応するフィールドと同じ値を持つ `iotcentral-device-id`、`iotcentral-application-id`、`iotcentral-message-source`、および `iotcentral-message-type` フィールドがあります。

### <a name="azure-data-explorer-destination"></a>Azure Data Explorer の宛先

データは、Azure Data Explorer クラスター内の指定されたデータベース テーブルにほぼリアルタイムでエクスポートされます。 データはメッセージ本文に含まれ、UTF-8 としてエンコードされた JSON 形式です。 IoT Central に [変換](howto-transform-data-internally.md) を追加して、テーブルスキーマに一致するデータをエクスポートすることができます。

Azure Data Explorer ポータルでエクスポートされたデータに対してクエリを実行するには、データベースに移動し、 **[クエリ]** を選択します。

### <a name="webhook-destination"></a>Webhook の宛先

Webhook が宛先の場合も、データはほぼリアルタイムでエクスポートされます。 メッセージ本文のデータは、Event Hubs および Service Bus と同じ形式です。

## <a name="telemetry-format"></a>テレメトリ形式

エクスポートされた各メッセージには、メッセージ本文でデバイスから送信された、完全なメッセージが正規化された形式で含まれます。 メッセージは JSON 形式で、UTF-8 としてエンコードされます。 各メッセージに含まれる情報は次のとおりです。

- `applicationId`:IoT Central アプリケーションの ID。
- `messageSource`:メッセージのソース - `telemetry`。
- `deviceId`:テレメトリ メッセージを送信したデバイスの ID。
- `schema`:ペイロード スキーマの名前とバージョン。
- `templateId`:デバイスに関連付けられているデバイス テンプレートの ID。
- `enqueuedTime`: IoT Central によってこのメッセージが受信された時刻。
- `enrichments`:エクスポートに設定されたエンリッチメント。
- `module`: このメッセージを送信した IoT Edge モジュール。 このフィールドは、メッセージが IoT Edge モジュールから送信された場合にのみ表示されます。
- `component`: このメッセージを送信したコンポーネント。 このフィールドは、メッセージで送信された機能がデバイス テンプレートでコンポーネントとしてモデル化されている場合にのみ表示されます。
- `messageProperties`: デバイスがメッセージと共に送信するその他のプロパティ。 これらのプロパティは *アプリケーション プロパティ* と呼ばれることもあります。 [詳細については IoT Hub のドキュメントを参照してください](../../iot-hub/iot-hub-devguide-messages-construct.md)。

Event Hubs と Service Bus の場合、IoT Central は、デバイスからメッセージを受信した後、すぐに新しいメッセージをエクスポートします。 各メッセージのユーザー プロパティ (アプリケーション プロパティとも呼ばれます) では、`iotcentral-device-id`、`iotcentral-application-id`、および `iotcentral-message-source` が自動的に含まれます。

Blob Storage の場合、メッセージはバッチ処理され、1 分に 1 回エクスポートされます。

次の例は、エクスポートされたテレメトリ メッセージを示しています。

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "module": "VitalsModule",
    "component": "DeviceComponent",
    "messageProperties": {
      "messageProp": "value"
    }
}
```

### <a name="message-properties"></a>メッセージのプロパティ

テレメトリ メッセージには、テレメトリ ペイロードに加え、メタデータのプロパティが含まれています。 前のスニペットは、`deviceId` や `enqueuedTime` など、システム メッセージの例を示しています。 システム メッセージ プロパティの詳細については、「[D2C IoT Hub メッセージのシステム プロパティ](../../iot-hub/iot-hub-devguide-messages-construct.md#system-properties-of-d2c-iot-hub-messages)」を参照してください。

テレメトリ メッセージにカスタム メタデータを追加する必要がある場合、テレメトリ メッセージにプロパティを追加できます。 たとえば、デバイスでメッセージが作成されるとき、タイムスタンプを追加する必要があります。

次のコード スニペットは、デバイス上でメッセージを作成するとき、それに `iothub-creation-time-utc` プロパティを追加する方法を示しています。

> [!IMPORTANT]
> このタイムスタンプの形式は、タイムゾーン情報を含まない UTC であることが必要です。 たとえば、`2021-04-21T11:30:16Z` は有効で、`2021-04-21T11:30:16-07:00` は無効です。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.properties.add("iothub-creation-time-utc", new Date().toISOString());
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

# <a name="java"></a>[Java](#tab/java)

```java
private static void sendTemperatureTelemetry() {
  String telemetryName = "temperature";
  String telemetryPayload = String.format("{\"%s\": %f}", telemetryName, temperature);

  Message message = new Message(telemetryPayload);
  message.setContentEncoding(StandardCharsets.UTF_8.name());
  message.setContentTypeFinal("application/json");
  message.setProperty("iothub-creation-time-utc", Instant.now().toString());

  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
  log.debug("My Telemetry: Sent - {\"{}\": {}°C} with message Id {}.", telemetryName, temperature, message.getMessageId());
  temperatureReadings.put(new Date(), temperature);
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
private async Task SendTemperatureTelemetryAsync()
{
  const string telemetryName = "temperature";

  string telemetryPayload = $"{{ \"{telemetryName}\": {_temperature} }}";
  using var message = new Message(Encoding.UTF8.GetBytes(telemetryPayload))
  {
      ContentEncoding = "utf-8",
      ContentType = "application/json",
  };
  message.Properties.Add("iothub-creation-time-utc", DateTime.UtcNow.ToString("yyyy-MM-ddTHH:mm:ssZ"));
  await _deviceClient.SendEventAsync(message);
  _logger.LogDebug($"Telemetry: Sent - {{ \"{telemetryName}\": {_temperature}°C }}.");
}
```

# <a name="python"></a>[Python](#tab/python)

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.custom_properties["iothub-creation-time-utc"] = datetime.now(timezone.utc).isoformat()
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

---

次のスニペットは、BLOB ストレージにエクスポートされたメッセージにおけるこのプロパティを示しています。

```json
{
  "applicationId":"5782ed70-b703-4f13-bda3-1f5f0f5c678e",
  "messageSource":"telemetry",
  "deviceId":"sample-device-01",
  "schema":"default@v1",
  "templateId":"urn:modelDefinition:mkuyqxzgea:e14m1ukpn",
  "enqueuedTime":"2021-01-29T16:45:39.143Z",
  "telemetry":{
    "temperature":8.341033560421833
  },
  "messageProperties":{
    "iothub-creation-time-utc":"2021-01-29T16:45:39.021Z"
  },
  "enrichments":{}
}
```

## <a name="property-changes-format"></a>プロパティ変更の形式

各メッセージまたはレコードは、デバイスとクラウドのプロパティの変更を表します。 エクスポートされたメッセージに含まれる情報は次のとおりです。

- `applicationId`:IoT Central アプリケーションの ID。
- `messageSource`:メッセージのソース - `properties`。
- `messageType`:`cloudPropertyChange`、`devicePropertyDesiredChange` または `devicePropertyReportedChange` のいずれかです。
- `deviceId`:テレメトリ メッセージを送信したデバイスの ID。
- `schema`:ペイロード スキーマの名前とバージョン。
- `enqueuedTime`: IoT Central によってこの変更が検出された時刻。
- `templateId`:デバイスに関連付けられているデバイス テンプレートの ID。
- `properties`: 変更されたプロパティの名前と値を含む、変更されたプロパティの配列。 プロパティがコンポーネントまたは IoT Edge モジュール内でモデル化されている場合、コンポーネントとモジュールの情報が含まれます。
- `enrichments`:エクスポートに設定されたエンリッチメント。

Event Hubs と Service Bus の場合、IoT Central から新しいメッセージ データが、ほぼリアルタイムでイベント ハブあるいは Service Bus キューまたはトピックに送信されます。 各メッセージのユーザー プロパティ (アプリケーション プロパティとも呼ばれます) では、`iotcentral-device-id`、`iotcentral-application-id`、`iotcentral-message-source` および `iotcentral-message-type` が自動的に含まれます。

Blob Storage の場合、メッセージはバッチ処理され、1 分に 1 回エクスポートされます。

次の例は、Azure Blob Storage で受信したエクスポートされたプロパティ変更メッセージを示しています。

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "name": "MachineSerialNumber",
        "value": "abc",
        "module": "VitalsModule",
        "component": "DeviceComponent"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="device-connectivity-changes-format"></a>デバイス接続性の変更形式

各メッセージまたはレコードは、1つのデバイスからの接続イベントを表します。 エクスポートされたメッセージに含まれる情報は次のとおりです。

- `applicationId`:IoT Central アプリケーションの ID。
- `messageSource`:メッセージのソース - `deviceConnectivity`。
- `messageType`: `connected` または `disconnected` のいずれか。
- `deviceId`: 変更されたデバイスの ID。
- `schema`:ペイロード スキーマの名前とバージョン。
- `templateId`:デバイスに関連付けられているデバイス テンプレートの ID。
- `enqueuedTime`: IoT Central でこの変更が発生した時刻。
- `enrichments`:エクスポートに設定されたエンリッチメント。

Event Hubs と Service Bus の場合、IoT Central から新しいメッセージ データが、ほぼリアルタイムでイベント ハブあるいは Service Bus キューまたはトピックに送信されます。 各メッセージのユーザー プロパティ (アプリケーション プロパティとも呼ばれます) では、`iotcentral-device-id`、`iotcentral-application-id`、`iotcentral-message-source` および `iotcentral-message-type` が自動的に含まれます。

Blob Storage の場合、メッセージはバッチ処理され、1 分に 1 回エクスポートされます。

次の例は、Azure Blob Storage で受信された、エクスポート済みデバイス接続メッセージを示しています。

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceConnectivity",
  "messageType": "connected",
  "deviceId": "1vzb5ghlsg1",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-04-05T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}

```

## <a name="device-lifecycle-changes-format"></a>デバイスのライフサイクル変更の形式

各メッセージまたはレコードは、1 つのデバイスに対する 1 つの変更を表します。 エクスポートされたメッセージに含まれる情報は次のとおりです。

- `applicationId`:IoT Central アプリケーションの ID。
- `messageSource`:メッセージのソース - `deviceLifecycle`。
- `messageType`: 発生したエラーの種類。 `registered`、`deleted`、`provisioned`、`enabled`、`disabled`、`displayNameChanged` および `deviceTemplateChanged` のいずれか。
- `deviceId`: 変更されたデバイスの ID。
- `schema`:ペイロード スキーマの名前とバージョン。
- `templateId`:デバイスに関連付けられているデバイス テンプレートの ID。
- `enqueuedTime`: IoT Central でこの変更が発生した時刻。
- `enrichments`:エクスポートに設定されたエンリッチメント。

Event Hubs と Service Bus の場合、IoT Central から新しいメッセージ データが、ほぼリアルタイムでイベント ハブあるいは Service Bus キューまたはトピックに送信されます。 各メッセージのユーザー プロパティ (アプリケーション プロパティとも呼ばれます) では、`iotcentral-device-id`、`iotcentral-application-id`、`iotcentral-message-source` および `iotcentral-message-type` が自動的に含まれます。

Blob Storage の場合、メッセージはバッチ処理され、1 分に 1 回エクスポートされます。

次の例は、Azure Blob Storage で受信された、エクスポートされたデバイスのライフサイクル メッセージを示しています。

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceLifecycle",
  "messageType": "registered",
  "deviceId": "1vzb5ghlsg1",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-01-01T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}
```

## <a name="device-template-lifecycle-changes-format"></a>デバイス テンプレートのライフサイクル変更の形式

各メッセージまたはレコードは、1 つの発行されたデバイス テンプレートに対する 1 つの変更を表します。 エクスポートされたメッセージに含まれる情報は次のとおりです。

- `applicationId`:IoT Central アプリケーションの ID。
- `messageSource`:メッセージのソース - `deviceTemplateLifecycle`。
- `messageType`: `created`、`updated`、または `deleted` のいずれか。
- `schema`:ペイロード スキーマの名前とバージョン。
- `templateId`:デバイスに関連付けられているデバイス テンプレートの ID。
- `enqueuedTime`: IoT Central でこの変更が発生した時刻。
- `enrichments`:エクスポートに設定されたエンリッチメント。

Event Hubs と Service Bus の場合、IoT Central から新しいメッセージ データが、ほぼリアルタイムでイベント ハブあるいは Service Bus キューまたはトピックに送信されます。 各メッセージのユーザー プロパティ (アプリケーション プロパティとも呼ばれます) では、`iotcentral-device-id`、`iotcentral-application-id`、`iotcentral-message-source` および `iotcentral-message-type` が自動的に含まれます。

Blob Storage の場合、メッセージはバッチ処理され、1 分に 1 回エクスポートされます。

次の例は、Azure Blob Storage で受信された、エクスポートされたデバイスのライフサイクル メッセージを示しています。

```json
{
  "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
  "messageSource": "deviceTemplateLifecycle",
  "messageType": "created",
  "schema": "default@v1",
  "templateId": "urn:qugj6vbw5:___qbj_27r",
  "enqueuedTime": "2021-01-01T22:26:55.455Z",
  "enrichments": {
    "userSpecifiedKey": "sampleValue"
  }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>従来のデータ エクスポートとデータ エクスポートの比較

次の表は、[レガシ データ エクスポート](howto-export-data-legacy.md)とデータ エクスポートの機能の違いを示しています。

| 機能  | 従来のデータ エクスポート | 新しいデータ エクスポート |
| :------------- | :---------- | :----------- |
| 使用できるデータの種類 | テレメトリ、デバイス、デバイス テンプレート | テレメトリ、プロパティ変更、デバイス接続性の変更、デバイスのライフサイクルの変更、デバイス テンプレートのライフサイクルの変更 |
| フィルター処理 | なし | エクスポートするデータの種類によって異なります。 テレメトリの場合は、テレメトリ、メッセージ プロパティ、プロパティ値によるフィルター処理 |
| エンリッチメント | なし | デバイスのカスタム文字列またはプロパティ値を使用してエンリッチ化する |
| 変換先 | Azure Event Hubs、Azure Service Bus キューおよびトピック、Azure Blob Storage | 従来のデータ エクスポートと同じ宛先に加えて Webhook|
| サポートされているアプリケーションのバージョン | V2、V3 | V3 のみ |
| 重要な制限 | アプリごとに5つのエクスポート、エクスポートごとに1つのエクスポート先 | エクスポートの宛先の接続数はアプリごとに 10 個 |

## <a name="next-steps"></a>次の手順

データのエクスポートを構成する方法を理解したので、次の手順として、[IoT Central アプリケーション内のデータをエクスポート用に変換する](howto-transform-data-internally.md)について学習することをお勧めします。
