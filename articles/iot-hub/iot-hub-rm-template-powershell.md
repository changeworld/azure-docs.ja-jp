<properties
	pageTitle="Resource Manager テンプレートと PowerShell を使用した IoT Hub の作成 | Microsoft Azure"
	description="リソース マネージャー テンプレートと PowerShell を使って IoT Hub を作成する方法についてわかりやすく説明します。"
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/07/2016"
     ms.author="dobett"/>

# PowerShell を使用した IoT Hub の作成

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## はじめに

Azure リソース マネージャーを使って、Azure IoT ハブをプログラムを使用して作成、管理できます。このチュートリアルでは、Resource Manager テンプレートを使用して PowerShell で IoT Hub を作成する方法を説明します。

> [AZURE.NOTE] Azure には、リソースの作成と操作に関して 2 種類のデプロイメント モデルがあります。[リソース マネージャー デプロイメント モデルとクラシック デプロイメント モデル](../resource-manager-deployment-model.md)です。この記事では、リソース マネージャーのデプロイメント モデルの使用について説明します。

このチュートリアルを完了するには、以下が必要です。

- アクティブな Azure アカウント。<br/>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト][lnk-free-trial]を参照してください。
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] 以降

> [AZURE.TIP] PowerShell スクリプトと Resource Manager テンプレートを使って Azure のリソースを作成する方法の詳細については、「[Azure Resource Manager での Azure PowerShell の使用][lnk-powershell-arm]」の記事を参照してください。

## Azure サブスクリプションへの接続

PowerShell コマンド プロンプトから、次のコマンドを入力して Azure サブスクリプションにサインインします。

```
Login-AzureRmAccount
```

IoT Hub のデプロイ先と現在サポートされている API バージョンは、次のコマンドで調べることができます。

```
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

IoT Hub がサポートされているいずれかの場所から次のコマンドを使用し、IoT Hub の追加先となるリソース グループを作成します。この例では、**MyIoTRG1** というリソース グループを作成しています。

```
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## テンプレートを送信して IoT Hub を作成する

JSON テンプレートを使用して、リソース グループに IoT Hub を作成します。また、テンプレートを使用して、既存の IoT Hub に変更を加えることもできます。

1. テキスト エディターを使用して、**template.json** という Resource Manager テンプレートを作成し、標準的な IoT Hub を新規作成するリソース定義 (以下) を記述します。この例では、IoT Hub を**米国東部**リージョンに追加し、イベント ハブと互換性のあるエンドポイントに 2 つのコンシューマー グループ (**cg1** と **cg2**) を作成して、**2016-02-03** API バージョンを使用します。また、このテンプレートでは、**hubName** というパラメーターに IoT Hub の名前を指定する必要があります。IoT Hub をサポートする場所の最新のリストについては、「[Azure の状態][lnk-status]」を参照して下さい。

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. このテンプレート ファイルをローカル コンピューターに保存します。この例では、**c:\\templates** フォルダーに保存することを前提としています。

3. 次のコマンドを実行して、新しい IoT Hub をデプロイします。実際の IoT Hub の名前をパラメーターで指定してください。この例では、IoT Hub の名前は **abcmyiothub** です (この名前はグローバルに一意でなければならないため、自分の名前やイニシャルを含めます)。

    ```
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```

4. 作成した IoT Hub のキーが出力として表示されます。

5. アプリケーションで新しい IoT Hub が追加されたことを確認するには、[ポータル][lnk-azure-portal]にアクセスし、リソースの一覧を表示するか、**Get-AzureRmResource** PowerShell コマンドレットを使用します。

> [AZURE.NOTE] このサンプル アプリケーションでは、課金の対象とする S1 Standard IoT Hub を追加します。IoT Hub を削除するには、[ポータル][lnk-azure-portal]を使うか、終了時に **Remove-AzureRmResource** PowerShell コマンドレットを使用します。

## 次のステップ

ここでは、PowerShell と Resource Manager テンプレートを使用して IoT Hub をデプロイしました。次の手順に進んでください。

- [IoT Hub Resource Provider REST API][lnk-rest-api] の機能の詳細をご確認ください。
- Azure リソース マネージャーの機能の詳細については、「[Azure リソース マネージャーの概要][lnk-azure-rm-overview]」を参照してください。

IoT Hub の開発に関する詳細については、以下を参照してください

- [C SDK の概要][lnk-c-sdk]
- [IoT Hub SDK][lnk-sdks]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [ソリューションの設計][lnk-design]
- [サンプル UI を使用したデバイス管理の探求][lnk-dmui]
- [Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]
- [Azure ポータルを使用した IoT Hub の管理][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-powershell-arm]: ../powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0907_2016-->