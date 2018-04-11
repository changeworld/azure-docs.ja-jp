> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-how-to-configure.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-how-to-configure.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>はじめに

[IoT Hub デバイス ツインの概要][lnk-twin-tutorial]に関するページでは、"*タグ*" を使用してデバイス メタデータを設定する方法について学習しました。 "*報告されるプロパティ*" を使用してデバイス アプリからデバイスの状態を受信し、SQL に似た言語を使用してこの情報のクエリを実行しました。

このチュートリアルでは、デバイス ツインの "*必要なプロパティ*" と "*報告されるプロパティ*" を使用して、デバイス アプリをリモートで構成する方法について説明します。 デバイス ツインの報告されるプロパティと必要なプロパティを使用すると、デバイス アプリケーションを複数の手順で構成したり、すべてのデバイスにおけるこの操作の状態を表示したりできます。 デバイス構成の役割の詳細については、[IoT Hub を使用したデバイス管理の概要][lnk-dm-overview]に関するページを参照してください。

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

大まかには、デバイス ツインを使用すると、ソリューション バックエンドは、特定のコマンドを送信する代わりに、管理対象デバイスに必要な構成を指定できます。 デバイスがその構成を更新する最善の方法を設定します (特定のデバイスの状態が、特定のコマンドを直ちに実行する能力に影響するような IoT シナリオでは重要です)。同時に、更新プロセスの現在の状態と潜在的なエラー状態を継続的に報告します。 このパターンは、多数のデバイスの管理に役立ちます。これは、すべてのデバイスにおける構成プロセスの状態を、ソリューション バックエンドで完全に表示できるためです。

> [!TIP]
> より対話的な方法でデバイスを制御するシナリオ (例: ユーザー制御アプリからファンをオンにする) では、[ダイレクト メソッド][lnk-methods]の使用を検討してください。

このチュートリアルでは、デバイス アプリが構成の更新を適用できるように、ソリューション バックエンドによって対象のデバイスのテレメトリ構成が変更されます。 たとえば、構成を更新するとソフトウェア モジュールを再起動する必要が生じる可能性があります。これを、このチュートリアルでは単純な遅延としてシミュレートします。

ソリューション バックエンドでは、次の方法でデバイス ツインの必要なプロパティに構成が格納されます。

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

構成オブジェクトは複雑になる可能性があるため、構成には一意の ID (ハッシュまたは [GUID][lnk-guid]) が割り当てられます。


デバイス アプリは、必要なプロパティ **telemetryConfig** を報告されるプロパティに反映して、現在の構成を報告します。

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

報告される **telemetryConfig** に追加のプロパティ **status** があることに注意してください。このプロパティは、構成の更新プロセスの状態を報告するために使用されます。

望ましい構成が新たに届くと、デバイス アプリは状態を変更することで保留中の構成を報告します。

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "configId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

しばらくしてから、デバイス アプリは、プロパティを更新することでこの操作の失敗または成功を報告します。 すべてのデバイスにおける構成プロセスの状態は、ソリューション バックエンドでいつでも照会できます。

このチュートリアルでは、次の操作方法について説明します。

* ソリューション バックエンドから構成の更新を受け取り、構成の更新プロセスで複数の更新を "*報告されるプロパティ*" として報告する、シミュレーション対象デバイス アプリを作成します。
* デバイスの望ましい構成を更新し、構成の更新プロセスを照会するバックエンド アプリを作成します。

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
