---
title: Microsoft OPC Publisher の構成
description: このチュートリアルでは、スタンドアロン モードで OPC Publisher を構成する方法について説明します。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4d4f9c90fd96365216480164f29f08fad92eb9d0
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787344"
---
# <a name="tutorial-configure-the-opc-publisher"></a>チュートリアル: OPC Publisher の構成

このチュートリアルでは、OPC Publisher の構成について説明します。 これを構成するには、いくつかのインターフェイスを使用できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 構成ファイルを使用して OPC Publisher を構成する
> * コマンドライン引数を使用して OPC Publisher を構成する
> * IoT Hub のダイレクト メソッドを使用して OPC Publisher を構成する
> * クラウドベースのコンパニオン REST マイクロサービスを使用して OPC Publisher を構成する

## <a name="configuring-security"></a>セキュリティの構成

IoT Edge では、自動的に IoT Hub にアクセスするためのセキュリティ構成を OPC Publisher に提供しています。 また、OPC Publisher は、`dc` コマンドライン パラメーターを使用して IoT Hub にアクセスするためのデバイスの接続文字列を指定することで、スタンドアロンの Docker コンテナーとしても実行できます。 IoT Hub 用のデバイスを作成し、その接続文字列を Azure portal で取得できます。

OPC UA 対応の資産にアクセスする場合は、X.509 証明書とそれに関連付けられた秘密キーが OPC UA によって使用されます。 これは OPC UA アプリケーション認証と呼ばれ、OPC UA ユーザー認証に追加されます。 OPC Publisher では、すべてのアプリケーション証明書を管理するためにファイル システムベースの証明書ストアが使用されています。 OPC Publisher は、起動時に、使用可能な証明書がこの証明書ストアにあるかどうかを確認し、ない場合に、新しい自己署名証明書と関連付けられた新しい秘密キーを作成します。 自己署名証明書は、信頼された証明機関によって署名されていないため、提供される認証は脆弱ですが、少なくとも、OPC UA 対応の資産への通信はこの方法で暗号化できます。

セキュリティは、構成ファイルで `"UseSecurity": true,` フラグを使用して有効にします。 OPC Publisher の接続先となる OPC UA サーバーで使用できる最も安全なエンドポイントが自動的に選択されます。
既定では、OPC Publisher は (上記のアプリケーション認証に加えて) 匿名ユーザー認証を使用します。 ただし、OPC Publisher では、ユーザー名とパスワードを使用したユーザー認証もサポートされています。 これは、REST API 構成インターフェイス (後で説明) または次のような構成ファイルを使用して指定できます。
```
"OpcAuthenticationMode": "UsernamePassword",
"OpcAuthenticationUsername": "usr",
"OpcAuthenticationPassword": "pwd",
```
さらに、OPC Publisher バージョン 2.5 以前では、構成ファイル内のユーザー名とパスワードが暗号化されます。 バージョン 2.6 以降では、プレーンテキストのユーザー名とパスワードのみがサポートされます。 これは、OPC Publisher の次のバージョンで改善される予定です。

再起動後も OPC Publisher のセキュリティ構成を維持するには、証明書ストア ディレクトリにある証明書と秘密キーを IoT Edge ホスト OS ファイルシステムにマップする必要があります。 前述の「Azure portal でコンテナーの作成オプションを指定する」を参照してください。

## <a name="configuration-via-configuration-file"></a>構成ファイルを使用した構成

