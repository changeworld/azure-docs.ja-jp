---
title: インクルード ファイル
description: インクルード ファイル
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c79b6f854dc78670a7eb8a1275c3e2fc46fcdd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "67181610"
---
### <a name="code-walkthrough"></a>コードのチュートリアル

このセクションでは、サンプル コードのいくつかの重要な部分について説明し、これらがどのようにリモート監視ソリューション アクセラレータに関連しているかについて説明します。

次のスニペットは、デバイスの機能を記述するレポートされたプロパティがどのように定義されているかを示しています。 これには次のようなプロパティがあります。

- ソリューション アクセラレータがデバイスをマップに追加できるようにするためのデバイスの場所。
- 現在のファームウェア バージョン。
- デバイスでサポートされているメソッドの一覧。
- デバイスによって送信されるテレメトリ メッセージのスキーマ。

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

このサンプルには、Parson ライブラリを使用してこのデータ構造をシリアル化する **serializeToJson** 関数が含まれています。

このサンプルには、クライアントがソリューション アクセラレータと対話するときに情報をコンソールに出力するいくつかのコールバック関数が含まれています。

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

次のスニペットは、**device_method_callback** 関数を示しています。 この関数は、ソリューション アクセラレータからメソッド呼び出しを受け取ったときに実行するアクションを決定します。 この関数は、**userContextCallback** パラメーター内の **Chiller** データ構造への参照を受け取ります。 **userContextCallback** の値は、**main** 関数でコールバック関数が構成されるときに設定されます。

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

このサンプルは、ソリューション アクセラレータがファームウェア更新メソッドを呼び出したときに JSON ペイロードを逆シリアル化し、バックグラウンド スレッドを開始して更新プロセスを完了します。 次のスニペットは、スレッドで実行される **do_firmware_update** を示しています。

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

次のスニペットは、クライアントがテレメトリ メッセージをソリューション アクセラレータに送信する方法を示しています。 メッセージ プロパティには、ソリューション アクセラレータがテレメトリをダッシュボードに表示するのに役立つメッセージ スキーマが含まれています。

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

サンプルの **main** 関数:

- SDK サブシステムを初期化およびシャットダウンします。
- **Chiller** データ構造を初期化します。
- レポートされたプロパティをソリューション アクセラレータに送信します。
- デバイス メソッド コールバック関数を構成します。
- シミュレートされたテレメトリ値をソリューション アクセラレータに送信します。

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
