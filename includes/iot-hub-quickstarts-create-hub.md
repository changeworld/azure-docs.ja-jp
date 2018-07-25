---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 63acf0297a694ff442d56e67d52fd9b4e49f812d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008588"
---
最初の手順では、Azure Portal を使用して、サブスクリプションに IoT Hub を作成します。 IoT Hub を使用すると、多くのデバイスからクラウドに大量の利用統計情報を取り込むことができます。 ハブにより、クラウド内で実行されている 1 つまたは複数のバックエンド サービスがその利用統計情報を読み取って処理することが可能になります。

1. [Azure ポータル](http://portal.azure.com)にサインインします。

1. **[リソースの作成]** > **[モノのインターネット]** > **[IoT Hub]** を選択します。

    ![IoT Hub をインストールすることを選択する][1]

1. **[IoT Hub]** ウィンドウで、IoT Hub のために以下の情報を入力します。

   * **[サブスクリプション]**: この IoT ハブを作成するために使用するサブスクリプションを選択します。
   * **[リソース グループ]**: IoT Hub を入れるリソース グループを作成するか、既存のリソース グループを使用します。 関連するすべてのリソースを 1 つのグループ (**TestResources** など) 内に配置することで、それらを一緒に管理できます。 たとえば、リソース グループを削除すると、そのグループに含まれているすべてのリソースが削除されます。 詳細については、[リソース グループを使用した Azure リソースの管理][lnk-resource-groups]に関するページを参照してください。
   * **[リージョン]**: 使用しているデバイスに最も近い場所を選択します。
   * **[名前]**: IoT ハブの一意の名前を作成します。 入力した名前が使用可能な場合は、緑色のチェック マークが表示されます。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![IoT ハブの基本ウィンドウ][2]

2. **[Next: Size and scale]\(次へ: サイズとスケール\)** を選択して、IoT ハブの作成を続けます。 

3. **[価格とスケールティア]** を選択します。 この記事では、**[F1 - Free]** レベルを選択します (サブスクリプションで引き続き使用可能な場合)。 詳細については、[価格とスケールティア][lnk-pricing]に関するページを参照してください。

   ![IoT ハブのサイズとスケールのウィンドウ][3]

4. **[Review + create]\(レビュー + 作成\)** を選択します。

1. IoT ハブの情報を確認してから、**[作成]** をクリックします。 IoT ハブの作成には数分かかることがあります。 **[通知]** ウィンドウで進行状況を監視できます。


<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md