OPC Publisher を構成する最も簡単な方法は、構成ファイルの使用です。 構成ファイルの例とその形式に関するドキュメントは、このリポジトリの [`publishednodes.json`](https://raw.githubusercontent.com/Azure/iot-edge-opc-publisher/master/opcpublisher/publishednodes.json) ファイルで提供されています。
構成ファイルの構文は時間の経過と共に変更されています。OPC Publisher は、引き続き古い形式を読み取ることはできますが、構成を保持する際に最新の形式に変換します。これは、定期的に自動で行われます。

基本的な構成ファイルは次のようになります。
```
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

OPC UA 資産は、データの変更時にそのデータの変更を OPC Publisher に送信するだけで、ネットワークの帯域幅を最適化します。 データの変更をより頻繁に、または定期的に発行する必要がある場合、OPC Publisher では、構成されるデータ項目ごとに "ハートビート" をサポートします。これは、データ項目の構成で HeartbeatInterval キーを追加指定することで有効にすることができます。 この間隔は秒単位で指定します。
```
 "HeartbeatInterval": 3600,
```

OPC UA 資産は常に、OPC Publisher が初めてデータ項目にアクセスしたときにその現在の値を送信します。 このデータが IoT Hub に発行されないようにするには、データ項目の構成で SkipFirst キーを追加指定できます。
```
 "SkipFirst": true,
```

どちらの設定も、コマンドライン オプションを使用してグローバルに有効にすることができます。

## <a name="configuration-via-command-line-arguments"></a>コマンドライン引数を使用した構成

OPC Publisher のグローバル設定に使用できるコマンドライン引数がいくつかあります。 これらについては、[こちら](reference-command-line-arguments.md)で説明しています。


## <a name="configuration-via-the-built-in-opc-ua-server-interface"></a>組み込みの OPC UA サーバー インターフェイスを使用した構成

>[!NOTE] 
> この機能は、OPC Publisher のバージョン 2.5 以前でのみ使用できます。**

OPC Publisher には、ポート 62222 で実行される、組み込みの OPC UA サーバーがあります。 これには、次の 3 つの OPC UA メソッドが実装されています。

  - PublishNode
  - UnpublishNode
  - GetPublishedNodes

このインターフェイスには、OPC UA クライアント アプリケーション (たとえば、[UA Expert](https://www.unified-automation.com/products/development-tools/uaexpert.html)) を使用してアクセスできます。

## <a name="configuration-via-iot-hub-direct-methods"></a>IoT Hub のダイレクト メソッドを使用した構成

>[!NOTE] 
> この機能は、OPC Publisher のバージョン 2.5 以前でのみ使用できます。**

OPC Publisher には、次の [IoT Hub のダイレクトメソッド](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-direct-methods)が実装されています。これらは、[IoT Hub Device SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) を使用して、(世界中のどこからでも) アプリケーションから呼び出すことができます。

  - PublishNodes
  - UnpublishNodes
  - UnpublishAllNodes
  - GetConfiguredEndpoints
  - GetConfiguredNodesOnEndpoint
  - GetDiagnosticInfo
  - GetDiagnosticLog
  - GetDiagnosticStartupLog
  - ExitApplication
  - GetInfo

このインターフェイスを利用して、[サンプルの構成アプリケーション](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration)と、OPC Publisher のオープンソースから[診断情報を読み取るためのアプリケーション](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics)が提供されています。

## <a name="configuration-via-cloud-based-companion-rest-microservice"></a>クラウドベースのコンパニオン REST マイクロサービスを使用した構成

>[!NOTE] 
> この機能は、OPC Publisher のバージョン 2.6 以降でのみ使用できます。

REST インターフェイスを備えた、クラウドベースのコンパニオン マイクロサービスは、[こちら](https://github.com/Azure/Industrial-IoT/blob/master/docs/services/publisher.md)で説明されており、入手可能です。 これは、OpenAPI と互換性のあるインターフェイス (Swagger など) で OPC Publisher を構成するために使用できます。

## <a name="configuration-of-the-simple-json-telemetry-format-via-separate-configuration-file"></a>個別の構成ファイルを使用したシンプルな JSON テレメトリ形式の構成

>[!NOTE] 
> この機能は、OPC Publisher のバージョン 2.5 以前でのみ使用できます。

OPC Publisher では、標準化されていないシンプルなテレメトリ形式の要素を、tc コマンド ライン オプションで指定できる個別の構成ファイルを使用してフィルター処理できます。 構成ファイルが指定されない場合は、完全な JSON テレメトリ形式が IoT Hub に送信されます。 個別のテレメトリ構成ファイルの形式については、[こちら](reference-opc-publisher-telemetry-format.md#opc-publisher-telemetry-configuration-file-format)で説明しています。

## <a name="next-steps"></a>次のステップ
OPC Publisher の構成が完了したので、次の手順では、Edge モジュールのパフォーマンスとメモリを調整する方法について説明します。

> [!div class="nextstepaction"]
> [パフォーマンスとメモリの調整](tutorial-publisher-performance-memory-tuning-opc-publisher.md)