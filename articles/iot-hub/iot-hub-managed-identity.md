---
title: Azure IoT Hub のマネージド ID | Microsoft Docs
description: マネージド ID を使用して IoT Hub から他の Azure リソースへのエグレス接続を可能にする方法。
author: miag
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: miag
ms.openlocfilehash: b6f050c99d57e077e9e60aeb70d0acb11d200adb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072753"
---
# <a name="iot-hub-support-for-managed-identities"></a>IoT Hub でのマネージド ID のサポート 

マネージド ID は、Azure AD で自動的に管理される ID を Azure サービスに安全な方法で提供します。 これにより、開発者は、ID を指定して資格情報を管理する必要がなくなります。 マネージド ID には、システム割り当てとユーザー割り当ての 2 種類があります。 IoT Hub では両方がサポートされます。 

IoT Hub では、[メッセージ ルーティング](iot-hub-devguide-messages-d2c.md)、[ファイルのアップロード](iot-hub-devguide-file-upload.md)、[デバイスの一括インポートおよびエクスポート](iot-hub-bulk-identity-mgmt.md)などの機能のために、IoT Hub ハブから他の Azure サービスへのエグレス接続にマネージド ID を使用できます。 この記事では、IoT ハブでさまざまな機能にシステム割り当ておよびユーザー割り当てのマネージド ID を使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

- [Azure リソースのマネージド ID](./../active-directory/managed-identities-azure-resources/overview.md) に関するドキュメントを読み、システム割り当て ID とユーザー割り当てのマネージド ID の違いを理解しておいてください。

- IoT ハブがない場合は、先に進む前に[作成](iot-hub-create-through-portal.md)してください。

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

### <a name="add-and-remove-a-system-assigned-managed-identity-in-azure-portal"></a>Azure portal でシステム割り当てマネージド ID を追加および削除する

1. Azure portal にサインインし、目的の IoT ハブに移動します。
2. IoT Hub ポータルで **[ID]** に移動します。
3. **[システム割り当て]** タブで **[オン]** を選択し、 **[保存]** をクリックします。
4. IoT ハブからシステム割り当てマネージド ID を削除するには、 **[オフ]** を選択し、 **[保存]** をクリックします。

    :::image type="content" source="./media/iot-hub-managed-identity/system-assigned.png" alt-text="I O T ハブに対してシステム割り当て済みマネージド id をオンにする場所を示すスクリーンショット。":::

### <a name="enable-system-assigned-managed-identity-at-hub-creation-time-using-arm-template"></a>ARM テンプレートを使用してハブの作成時にシステム割り当てマネージド ID を有効にする

リソースのプロビジョニング時に IoT ハブでシステム割り当てマネージド ID を有効にするには、次の Azure Resource Manager (ARM) テンプレートを使用します。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": 
    {
      "iotHubName": {
        "type": "string",
        "metadata": {
          "description": "Name of iothub resource"
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "S1",
        "metadata": {
          "description": "SKU name of iothub resource, by default is Standard S1"
        }
      },
      "skuTier": {
        "type": "string",
        "defaultValue": "Standard",
        "metadata": {
          "description": "SKU tier of iothub resource, by default is Standard"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location of iothub resource. Please provide any of supported-regions of iothub"
        }
      }
    },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "createIotHub",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2021-03-31",
              "name": "[parameters('iotHubName')]",
              "location": "[parameters('location')]",
              "identity": {
                "type": "SystemAssigned"
              },
              "sku": {
              "name": "[parameters('skuName')]",
              "tier": "[parameters('skuTier')]",
              "capacity": 1
              }
            }
          ] 
        }
      }
    }
  ]
}
```

リソースの `name`、`location`、`SKU.name`、`SKU.tier` の値を置き換えた後、以下の Azure CLI を使用して、既存のリソース グループにリソースをデプロイすることができます。

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json> --parameters iotHubName=<valid-iothub-name> skuName=<sku-name> skuTier=<sku-tier> location=<any-of-supported-regions>
```

リソースが作成されたら、Azure CLI を使用して、ハブに割り当てられたシステム割り当てマネージド ID を取得できます。

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

## <a name="user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID

このセクションでは、Azure portal を使用して、IoT ハブとの間でユーザー割り当てマネージド ID を追加および削除する方法について説明します。

