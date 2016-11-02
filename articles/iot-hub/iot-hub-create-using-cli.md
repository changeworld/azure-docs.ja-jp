<properties
    pageTitle="CLI を使用した IoT Hub の作成 | Microsoft Azure"
    description="Azure コマンド ライン インターフェイスを使用して IoT Hub を作成するには、この記事に従ってください。"
    services="iot-hub"
    documentationCenter=".net"
    authors="BeatriceOltean"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/21/2016"
     ms.author="boltean"/>


# <a name="create-an-iot-hub-using-cli"></a>CLI を使用した IoT Hub の作成

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>はじめに

Azure コマンド ライン インターフェイスを使って、Azure IoT ハブをプログラムを使用して作成、管理できます。 この記事では、Azure CLI を使用して IoT Hub を作成する方法を説明します。

このチュートリアルを完了するには、以下が必要です。

- アクティブな Azure アカウント。 数分で [Azure の無料試用版][lnk-free-trial]のアカウントを作成できます。
- [Azure CLI 0.10.4][lnk-CLI-install] 以降。 既に Azure CLI がある場合は、コマンド プロンプトで次のコマンドを使用して、現在のバージョンを確認できます。
```
    azure --version
```

> [AZURE.NOTE] Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../resource-manager-deployment-model.md) 2 種類のデプロイメント モデルがあります。 Azure CLI は、Azure Resource Manager モードである必要があります。
```
    azure config mode arm
```

## <a name="set-your-azure-account-and-subscription"></a>Azure アカウントとサブスクリプションを設定する 

1. コマンド プロンプトで、次のコマンドを入力してログインします。
```
    azure login
```
認証には、推奨される Web ブラウザーとコードを使用します。

2. 複数の Azure サブスクリプションがある場合は、Azure に接続すると、資格情報に関連付けられているすべてのサブスクリプションへのアクセスが許可されます。 コマンドを使用すると、すべてのサブスクリプションを表示でき、既定のサブスクリプションも確認できます。
```
    azure account list 
```

コマンドの残りの部分を実行するサブスクリプションのコンテキストを設定するには、次を使用します。

```
    azure account set <subscription name>
```

3. リソース グループがない場合は、**exampleResourceGroup** という名前のリソース グループを作成できます。 
```
    azure group create -n exampleResourceGroup -l westus
```

> [AZURE.TIP] 「[Azure CLI を使用して Azure のリソースとリソース グループを管理する][lnk-CLI-arm]」の記事には、Azure CLI を使用して Azure リソースを管理する方法の詳細が記載されています。 


## <a name="create-and-iot-hub"></a>IoT Hub の作成

必要なパラメーターは以下のとおりです。

```
 azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>  
    - <resourceGroup> The resource group name (case insensitive alphanumeric, underscore and hyphen, 1-64 length)
    - <name> (The name of the IoT hub to be created. The format is case insensitive alphanumeric, underscore and hyphen, 3-50 length )
    - <location> (The location (azure region/datacenter) where the IoT hub will be provisioned.
    - <sku-name> (The name of the sku, one of: [F1, S1, S2, S3] etc. For the latest full list refer to the pricing page for IoT Hub.
    - <units> (The number of provisioned units. Range : F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. IoT Hub units are based on your total message count and the number of devices you want to connect.)
```
作成に使用できるすべてのパラメーターを表示するには、コマンド プロンプトでヘルプ コマンドを使用します。
```
    azure iothub create -h 
```
簡単な例:

 リソース グループ **exampleResourceGroup** に **exampleIoTHubName** という名前の IoT Hub を作成するには、次のコマンドを実行します。
```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [AZURE.NOTE] この CLI コマンドでは、課金の対象とする S1 Standard IoT Hub を作成します。 次のコマンドを使用すると、IoT Hub **exampleIoTHubName** を削除できます。 
```
    azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
```


## <a name="next-steps"></a>次のステップ
IoT Hub の開発に関する詳細については、以下を参照してください
- [IoT Hub SDK][lnk-sdks]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

- [Azure Portal を使用した IoT Hub の管理][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-CLI-arm]: ../xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 



<!--HONumber=Oct16_HO2-->


