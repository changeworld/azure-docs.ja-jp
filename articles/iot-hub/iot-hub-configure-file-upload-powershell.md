---
title: Azure PowerShell を使用してファイルのアップロードを構成する | Microsoft Docs
description: Azure PowerShell コマンドレットを使用して IoT Hub を構成し、接続されているデバイスからファイルのアップロードを有効にする方法。 対象の Azure ストレージ アカウントの構成に関する情報が含まれています。
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: lizross
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f20236edd7a905f05d59ddafd3132569a851816a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553935"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>PowerShell を使用して IoT Hub ファイルのアップロードを構成する

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

この記事では、PowerShell を使用して IoT ハブ上でファイルのアップロードを構成する方法を示します。 

[IoT Hub でファイルのアップロード機能](iot-hub-devguide-file-upload.md)を使用するには、最初に Azure ストレージ アカウントと BLOB コンテナーを IoT ハブに関連付ける必要があります。 IoT Hub により、ファイルをアップロードするときにデバイスで使用する、この BLOB コンテナーへの書き込みアクセス許可を含む SAS URI が自動的に生成します。 ストレージ アカウントと BLOB コンテナーに加えて、SAS URI の Time to Live を設定し、IoT Hub がバックエンド サービスに配信できるオプションのファイル アップロード通知の設定を構成できます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。

* Azure IoT Hub。 IoT Hub がない場合は、[New-AzIoTHub コマンドレット](/powershell/module/az.iothub/new-aziothub)を使用するか、ポータルを使用して、[IoT Hub を作成](iot-hub-create-through-portal.md)できます。

* Azure ストレージ アカウント。 Azure ストレージ アカウントがない場合は、[Azure Storage PowerShell コマンドレット](/powershell/module/az.storage/)を使用するか、ポータルを使用して[ストレージ アカウントを作成](../storage/common/storage-account-create.md)できます