1. 最初に、スタンドアロン リソースとしてユーザー割り当てマネージド ID を作成する必要があります。 それを行うには、「[ユーザー割り当てマネージド ID を作成する](./../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#create-a-user-assigned-managed-identity)」の手順に従います。
2. IoT ハブにアクセスし、IoT Hub ポータルで **[ID]** に移動します。
3. **[ユーザー割り当て]** タブで、 **[ユーザー割り当てマネージド ID の関連付け]** をクリックします。 ハブに追加するユーザー割り当てマネージド ID を選択し、 **[選択]** をクリックします。 
4. IoT ハブからユーザー割り当て ID を削除できます。 削除するユーザー割り当て ID を選択し、 **[削除]** ボタンをクリックします。 この削除では、ユーザー割り当て ID は IoT ハブから削除されるだけで、リソースとしては削除されないことに注意してください。 リソースとしてのユーザー割り当て ID を削除するには、「[ユーザー割り当てマネージド ID を削除する](./../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#delete-a-user-assigned-managed-identity)」の手順に従います。

    :::image type="content" source="./media/iot-hub-managed-identity/user-assigned.png" alt-text="I O T ハブのユーザー割り当てマネージド id を追加する方法を示すスクリーンショット。":::

### <a name="enable-user-assigned-managed-identity-at-hub-creation-time-using-arm-template"></a>ARM テンプレートを使用してハブの作成時にユーザー割り当てマネージド ID を有効にする

ユーザー割り当てマネージド ID を使用するハブを作成するために使用できるテンプレートの例を次に示します。 このテンプレートでは、作成された IoT ハブに割り当てられる、 *[iothub-name-provided]-identity* という名前のユーザー割り当て ID が 1 つ作成されます。 テンプレートを変更して、必要に応じて複数のユーザー割り当て ID を追加できます。
 
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "iotHubName": {
      "type": "string",
      "metadata": {
        "description": "Name of iothub resource"
      }
    },
  "skuName": {
    "type": "string",
    "defaultValue": "S1",
    "metadata": {
      "description": "SKU name of iothub resource, by default is Standard S1"
    }
  },
  "skuTier": {
    "type": "string",
    "defaultValue": "Standard",
    "metadata": {
      "description": "SKU tier of iothub resource, by default is Standard"
    }
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]",
    "metadata": {
      "description": "Location of iothub resource. Please provide any of supported-regions of iothub"
    }
  }
},
  "variables": {
    "identityName": "[concat(parameters('iotHubName'), '-identity')]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "createIotHub",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
              "name": "[variables('identityName')]",
              "apiVersion": "2018-11-30",
              "location": "[resourceGroup().location]"
            },
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2021-03-31",
              "name": "[parameters('iotHubName')]",
              "location": "[parameters('location')]",
              "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                  "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('identityName'))]": {}
                }
              },
              "sku": {
                "name": "[parameters('skuName')]",
                "tier": "[parameters('skuTier')]",
                "capacity": 1
              },
              "dependsOn": [
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('identityName'))]"
              ]
            }
          ]
        }
      }
    }
  ]
}
```

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json> --parameters iotHubName=<valid-iothub-name> skuName=<sku-name> skuTier=<sku-tier> location=<any-of-supported-regions>
```

リソースが作成されたら、Azure CLI を使用して、ハブのユーザー割り当てマネージド ID を取得できます。

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>IoT Hub から他の Azure リソースへのエグレス接続

[メッセージ ルーティング](iot-hub-devguide-messages-d2c.md)、[ファイルのアップロード](iot-hub-devguide-file-upload.md)、[デバイスの一括インポートおよびエクスポート](iot-hub-bulk-identity-mgmt.md)のために、IoT Hub ハブから他の Azure サービスへのエグレス接続にマネージド ID を使用できます。 顧客所有のエンドポイント (ストレージ アカウント、イベント ハブ、サービス バス エンドポイントなど) への IoT Hub エグレス接続ごとに、使用するマネージド ID を選択できます。

> [!NOTE]
> プライベート リソースにアクセスするには、システム割り当てマネージド ID を使用します。

## <a name="configure-message-routing-with-managed-identities"></a>マネージド ID を使用してメッセージ ルーティングを構成する

このセクションでは、例として、イベント ハブ カスタム エンドポイントへの[メッセージ ルーティング](iot-hub-devguide-messages-d2c.md)を使用します。 この例は、他のルーティング カスタム エンドポイントにも当てはまります。 

1. まず、Azure portal でご使用のイベント ハブに移動し、マネージド ID に適切なアクセス権を割り当てる必要があります。 イベント ハブで、 **[アクセスの制御 (IAM)]** タブに移動し、 **[追加]** 、 **[ロールの割り当ての追加]** の順にクリックします。 ロールを割り当てるためのアクセス許可がない場合は、[ロールの割り当ての追加] オプションは無効になります。

