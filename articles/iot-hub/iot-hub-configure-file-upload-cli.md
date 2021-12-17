---
title: Azure CLI を使用して IoT Hub へのファイルのアップロードを構成する | Microsoft Docs
description: クロスプラットフォームの Azure CLI を使用して、Azure IoT Hub へのファイルのアップロードを構成する方法です。
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: lizross
ms.openlocfilehash: c3aa2d496151d8c9d9f6ef5acfea6dee55548b2b
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555605"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Azure CLI を使用して IoT Hub ファイルのアップロードを構成する

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

この記事では、Azure CLI を使用して IoT ハブ上でファイルのアップロードを構成する方法を示します。 

[IoT Hub でファイルのアップロード機能](iot-hub-devguide-file-upload.md)を使用するには、最初に Azure ストレージ アカウントと BLOB コンテナーを IoT Hub に関連付ける必要があります。 IoT Hub により、ファイルをアップロードするときにデバイスで使用する、この BLOB コンテナーへの書き込みアクセス許可を含む SAS URI が自動的に生成します。 ストレージ アカウントと BLOB コンテナーに加えて、SAS URI の有効期限と、Azure Storage で IoT Hub が使用する認証の種類を設定できます。 IoT Hub でバックエンド サービスに配信できる、オプションのファイル アップロード通知の設定を構成することもできます。

## <a name="prerequisites"></a>前提条件

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。

