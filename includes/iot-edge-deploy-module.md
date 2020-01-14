---
title: インクルード ファイル
description: インクルード ファイル
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/31/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: abbde0cb8137650faad563248c8fafe7dc0f469a
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564776"
---
Azure IoT Edge の主要な機能の 1 つは、クラウドからお客様の IoT Edge デバイスにコードをデプロイできることです。 **IoT Edge モジュール**は、コンテナーとして実装されている実行可能ファイルのパッケージです。 このセクションでは、[Azure Marketplace の IoT Edge モジュールのセクション](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)で事前に構築したモジュールを、Azure IoT ハブから直接デプロイします。

このセクションでデプロイするモジュールはセンサーをシミュレートし、生成されたデータを送信します。 シミュレートされたデータを開発とテストに使用できるため、このモジュールは IoT Edge の使用を開始する際にコードの一部として役に立ちます。 このモジュールで行われる内容を正確に確認したい場合は、[シミュレートされた温度センサーのソース コード](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs)をご覧いただけます。

Azure Marketplace からお客様の最初のモジュールをデプロイするには、次の手順を使用します。

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。

1. 左側のペインのメニューで、 **[デバイスの自動管理]** の下にある **[IoT Edge]** を選択します。

1. デバイスの一覧でターゲット デバイスのデバイス ID をクリックします。

1. 上部のバーで **[モジュールの設定]** を選択します。

1. ページの **[IoT Edge モジュール]** セクションで、 **[追加]** をクリックします。

1. ドロップダウン メニューから **[Marketplace モジュール]** を選択します。

   ![Azure portal で検索したシミュレートされた温度センサー](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

1. **[IoT Edge モジュールの Marketplace]** で、"シミュレートされた温度センサー" を検索し、そのモジュールを選択します。

1. SimulatedTemperatureSensor モジュールが自動的に設定されることに注目してください。 チュートリアルでは、このページを使用して他のモジュールをお客様のデプロイに追加します。 このクイック スタートでは、この 1 つのモジュールのみをデプロイします。 これは公開されているため、資格情報は必要ありません。

   ![デバイスにモジュールを設定する](./media/iot-edge-deploy-module/set-modules-on-device.png)

   **[次へ:ルート]** を選択し、ウィザードの次の手順に進みます。

1. ウィザードの **[ルート]** タブで、モジュールと IoT ハブの間でメッセージが渡される方法を定義します。 メッセージは、名前と値のペアを使用して作成されます。 このクイック スタートでは、すべてのモジュールのすべてのメッセージを IoT Hub (`$upstream`) に送信します。 自動的に設定されない場合は、**名前**の**値** `upstream` を指定するために次のコードを追加します。

   ```sql
    FROM /messages/* INTO $upstream
   ```

   **[次へ:確認と作成]** を選択し、ウィザードの次の手順に進みます。

1. ウィザードの **[確認と作成]** タブでは、IoT Edge デバイスにデプロイされているすべてのモジュールを定義する JSON ファイルをプレビューできます。 **SimulatedTemperatureSensor** モジュールに加え、**edgeAgent** と **edgeHub** という 2 つの追加システム モジュールが含まれていることに注目してください。 確認が完了したら **[作成]** を選択します。

   IoT Edge デバイスに新しいデプロイを送信しても、デバイスには何もプッシュされません。 代わりに、デバイスから IoT Hub に対して、新しい指示のクエリが定期的に実行されます。 更新されたデプロイ マニフェストがデバイスによって検出されると、新しいデプロイに関する情報が使用されてクラウドからモジュール イメージがプルされ、ローカルでのモジュールの実行が開始されます。 このプロセスは数分かかることがあります。

1. モジュールのデプロイの詳細が作成されると、ウィザードは IoT ハブの **[IoT Edge]** ページに戻ります。 IoT Edge デバイスの一覧からデバイスを選択すると、その詳細が表示されます。

1. デバイスの詳細ページで、 **[モジュール]** タブまで下にスクロールします。$edgeAgent、$edgeHub、SimulatedTemperatureSensor という 3 つのモジュールが表示されているはずです。 デプロイで指定されたとおりに表示されているのにデバイスによってレポートされていないモジュールが 1 つ以上ある場合、それらはまだお客様の IoT Edge デバイスによって起動中です。 少し待ってから、ページの上部にある **[更新]** をクリックします。

   ![デプロイ済みのモジュールの一覧に SimulatedTemperatureSensor を表示する](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