2. **[ロール] として [Event Hubs Data Sender]\(Event Hubs のデータ送信者\)** を選択します。

    > [!NOTE]
    > ストレージ アカウントの場合は、 **[ロール]** として **[ストレージ BLOB データ共同作成者]** を選択します ([ [共同作成者] または [ストレージ アカウント共同作成者] では "*ありません*"](../storage/blobs/assign-azure-role-data-access.md))。 サービス バスの場合は、 **[ロール]** として **[Azure Service Bus のデータ送信者]** を選択します。

3. ユーザー割り当ての場合、 **[アクセスの割り当て先]** で **[ユーザー割り当てマネージド ID]** を選択します。 ドロップダウン リストで、ご使用のサブスクリプションとユーザー割り当てマネージド ID を選択します。 **[保存]** ボタンをクリックします。

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-iam-user-assigned.png" alt-text="ユーザーが割り当てられたメッセージルーティングを示すスクリーンショット。":::

4. システム割り当ての場合、 **[アクセスの割り当て先]** で **[ユーザー、グループ、またはサービス プリンシパル]** を選択し、ドロップダウン リストで、ご使用の IoT Hub のリソース名を選択します。 **[保存]** をクリックします。

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-iam-system-assigned.png" alt-text="システムが割り当てられたメッセージのルーティングを示すスクリーンショット。":::

    VNet を介したカスタム エンドポイントへの接続を制限する必要がある場合は、信頼できる Microsoft ファースト パーティの例外を有効にして、ご使用の IoT ハブに特定のエンドポイントへのアクセス権を付与する必要があります。 たとえば、イベント ハブ カスタムエンド ポイントを追加する場合、イベント ハブの **[ファイアウォールと仮想ネットワーク]** タブに移動し、 **[選択したネットワークからのアクセスを許可する]** オプションを有効にします。 **[例外]** 一覧で、 **[Allow trusted Microsoft services to access event hubs]\(信頼された Microsoft サービスによる Event Hubs に対するアクセスを許可します\)** のボックスをオンにします。 **[保存]** ボタンをクリックします。 これは、ストレージ アカウントとサービス バスにも当てはまります。 詳細については、[IoT Hub での仮想ネットワークのサポート](./virtual-network-support.md)に関するページを参照してください。

    > [!NOTE]
    > イベント ハブを IoT Hub のカスタム エンドポイントとして追加する前に、上記の手順を完了して、マネージ ID に適切なアクセスを割り当てる必要があります。 ロールの割り当てが反映されるまで数分お待ちください。

5. 次に、IoT ハブに移動します。 ご使用のハブで、 **[メッセージ ルーティング]** に移動し、 **[カスタム エンドポイント]** をクリックします。 **[追加]** をクリックして、使用するエンドポイントの種類を選択します。 このセクションでは、例としてイベント ハブを使用します。

6. ページの下部で、希望する **[認証の種類]** を選択します。 このセクションでは、例として、 **[ユーザー割り当て]** を使用します。 ドロップダウンで、優先するユーザー割り当てマネージド ID を選択し、 **[作成]** をクリックします。

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-routing-endpoint.png" alt-text="ユーザーが割り当てられているイベントハブを示すスクリーンショット。":::

7. カスタム エンドポイントが正常に作成されました。

8. 認証の種類は、作成した後でも変更できます。 左側のナビゲーションウィンドウで **[メッセージのルーティング]** を選択し、次に **[カスタムエンドポイント]** を選択します。 認証の種類を変更するカスタムエンドポイントを選択し、[認証の **種類の変更**] をクリックします。

9. このエンドポイントに対して更新する新しい認証の種類を選択し、 **[保存]** をクリックします。

## <a name="configure-file-upload-with-managed-identities"></a>マネージド ID を使用してファイルのアップロードを構成する

IoT Hub の[ファイルのアップロード](iot-hub-devguide-file-upload.md)機能を使用すると、デバイスで顧客所有のストレージ アカウントにファイルをアップロードできます。 ファイルのアップロードを機能させるには、IoT Hub でストレージ アカウントに接続する必要があります。 メッセージ ルーティングと同様に、Azure Storage アカウントへの IoT Hub エグレス接続のための優先する認証の種類とマネージド ID を選択できます。 