* [Azure Cloud Shell](../cloud-shell/quickstart-powershell.md) で PowerShell 環境を使用します。

   [![新しいウィンドウで Cloud Shell を起動する](./media/iot-hub-configure-file-upload-powershell/hdi-launch-cloud-shell.png)](https://shell.azure.com)

* 必要に応じて、PowerShell をローカルに[インストール](/powershell/scripting/install/installing-powershell)します。

  * [Azure Az PowerShell モジュールをインストールします](/powershell/azure/install-az-ps)。 (モジュールは既定で Azure Cloud Shell PowerShell 環境にインストールされます。) 
  * [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドを使用して PowerShell にサインインします。  認証プロセスを完了するには、ターミナルに表示される手順に従います。  その他のサインイン オプションについては、「[Azure PowerShell でのサインイン](/powershell/azure/authenticate-azureps)」を参照してください。


## <a name="sign-in-and-set-your-azure-account"></a>サインインして Azure アカウントを設定する

Azure アカウントにサインインしてサブスクリプションを選択します。 Azure Cloud Shell を使用している場合は、既にサインインしているはずです。ただし、複数のサブスクリプションがある場合は、引き続き Azure サブスクリプションの選択が必要になる場合があります。

1. PowerShell プロンプトで、**Connect-AzAccount** コマンドレットを実行します。

    ```powershell
    Connect-AzAccount
    ```

2. 複数の Azure サブスクリプションがある場合は、Azure にサインインすると、資格情報に関連付けられているすべての Azure サブスクリプションへのアクセスが許可されます。 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) コマンドを使用して、使用できる Azure サブスクリプションの一覧を表示します。

    ```powershell
    Get-AzSubscription
    ```

    次のコマンドで、使用するサブスクリプションを選択して、IoT Hub を管理するためのコマンドを実行します。 前のコマンドの出力から、サブスクリプション名または ID のいずれかを使用できます。

    ```powershell
    Select-AzSubscription `
        -Name "{your subscription name}"
    ```

    > [!NOTE]
    > **Select-AzSubscription** コマンドは [Select-AzContext](/powershell/module/az.accounts/select-azcontext) の別名であり、**Select-AzContext** コマンドに必要な複雑なコンテキスト名ではなく、**Get-AzSubscription** コマンドによって返されるサブスクリプション名 (**Name**) またはサブスクリプション ID (**Id**) を使用できます。

## <a name="retrieve-your-storage-account-details"></a>ストレージ アカウントの詳細を取得する

次の手順は、ストレージ アカウントが、**クラシック** デプロイ モデルではなく **Resource Manager** デプロイ モデルを使用して作成されていることを前提としています。

デバイスからファイル アップロードを構成するには、Azure Storage アカウント用の接続文字列が必要です。 Azure Storage アカウントは、IoT Hub と同じサブスクリプション内にある必要があります。 また、ストレージ アカウントには BLOB コンテナーの名前も必要です。 [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) コマンドを使用してストレージ アカウント キーを取得します。

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

**key1** ストレージ アカウント キーの値を書き留めておきます。 これは、以降の手順で必要です。

ファイル アップロード用の既存の BLOB コンテナーを使用するか、新しいものを作成します。

* ストレージ アカウント内の既存の BLOB コンテナーの一覧を表示するには、[New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) および [Get-AzStorageContainer](/powershell/module/az.storage/get-azstoragecontainer) コマンドを使用します。

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* ストレージ アカウント内に BLOB コンテナーを作成するには、[New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) および [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) コマンドを使用します。

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>IoT Hub を構成する

次は、ストレージ アカウントの詳細を使用して[ファイルを IoT Hub にアップロード](iot-hub-devguide-file-upload.md)するように、IoT Hub を構成します。

構成するには次の値が必要です。

* **ストレージ コンテナー**: IoT Hub に関連付ける、現在の Azure サブスクリプションの Azure ストレージ アカウントの BLOB コンテナー。 必要なストレージ アカウント情報は、前のセクションで取得しました。 IoT Hub により、ファイルをアップロードするときにデバイスで使用する、この BLOB コンテナーへの書き込みアクセス許可を含む SAS URI が自動的に生成します。

* **Receive notifications for uploaded files (アップロードされたファイルに関する通知を受け取る)** : ファイルのアップロードに関する通知を有効または無効にします。

* **SAS TTL**: IoT Hub によりデバイスに返される SAS URI の有効期間を設定します。 既定では 1 時間に設定されています。

* **File notification settings default TTL (ファイルの通知設定 既定の TTL)** : 有効期限が切れるまでのファイルのアップロード通知の有効期間です。 既定では 1 日に設定されています。

* **File notification maximum delivery count (ファイルの通知設定 最大配信回数)** : IoT Hub がファイルのアップロード通知の配信を試行する回数です。 既定では 10 に設定されています。

IoT ハブ上でファイルのアップロード設定を構成するには、[Set-AzIotHub](/powershell/module/az.iothub/set-aziothub) コマンドを使用します。

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

> [!NOTE]
> 既定では、接続文字列内でアカウント キーを使用して IoT Hub を Azure Storage で認証します。 システム割り当てまたはユーザー割り当てのマネージド ID を使用した認証も使用できます。 マネージド ID は、Azure AD で自動的に管理される ID を Azure サービスに安全な方法で提供します。 詳細については、「[IoT Hub でのマネージド ID のサポート](./iot-hub-managed-identity.md)」を参照してください。 現時点では、**Set-AzIotHub** コマンドには認証の種類を設定するパラメーターがありません。 代わりに、[Azure portal](./iot-hub-configure-file-upload.md) または [Azure CLI](./iot-hub-configure-file-upload-cli.md) を使用できます。 

## <a name="next-steps"></a>次のステップ

* [デバイス概要からファイルをアップロードする](iot-hub-devguide-file-upload.md)
* [IoT Hub でのマネージド ID のサポート](./iot-hub-managed-identity.md)
* [ファイル アップロードのハウツー ガイド](./iot-hub-csharp-csharp-file-upload.md)