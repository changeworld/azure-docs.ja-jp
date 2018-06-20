---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: chrissie926
manager: timlt
ms.service: iot-hub
ms.topic: include
ms.date: 04/26/2018
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: d2b409c7454645893665b080b927998402056cdd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34666910"
---
## <a name="create-a-module-identity"></a>モジュール ID を作成する

このセクションでは、IoT ハブの ID レジストリにデバイス ID とモジュール ID を作成する .NET コンソール アプリケーションを作成します。 IoT ハブに接続するデバイスまたはモジュールは、あらかじめ ID レジストリに登録されている必要があります。 詳細については、[IoT Hub 開発者ガイド][lnk-devguide-identity]の ID レジストリに関するセクションをご覧ください。 このコンソール アプリを実行すると、デバイスとモジュール両方に対して一意のデバイス ID とキーが生成されます。 デバイスとモジュールは、IoT ハブに device-to-cloud メッセージを送信するときにこれらの値を使用して自分自身を識別します。 ID には大文字と小文字の区別があります。


1. **Visual Studio プロジェクトを作成する** - Visual Studio で、**コンソール アプリ (.NET Framework)** プロジェクト テンプレートを使用して、新しいソリューションに Visual C# Windows Classic Desktop プロジェクトを追加します。 .NET Framework のバージョンが 4.6.1 以降であることを確認します。 プロジェクトに **CreateIdentities** という名前を付け、ソリューションに **IoTHubGetStarted** という名前を付けます。

    ![Visual Studio ソリューションを作成する][11]

2. **Azure IoT Hub .NET service SDK V1.16.0-preview-001 をインストールする** - 現在、モジュール ID とモジュール ツインはパブリック プレビュー段階です。 これらは、IoT ハブのプレリリース サービス版の SDK でのみ使えます。 Visual Studio で、[ツール] > [Nuget パッケージ マネージャー] > [ソリューションの Nuget パッケージの管理] の順に選択します。 Microsoft.Azure.Devices を検索します。 [プレリリースを含める] チェック ボックスをオンにしてください。 バージョン 1.16.0-preview-001 を選択してインストールします。 これで、モジュールのすべての機能を使用できるようになりました。 

    ![Azure IoT Hub .NET service SDK V1.16.0-preview-001 をインストールする][10]

3. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Common.Exceptions;
    ```

4. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値は、前のセクションで作成したハブの IoT Hub 接続文字列に置き換えてください。

    ```csharp
    const string connectionString = "<replace_with_iothub_connection_string>";
    const string deviceID = "myFirstDevice";
    const string moduleID = "myFirstModule";
    ```

5. 次の操作を行うコードを **Main** クラスに追加します。
    ```csharp
    static void Main(string[] args)
    {
        AddDeviceAsync().Wait();
        AddModuleAsync().Wait();
    }
    ```

6. **Program** クラスに次のメソッドを追加します。

    ```csharp
    private static async Task AddDeviceAsync()
    {
        RegistryManager registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        Device device;

        try
        {
            device = await registryManager.AddDeviceAsync(new Device(deviceID));
        }
        catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
            }

            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    AddDeviceAsync() メソッドでは、**myFirstDevice** という ID でデバイス ID が作成されます。 (そのデバイス ID が既に ID レジストリに存在する場合は、単にその既存のデバイス情報を取得します)。続けてその ID のプライマリ キーが表示されます。 シミュレーション対象デバイス アプリでこのキーを使用し、IoT Hub に接続します。

    AddModuleAsync() メソッドでは、デバイス **myFirstDevice** の下で、**myFirstModule** という ID でモジュール ID が作成されます。 (そのモジュール ID が既に ID レジストリに存在する場合は、単にその既存のモジュール情報を取得します。)続けてその ID のプライマリ キーが表示されます。 シミュレートされたモジュール アプリでこのキーを使用し、IoT ハブに接続します。

[!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

7. このアプリケーションを実行し、デバイス キーとモジュール キーを書き留めておきます。

> [!NOTE]
> IoT ハブの ID レジストリには、IoT ハブに対するセキュリティで保護されたアクセスを有効にするためのデバイス ID とモジュール ID のみが格納されます。 ID レジストリには、セキュリティ資格情報として使用されるデバイス ID とキーが格納されます。 ID レジストリには、そのデバイスのアクセスを無効にするために使用できる各デバイスの有効/無効フラグも格納されます。 その他デバイス固有のメタデータをアプリケーションで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。 モジュール ID 用の有効/無効フラグはありません。 詳細については、[IoT Hub 開発者ガイド][lnk-devguide-identity]をご覧ください。

<!-- Images. -->
[10]: ./media/iot-hub-get-started-create-module-identity-csharp/install-sdk.png
[11]: ./media/iot-hub-get-started-create-module-identity-csharp/create-identities-csharp1.JPG

<!-- Links -->
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
