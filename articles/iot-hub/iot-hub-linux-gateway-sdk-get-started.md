<properties
	pageTitle="IoT Hub Gateway SDK の使用 | Microsoft Azure"
	description="Azure IoT Hub Gateway SDK を使用する場合に理解する必要のある主な概念を説明するための、Linux を使用した Azure IoT Hub Gateway SDK のチュートリアルです。"
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# IoT Gateway SDK (Beta) – Linux の使用

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## サンプルをビルドする方法

作業を開始する前に、Linux で SDK を使用するための[開発環境を設定][lnk-setupdevbox]する必要があります。

1. シェルを開きます。
2. **azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに移動します。
3. **tools\\build.sh** スクリプトを実行します。このスクリプトでは、**cmake** ユーティリティを使用して、**azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに **ビルド** という名前のフォルダーを作成し、メイクファイルを生成します。スクリプトは、次にソリューションをビルドし、テストを実行します。

> [AZURE.NOTE]  **build.sh** スクリプトを実行するたびに **ビルド** フォルダーが削除され、**azure-iot-gateway-sdk** リポジトリのローカル コピーのルート フォルダーに再作成されます。

## サンプルを実行する方法

1. **build.sh** スクリプトは、**azure-iot-gateway-sdk** リポジトリのローカル コピーの **ビルド** フォルダーに出力を生成します。ここには、このサンプルで使用する 2 つのモジュールが含まれています。

    この ビルドスクリプトにより、**liblogger\_hl.so** が **build/modules/logger/** フォルダーに配置され、**libhello\_world\_hl.so** が **build/modules/hello\_world/** フォルダーに配置されます。次の JSON 設定ファイルに示すように、**モジュール パス** の値にはこれらのパスを使用します。

2. **samples/hello\_world/src** フォルダーの **hello\_world\_lin.json** ファイルは、サンプルの実行に使用できる Linux の JSON 設定ファイルの例です。次に示す JSON 設定の例は、**azure-iot-gateway-sdk** リポジトリのローカル コピーのルートからサンプルを実行することを前提としています。

3. **args** セクションの **logger\_hl** モジュールでは、**ファイル名** の値をログ データを格納するファイルの名前とパスに設定します。

    これは、サンプルを実行するフォルダーに **log.txt** を書き込む Linux の JSON 設定ファイルの例です。

    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "module path" : "./build/modules/logger/liblogger_hl.so",
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "./build/modules/hello_world/libhello_world_hl.so",
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

3. シェル内で **azure-iot-gateway-sdk** フォルダーに移動します。
4. 次のコマンドを実行します。
  
  ```
  ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
  ``` 

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md

<!---HONumber=AcomDC_0928_2016-->