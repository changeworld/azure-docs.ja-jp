<properties
    pageTitle="IoT Hub Gateway SDK の使用 | Microsoft Azure"
    description="Azure IoT Hub Gateway SDK を使用する場合に理解する必要のある主な概念を説明するための、Windows を使用した Azure IoT Hub Gateway SDK のチュートリアルです。"
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>



# <a name="iot-gateway-sdk-(beta)---get-started-using-windows"></a>IoT Gateway SDK (Beta) – Windows の使用

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>サンプルをビルドする方法

作業を開始する前に、Windows で SDK を使用するための[開発環境を設定][lnk-setupdevbox]する必要があります。

1. **開発者コマンド プロンプト for VS2015** コマンド プロンプトを開きます。
2. **azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに移動します。
3. **tools\\build.cmd** スクリプトを実行します。 このスクリプトでは、Visual Studio ソリューション ファイルを作成し、ソリューションをビルドして、テストを実行します。 Visual Studio ソリューションは、**azure-iot-gateway-sdk** リポジトリのローカル コピーの **build** フォルダーにあります。

## <a name="how-to-run-the-sample"></a>サンプルを実行する方法

1. **build.cmd** スクリプトにより、リポジトリのローカル コピーに**ビルド**という名前のフォルダーが作成されます。 このフォルダーには、このサンプルで使用する 2 つのモジュールが含まれています。

    このスクリプトにより、**logger_hl.dll** が **build\\modules\\logger\\Debug** フォルダーに配置され、**hello_world_hl.dll** が **build\\modules\\hello_world\\Debug** フォルダーに配置されます。 次の JSON 設定ファイルに示すように、 **モジュール パス** の値にはこれらのパスを使用します。

2. **samples\\hello_world\\src** フォルダーの **hello_world_win.json** ファイルは、サンプルの実行に使用できる Windows の JSON 設定ファイルの例です。 次に示す JSON 設定の例は、Gateway SDK リポジトリを **C:** ドライブのルートに複製していることを前提としています。 別の場所にダウンロードした場合は、ダウンロード先に応じて **samples\\hello_world\\src\\hello_world_win.json** ファイルの**モジュール パス**の値を調整する必要があります。

3. **args** セクションの **logger_hl** モジュールでは、**ファイル名**の値をログ データを格納するファイルの名前とパスに設定します。

    これは、**C:** ドライブのルートで **log.txt** ファイルへの書き込みを行う、Windows 用の JSON 設定ファイルの例です。

    ```
    {
      "modules" :
      [
        {
          "module name" : "logger_hl",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
          "args" : 
          {
            "filename":"C:\\log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\\\modules\\hello_world\\Debug\\hello_world_hl.dll",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```

3. コマンド プロンプトで、 **azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに移動します。
4. 次のコマンドを実行します。
  
  ```
  build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
  ```

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md


<!--HONumber=Oct16_HO2-->