1. Azure portal で、ストレージ アカウントの **[アクセス制御 (IAM)]** タブに移動し、 **[ロールの割り当てを追加する]** セクションの下にある **[追加]** をクリックします。
2. [ロール] として **[ストレージ BLOB データ共同作成者]** を選択します ([共同作成者] または [ストレージ アカウント共同作成者] ではありません)。
3. ユーザー割り当ての場合、[アクセスの割り当て先] で **[ユーザー割り当てマネージド ID]** を選択します。 ドロップダウン リストで、ご使用のサブスクリプションとユーザー割り当てマネージド ID を選択します。 **[保存]** ボタンをクリックします。
4. システム割り当ての場合、 **[アクセスの割り当て先]** で **[ユーザー、グループ、またはサービス プリンシパル]** を選択し、ドロップダウン リストで、ご使用の IoT ハブのリソース名を選択します。 **[保存]** をクリックします。

    VNet を介したストレージ アカウントへの接続を制限する必要がある場合は、信頼できる Microsoft ファースト パーティの例外を有効にして、ご使用の IoT ハブにストレージ アカウントへのアクセス権を付与する必要があります。 ご使用のストレージ アカウントのリソース ページで、 **[ファイアウォールと仮想ネットワーク]** タブに移動し、 **[選択したネットワークからのアクセスを許可する]** オプションを有効にします。 **[例外]** 一覧で、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** のボックスをオンにします。 **[保存]** ボタンをクリックします。 詳細については、[IoT Hub での仮想ネットワークのサポート](./virtual-network-support.md)に関するページを参照してください。 

    > [!NOTE]
    > マネージド ID を使用するファイルのアップロードのためにストレージ アカウントを IoT Hub に保存する前に、上記の手順を完了して、マネージド ID に適切なアクセスを割り当てる必要があります。 ロールの割り当てが反映されるまで数分お待ちください。
5. ご使用の IoT Hub のリソース ページで、 **[ファイルのアップロード]** タブに移動します。
6. 表示されたページで、BLOB ストレージで使用する予定のコンテナーを選択し、必要に応じて **[ファイル通知の設定]、[SAS TTL]、[既定の TTL]、[最大配信回数]** を構成します。 優先する認証の種類を選択し、 **[保存]** をクリックします。 この手順でエラーが発生した場合は、一時的にストレージ アカウントを設定して、**すべてのネットワーク** からのアクセスを許可してから、再試行してください。 ファイルのアップロードの構成が完了したら、ストレージ アカウントでファイアウォールを構成できます。

    :::image type="content" source="./media/iot-hub-managed-identity/file-upload.png" alt-text="Msi を使用したファイルのアップロードを示すスクリーンショット。":::

    > [!NOTE]
    > ファイルのアップロードのシナリオでは、ハブとデバイスの両方がストレージ アカウントに接続する必要があります。 上記の手順は、必要な認証の種類を使用して、IoT ハブをストレージ アカウントに接続するためのものです。 その場合も、SAS URI を使用してデバイスをストレージに接続する必要があります。 現在、SAS URI は接続文字列を使用して生成されます。 マネージド ID を使用した SAS URI の生成のサポートが近日中に追加される予定です。 [ファイルのアップロード](iot-hub-devguide-file-upload.md)の手順に従ってください。

## <a name="configure-bulk-device-importexport-with-managed-identities"></a>マネージド ID を使用してデバイスの一括インポートとエクスポートを構成する

IoT Hub では、顧客指定のストレージ BLOB 間で、一括して[デバイスの情報をインポートおよびエクスポートする](iot-hub-bulk-identity-mgmt.md)機能がサポートされています。 この機能では、IoT Hub からストレージ アカウントへの接続が必要です。 

1. Azure portal で、ストレージ アカウントの **[アクセス制御 (IAM)]** タブに移動し、 **[ロールの割り当てを追加する]** セクションの下にある **[追加]** をクリックします。
2. [ロール] として **[ストレージ BLOB データ共同作成者]** を選択します ([共同作成者] または [ストレージ アカウント共同作成者] ではありません)。
3. ユーザー割り当ての場合、[アクセスの割り当て先] で **[ユーザー割り当てマネージド ID]** を選択します。 ドロップダウン リストで、ご使用のサブスクリプションとユーザー割り当てマネージド ID を選択します。 **[保存]** ボタンをクリックします。
4. システム割り当ての場合、 **[アクセスの割り当て先]** で **[ユーザー、グループ、またはサービス プリンシパル]** を選択し、ドロップダウン リストで、ご使用の IoT ハブのリソース名を選択します。 **[保存]** をクリックします。

