---
title: Azure IoT Hub を使用した制約のあるデバイス向けの開発 | Microsoft ドキュメント
description: 開発者ガイド - Azure IoT SDK を使用して制約のあるデバイス向けの開発を行う方法に関するガイダンス。
services: iot-hub
documentationcenter: c
author: yzhong94
manager: timlt
editor: ''
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2018
ms.author: yizhon
ms.openlocfilehash: 15fc5794c71428b5fb1036060af3e9c4a6890f4f
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969063"
---
# <a name="develop-for-constrained-devices-using-azure-iot-sdks"></a>Azure IoT SDK を使用した制約のあるデバイス向けの開発

## <a name="develop-using-azure-iot-hub-c-sdk"></a>Azure IoT Hub C SDK を使用した開発
Azure IoT Hub C SDK は ANSI C (C99) で記述されており、ディスクおよびメモリのフットプリントが小さいさまざまなプラットフォームを操作するのに最適です。  推奨されるメモリは 64 KB 以上ですが、正確なメモリ フットプリントは、使用するプロトコル、開かれる接続数、および対象とするプラットフォームに依存します。

C SDK は apt-get、NuGet、および MBED からパッケージ形式で入手できます。  制約のあるデバイスを対象とするには、ターゲット プラットフォームに対して SDK をローカルにビルドすることをお勧めします。 このドキュメントでは、[cmake][lnk-cmake] を使用することにより、特定の機能を削除して C SDK のフットプリントを縮小する方法を示します。  さらに、このドキュメントでは、制約のあるデバイスを操作するための最適なプログラミング モデルについても説明します。

### <a name="building-the-c-sdk-for-constrained-devices"></a>制約のあるデバイス向けの C SDK のビルド
#### <a name="prerequisites"></a>前提条件
こちらの[セットアップ ガイド][lnk-devbox-setup]に従って、C SDK をビルドするための開発環境を準備します。  cmake を使用したビルドの手順に進む前に、cmake フラグを呼び出して未使用の機能を削除することができます。

#### <a name="remove-additional-protocol-libraries"></a>余分なプロトコル ライブラリを削除する
今日、C SDK では、MQTT、MQTT over WebSocket、AMQP、AMQP over WebSocket、HTTPS という 5 つのプロトコルがサポートされています。    ほとんどのシナリオでは、クライアント上で実行されているプロトコルのうちの 1 つ、2 つを必要とします。したがって、使用しないプロトコル ライブラリは SDK から削除することができます。  シナリオに適した通信プロトコルの選択方法の詳細については、こちらの[ドキュメント][lnk-choosing-protocol]を参照してください。  たとえば、MQTT は、制約のあるデバイスに適する場合が多い軽量なプロトコルです。

AMQP ライブラリと HTTP ライブラリは、次の cmake コマンドを使用して削除することができます。
```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

#### <a name="remove-sdk-logging-capability"></a>SDK のログ機能を削除する
C SDK では、デバッグに役立つように、全体にわたって詳細なログを提供します。 運用環境のデバイス用のログ機能は、次の cmake コマンドを使用して削除することができます。
```
cmake -Dno_logging=OFF <Path_to_cmake>
```

#### <a name="remove-upload-to-blob-capability"></a>BLOB へのアップロード機能を削除する
SDK の組み込み機能を使用することにより、大きなファイルを Azure Storage にアップロードすることができます。  Azure IoT Hub は、関連付けられた Azure Storage アカウントへのディスパッチャーとして機能します。  この機能を使用することで、メディア ファイル、大きなテレメトリ バッチ、およびログを送信することができます。  IoT Hub でのファイルのアップロードの詳細については、こちらの[ドキュメント][lnk-hub-file-upload]を参照してください。  この機能は、アプリケーションで必要ない場合、次の cmake コマンドを使用して削除することができます。
```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```
#### <a name="running-strip-on-linux-environment"></a>Linux 環境でのストリップの実行
バイナリが Linux システム上で実行されている場合は、[strip コマンド][ lnk-strip]を利用して、コンパイル後の最終的なアプリケーションのサイズを縮小することができます。
```
strip -s <Path_to_executable>
```

### <a name="programming-models-for-constrained-devices"></a>制約のあるデバイス用のプログラミング モデル
#### <a name="avoid-using-the-serializer"></a>シリアライザーの使用を回避する
C SDK にはオプションとして[シリアライザー][lnk-serializer]があります。このオプションでは、宣言型マッピング テーブルを使用して、メソッドとデバイス ツイン プロパティを定義することができます。  シリアライザーは開発の簡略化を目的として設計されていますが、オーバーヘッドが追加されるため、制約のあるデバイスに最適ではありません。  この場合は、プリミティブ クライアント API の使用を検討し、さらに [parson][lnk-parson] などの軽量のパーサーを使用して json を解析します。

#### <a name="use-the-lower-layer-ll"></a>下位のレイヤーを使用する (_LL_)
C SDK では 2 つのプログラミング モデルがサポートされています。  一方のセットには、下位レイヤーを意味する挿入辞 _LL_ を伴った API が含まれています。  この API セットは軽量であり、ワーカー スレッドを起動しません。このことは、ユーザーがスケジュール設定を手動で制御する必要があることを意味します。  たとえば、デバイス クライアント用の _LL_ API はこちらの[ヘッダー ファイル](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h)で確認することができます。  _LL_ インデックスを伴っていない、もう一方の API セットは便利なレイヤーと呼ばれています。この場合、ワーカー スレッドは自動的に起動します。  たとえば、デバイス クライアント用の便利なレイヤーの API はこちらの[ヘッダー ファイル](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h)で確認できます。  余分な各スレッドによってシステム リソースの大部分が占有される可能性がある制約付きのデバイスの場合は、_LL_ API の使用を検討してください。

## <a name="next-steps"></a>次の手順
Azure IoT C SDK アーキテクチャの詳細について説明します。
-   [Azure IoT C SDK ソース コード](https://github.com/Azure/azure-iot-sdk-c/)
-   [Azure IoT device SDK for C の概要](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-sdk-c-intro)

------
[lnk-cmake]: https://cmake.org/
[lnk-devbox-setup]:  https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-choosing-protocol]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols
[lnk-hub-file-upload]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-file-upload
[lnk-strip]: https://en.wikipedia.org/wiki/Strip_(Unix)
[lnk-serializer]: https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer
[lnk-parson]: https://github.com/kgabis/parson