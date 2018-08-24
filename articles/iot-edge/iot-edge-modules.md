---
title: Azure IoT Edge モジュールについて | Microsoft Docs
description: Azure IoT Edge モジュールと、その構成方法について説明します
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 02/15/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9064e0da6dde6c4b30235adf771f06a4f25d709a
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2018
ms.locfileid: "42140111"
---
# <a name="understand-azure-iot-edge-modules"></a>Azure IoT Edge モジュールについて

Azure IoT Edge では、ビジネス ロジックを*モジュール*形式でエッジに展開および管理できます。 Azure IoT Edge モジュールは、IoT Edge によって管理される計算の最小単位であり、Azure Stream Analytics などの Azure サービスまたは独自ソリューション固有のコードを含めることができます。 モジュールを開発、展開、および管理する方法を理解するには、モジュールを構成する次の 4 つの概念を考えると理解が深まります。

* **モジュール イメージ**。モジュールを定義するソフトウェアを含むパッケージです。
* **モジュール インスタンス**。IoT Edge デバイスでモジュール イメージを実行している計算の特定の単位です。 モジュール インスタンスは、IoT Edge ランタイムによって開始されます。
* **モジュール ID**。IoT Hub に格納されている情報 (セキュリティ資格情報を含む) の一部で、各モジュール インスタンスに関連付けられています。
* **モジュール ツイン**。IoT Hub に格納されている JSON ドキュメントで、メタデータ、構成、条件などのモジュール インスタンスの状態情報が含まれています。 

## <a name="module-images-and-instances"></a>モジュール イメージとインスタンス

IoT Edge モジュール イメージには、IoT Edge ランタイムの管理、セキュリティ、および通信機能を活用するアプリケーションが含まれています。 独自のモジュール イメージを開発したり、Azure Stream Analytics などのサポートされている Azure サービスからモジュール イメージをエクスポートしたりすることもできます。
モジュール イメージはクラウドに存在し、さまざまなソリューションで更新、変更、展開できます。 たとえば、生産ラインの出力を予測する機械学習を使用するモジュールは、コンピューター ビジョンを使用してドローンを制御するモジュールとは別のイメージとして存在します。 

モジュール イメージがデバイスに展開され、IoT Edge ランタイムによって開始されるたびに、該当モジュールの新しいインスタンスが作成されます。 世界のクラウドの異なる部分にある 2 つのデバイスが同じモジュール イメージを使用することができますが、モジュールがデバイスで開始されたときに、各デバイスは独自のモジュール インスタンスを持つことになります。 

![クラウド内のモジュール イメージ - デバイス上のモジュール インスタンス][1]

実装では、モジュール イメージはリポジトリ内のコンテナー イメージとして存在し、モジュール インスタンスはデバイス上のコンテナーです。 

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>モジュール ID

新しいモジュール インスタンスが IoT Edge ランタイムによって作成されると、作成されたインスタンスには対応するモジュール ID が関連付けられます。 モジュール ID は IoT Hub に格納され、その特定のモジュール インスタンスのすべてのローカルおよびクラウド通信のアドレス指定とセキュリティ スコープとして採用されます。
モジュール インスタンスに関連付けられる ID は、インスタンスが実行されているデバイスの ID と、ソリューション内のモジュールに付けた名前によって決まります。 たとえば、Azure Stream Analytics を使用するモジュール `insight` を呼び出し、`Hannover01` というデバイスに展開した場合、IoT Edge ランタイムは `/devices/Hannover01/modules/insight` という対応するモジュール ID を作成します。

明らかに、1 つのモジュール イメージを同じデバイスに複数回展開する必要があるシナリオで、同じイメージを異なる名前で複数回展開することができます。

![モジュールの ID は一意][2]

## <a name="module-twins"></a>モジュール ツイン

各モジュール インスタンスは、モジュール インスタンスの構成に使用できる、対応するモジュール ツインも持ちます。 インスタンスとツインは、モジュール ID によってお互いに関連付けられます。 

モジュール ツインは、モジュールの情報と構成プロパティを格納する JSON ドキュメントです。 この概念は、IoT Hub の[デバイス ツイン][lnk-device-twin]の概念に似ています。 モジュール ツインの構造は、デバイス ツインとまったく同じです。 両方の種類のツインと対話するために使用する API も同じです。 2 つの唯一の違いは、クライアント SDK をインスタンス化するために使用する ID です。 

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings); 
await client.OpenAsync(); 
 
// Get the module twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="offline-capabilities"></a>オフライン機能

Azure IoT Edge は、IoT Edge デバイス上でのオフライン操作をサポートしています。 現在、これらの機能は限定されており、追加シナリオの開発が進行中です。 

IoT Edge モジュールは、次の要件を満たしている場合、既定の時間よりも長くオフライン状態を継続できます。 

* **メッセージ Time to Live (TTL) が有効期限切れになっていない**。 メッセージ TTL の既定値は 2 時間ですが、IoT Edge ハブのストア アンド フォワード構成で増減することができます。 
* **オフライン時にモジュールを IoT Edge ハブに対して再認証する必要がない**。 モジュールは、IoT ハブとのアクティブな接続がある Edge ハブに対してのみ認証できます。 何らかの理由で再起動された場合には、モジュールを再認証する必要があります。 SAS トークンの有効期限が切れた後でも、モジュールから Edge ハブにメッセージを送信することはできます。 接続が再開されると、Edge ハブはモジュールからの新しいトークンを要求し、それを IoT ハブに対して検証します。 成功した場合、Edge ハブは保存されているモジュール メッセージを転送します (モジュールのトークンが期限切れになっている間に送信されたメッセージであっても)。 
* **オフライン時にメッセージを送信したモジュールが、接続の再開時にまだ機能している**。 Edge ハブでは、IoT ハブに再接続する際、モジュール メッセージを転送する前に、新しいモジュール トークンを検証する必要があります (以前のトークンの有効期限が切れている場合)。 モジュールが新しいトークンを提供できない場合、Edge ハブはモジュールの保存済みメッセージに対してアクションを実行することができません。 
* **Edge ハブに、メッセージを保存できるだけのディスク容量がある**。 既定では、メッセージは Edge ハブ コンテナーのファイルシステムに保存されます。 なお、メッセージを保存するためのマウント済みボリュームを指定する構成オプションもあります。 いずれの場合も、IoT ハブへの遅延配信を行うには、メッセージを保存できるだけの容量が必要になります。  

## <a name="next-steps"></a>次の手順
 - [Understand the Azure IoT Edge runtime and its architecture (Azure IoT Edge ランタイムとそのアーキテクチャについて)][lnk-runtime]

<!-- Images -->
[1]: ./media/iot-edge-modules/image_instance.png
[2]: ./media/iot-edge-modules/identity.png

<!-- Links -->
[lnk-device-identity]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-runtime]: iot-edge-runtime.md