* Azure IoT Hub。 IoT Hub がない場合は、[`az iot hub create` コマンド](/cli/azure/iot/hub#az_iot_hub_create)を使用して作成するか、[ポータルを使用して IoT Hub を作成](iot-hub-create-through-portal.md)できます。

* Azure Storage のアカウント Azure Storage アカウントを持っていない場合は、Azure CLI を使用して作成できます。 詳しくは、「[ストレージ アカウントの作成](../storage/common/storage-account-create.md)」をご覧ください。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="sign-in-and-set-your-azure-account"></a>サインインして Azure アカウントを設定する

Azure アカウントにサインインしてサブスクリプションを選択します。 Azure Cloud Shell を使用している場合は、既にサインインしているはずです。ただし、複数のサブスクリプションがある場合は、引き続き Azure サブスクリプションの選択が必要になる場合があります。

1. コマンド プロンプトで、[ログイン コマンド](/cli/azure/get-started-with-azure-cli)を実行します。

    ```azurecli
    az login
    ```

    指示に従って、コードを使用して認証し、Web ブラウザーで Azure アカウントにサインインします。

2. 複数の Azure サブスクリプションがある場合は、Azure にサインインすると、資格情報に関連付けられているすべてのAzure アカウントへのアクセスが許可されます。 以下の、利用できる [Azure アカウントを一覧表示するコマンド](/cli/azure/account)を使用します。

    ```azurecli
    az account list
    ```

    以下のコマンドを使用して、コマンドを実行して IoT ハブを作成するために使用するサブスクリプションを選択します。 前のコマンドの出力から、サブスクリプション名または ID のいずれかを使用できます。

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>ストレージ アカウントの詳細を取得する

次の手順は、ストレージ アカウントが、**クラシック** デプロイ モデルではなく **Resource Manager** デプロイ モデルを使用して作成されていることを前提としています。

デバイスからファイル アップロードを構成するには、Azure Storage アカウント用の接続文字列が必要です。 Azure Storage アカウントは、IoT Hub と同じサブスクリプション内にある必要があります。 また、ストレージ アカウントには BLOB コンテナーの名前も必要です。 ストレージ アカウント キーを取得するには、次のコマンドを使用します。

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```
接続文字列は、次の出力のようになります。

```json
{
  "connectionString": "DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName={your storage account name};AccountKey={your storage account key}"
}
```

`connectionString` の値を書き留めておきます。 これは、以降の手順で必要です。

ファイル アップロード用の既存の BLOB コンテナーを使用するか、新しいものを作成します。

* ストレージ アカウントの既存の BLOB コンテナーの一覧を表示するには、次のコマンドを使用します。

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* ストレージ アカウントに BLOB コンテナーを作成するには、次のコマンドを使用します。

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="configure-your-iot-hub"></a>IoT Hub を構成する

これでストレージ アカウントの詳細を使用して、IoT Hub を構成することで、[ファイルを IoT Hub にアップロード](iot-hub-devguide-file-upload.md)する機能を有効化できるようになりました。

構成するには次の値が必要です。

* **ストレージ コンテナー**: IoT Hub に関連付ける、現在の Azure サブスクリプションの Azure ストレージ アカウントの BLOB コンテナー。 必要なストレージ アカウント情報は、前のセクションで取得しました。 IoT Hub により、ファイルをアップロードするときにデバイスで使用する、この BLOB コンテナーへの書き込みアクセス許可を含む SAS URI が自動的に生成します。

* **Receive notifications for uploaded files (アップロードされたファイルに関する通知を受け取る)** : ファイルのアップロードに関する通知を有効または無効にします。

* **SAS TTL**: IoT Hub によりデバイスに返される SAS URI の有効期間を設定します。 既定では 1 時間に設定されています。

* **File notification settings default TTL (ファイルの通知設定 既定の TTL)** : 有効期限が切れるまでのファイルのアップロード通知の有効期限です。 既定では 1 日に設定されています。

* **File notification maximum delivery count (ファイルの通知設定 最大配信回数)** : IoT Hub がファイルのアップロード通知の配信を試行する回数です。 既定では 10 に設定されています。

* **File notification lock duration (ファイルの通知設定 ロック期間)** : ファイル通知キューのロック期間です。 既定では 60 秒に設定されています。

* **認証の種類**: Azure Storage で使用する IoT Hub の認証の種類です。 この設定により、IoT ハブが Azure Storage で認証および認可する方法が決定します。 既定値はキーベースの認証です。ただし、システム割り当ておよびユーザー割り当てマネージド ID も使用できます。 マネージド ID は、Azure AD で自動的に管理される ID を Azure サービスに安全な方法で提供します。 IoT ハブと Azure Storage アカウントでマネージド ID を構成する方法については、「[IoT Hub でのマネージド ID のサポート](./iot-hub-managed-identity.md)」を参照してください。 構成が完了したら、Azure Storage での認証に使用するためにマネージド ID のいずれかを設定できます。

    > [!NOTE]
    > 認証の種類の設定では、IoT ハブが Azure Storage アカウントで認証する方法を構成します。 デバイスは、IoT ハブから取得した SAS URI を使用して、常に Azure Storage で認証されます。 


次のコマンドでは、IoT ハブでファイルのアップロード設定を構成する方法を示しています。 これらのコマンドはわかりやすくするために個別に示されますが、通常は、シナリオに必要なすべてのパラメーターを含めて 1 つのコマンドを発行します。 コマンド ラインに表示する引用符を含めます。 中かっこは含めません。 各パラメーターの詳細については、Azure CLI ドキュメントで [az iot hub update](/cli/azure/iot/hub#az_iot_hub_update) コマンドについて参照してください。

次のコマンドでは、ストレージ アカウントと BLOB コンテナーを構成します。

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-connectionstring "{your storage account connection string}" \
    --fileupload-storage-container-name "{your container name}" 
```

次のコマンドでは、SAS URI の有効期限を既定値 (1 時間) に設定します。

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-sas-ttl 1 
```

次のコマンドでは、ファイル通知を有効にし、ファイル通知プロパティを既定値に設定します。 (ファイル アップロード通知の有効期限は 1 時間に設定され、ロック期間は 60 秒に設定されます。)

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-notifications true  \
    --fileupload-notification-max-delivery-count 10 \
    --fileupload-notification-ttl 1 \
    --fileupload-notification-lock-duration 60
```

次のコマンドでは、キーベースの認証を構成します。

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type keyBased
```

次のコマンドでは、IoT ハブのシステム割り当てマネージド ID を使用して認証を構成します。 このコマンドを実行する前に、IoT ハブのシステム割り当てマネージド ID を有効にして、Azure Storage アカウントに適切な RBAC ロールを付与する必要があります。 詳細については、「[IoT Hub でのマネージド ID のサポート](./iot-hub-managed-identity.md)」を参照してください。

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type identityBased \
    --fileupload-storage-identity [system] 
```

次のコマンドでは、IoT ハブで構成されているユーザー割り当てマネージド ID を取得し、そのうちのいずれかを使用した認証を構成します。 ユーザー割り当てマネージド ID を使用して認証するには、事前にそれを IoT ハブで構成し、Azure Storage アカウントに適切な RBAC ロールを付与する必要があります。 詳細と手順については、「[IoT Hub でのマネージド ID のサポート](./iot-hub-managed-identity.md)」を参照してください。

IoT ハブでユーザー割り当てマネージド ID を照会するには、[az iot hub identity show](/cli/azure/iot/hub/identity#az_iot_hub_identity_show) コマンドを使用します。

```azurecli
az iot hub identity show --name {your iot hub name} --query userAssignedIdentities
```

このコマンドでは、IoT ハブで構成されたユーザー割り当てマネージド ID のコレクションを返します。 次の出力では、1 つのユーザー割り当てマネージド ID を含むコレクションを示しています。

```json
{
  "/subscriptions/{your subscription ID}/resourcegroups/{your resource group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{your user-assigned managed identity name}": 
  {
    "clientId": "<client ID GUID>",
    "principalId": "<principal ID GUID>"
  }
}
```

次のコマンドでは、上記のユーザー割り当て ID を使用する認証を構成します。

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type identityBased \
    --fileupload-storage-identity  "/subscriptions/{your subscription ID}/resourcegroups/{your resource group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{your user-assigned managed identity name}"
```

次のコマンドを使用して、IoT ハブの設定を確認できます。

```azurecli
az iot hub show --name {your iot hub name}
```

ファイルのアップロード設定のみを確認するには、次のコマンドを使用します。

```azurecli
az iot hub show --name {your iot hub name}
    --query '[properties.storageEndpoints, properties.enableFileUploadNotifications, properties.messagingEndpoints.fileNotifications]'
```

Azure CLI のコマンドで名前付きパラメーターを使用するのが最も簡単です。ただし、`--set` パラメーターを使用してファイルのアップロード設定を構成することもできます。 次のコマンドは、その方法を理解するために役立ちます。 

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

## <a name="next-steps"></a>次のステップ

* [デバイス概要からファイルをアップロードする](iot-hub-devguide-file-upload.md)
* [IoT Hub でのマネージド ID のサポート](./iot-hub-managed-identity.md)
* [ファイル アップロードのハウツー ガイド](./iot-hub-csharp-csharp-file-upload.md)
* Azure CLI の [az iot hub update](/cli/azure/iot/hub#az_iot_hub_update)、[az iot hub identity show](/cli/azure/iot/hub/identity#az_iot_hub_identity_show)、[az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) コマンド