### <a name="using-rest-api-or-sdk-for-import-and-export-jobs"></a>インポートおよびエクスポートのジョブに REST API または SDK を使用する

インポートおよびエクスポート ジョブを作成するために Azure IoT REST API を使用できるようになりました。 要求本文で次のプロパティを指定する必要があります。

- **storageAuthenticationType**: 値を **identityBased** に設定します。 
- **inputBlobContainerUri**: このプロパティは、インポート ジョブでのみ設定します。
- **outputBlobContainerUri**: このプロパティは、インポートとエクスポートの両方のジョブに対して設定します。
- **identity**: 値を、使用するマネージド ID に設定します。

Azure IoT Hub SDK では、サービス クライアントのレジストリ マネージャーでもこの機能がサポートされます。 次のコード スニペットでは、C# SDK を使用してインポート ジョブまたはエクスポート ジョブを開始する方法を示しています。

**C# のコード スニペット**

```csharp
    // Create an export job
 
    using RegistryManager srcRegistryManager = RegistryManager.CreateFromConnectionString(hubConnectionString);
 
    JobProperties jobProperties = JobProperties.CreateForExportJob(
        outputBlobContainerUri: blobContainerUri,
        excludeKeysInExport: false,
        storageAuthenticationType: StorageAuthenticationType.IdentityBased,
        identity: new ManagedIdentity
        {
            userAssignedIdentity = userDefinedManagedIdentityResourceId
        });
```
 
```csharp
    // Create an import job
    
    using RegistryManager destRegistryManager = RegistryManager.CreateFromConnectionString(hubConnectionString);
 
    JobProperties jobProperties = JobProperties.CreateForImportJob(
        inputBlobContainerUri: blobContainerUri,
        outputBlobContainerUri: blobContainerUri,
        storageAuthenticationType: StorageAuthenticationType.IdentityBased,
        identity: new ManagedIdentity
        {
            userAssignedIdentity = userDefinedManagedIdentityResourceId
        });
```  

**Python のコード スニペット**

```python
# see note below
iothub_job_manager = IoTHubJobManager("<IoT Hub connection string>")

# Create an import job
result = iothub_job_manager.create_import_export_job(JobProperties(
    type="import",
    input_blob_container_uri="<input container URI>",
    output_blob_container_uri="<output container URI>",
    storage_authentication_type="identityBased",
    identity=ManagedIdentity(
        user_assigned_identity="<resource ID of user assigned managed identity>"
    )
))

# Create an export job
result = iothub_job_manager.create_import_export_job(JobProperties(
    type="export",
    output_blob_container_uri="<output container URI>",
    storage_authentication_type="identityBased",
    exclude_keys_in_export=True,
    identity=ManagedIdentity(
        user_assigned_identity="<resource ID of user assigned managed identity>"
    ) 
))
```

> [!NOTE]
> - **storageAuthenticationType** が **identityBased** に設定され、**userAssignedIdentity** プロパティが **null** ではない場合、ジョブでは、指定されたユーザー割り当てマネージド ID が使用されます。
> - IoT ハブが、**userAssignedIdentity** で指定されたユーザー割り当てマネージド ID を使用して構成されていない場合、ジョブは失敗します。
> - **storageAuthenticationType** が **identityBased** に設定され、**userAssignedIdentity** が null の場合、ジョブではシステム割り当て ID が使用されます。
> - IoT ハブが、ユーザー割り当てマネージド ID を使用して構成されていない場合、ジョブは失敗します。
> - **storageAuthenticationType** が **identityBased** に設定され、ハブで **ユーザー割り当て** と **システム割り当て** のどちらのマネージド ID も構成されていない場合、ジョブは失敗します。

## <a name="sdk-samples"></a>SDK のサンプル
- [.NET SDK のサンプル](https://aka.ms/iothubmsicsharpsample)
- [Java SDK のサンプル](https://aka.ms/iothubmsijavasample)
- [Python SDK のサンプル](https://aka.ms/iothubmsipythonsample)
- Node.js SDK のサンプル: [デバイスの一括インポート](https://aka.ms/iothubmsinodesampleimport)、[デバイスの一括エクスポート](https://aka.ms/iothubmsinodesampleexport)

## <a name="next-steps"></a>次のステップ

IoT Hub の機能の詳細について、下記のリンク使用してください。

* [メッセージ ルーティング](./iot-hub-devguide-messages-d2c.md)
* [ファイルのアップロード](./iot-hub-devguide-file-upload.md)
* [デバイスの一括デインポートとエクスポート](./iot-hub-bulk-identity-mgmt.md)
