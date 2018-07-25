---
title: インクルード ファイル
description: インクルード ファイル
services: iot-edge
author: wesmc7777
ms.service: iot-edge
ms.topic: include
ms.date: 06/26/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 657006360105ac26091c54fe2e0deb523d1e6dea
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008503"
---
次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。

それ以外の場合は、課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除してもかまいません。 

> [!IMPORTANT]
> Azure のリソースとリソース グループは、削除すると元に戻すことができません。 これらの項目を削除すると、リソース グループとそこに含まれるすべてのリソースが完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 IoT Hub を、保持したいリソースが含まれている既存のリソース グループ内に作成した場合は、リソース グループを削除するのではなく、IoT Hub リソースだけを削除してください。
>

IoT Hub だけを削除するには、次のコマンドを実行してください。 \<YourIoTHub> は実際の IoT Hub の名前に、\<TestResources> は実際のリソース グループの名前に置き換えます。

```azurecli-interactive
az iot hub delete --name <YourIoTHub> --resource-group <TestResources>
```


名前でリソース グループ全体を削除するには、以下の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインし、**[リソース グループ]** を選択します。

2. **[名前でフィルター処理してください]** ボックスに、IoT Hub が含まれているリソース グループの名前を入力します。 

3. 結果一覧で、目的のリソース グループの右側にある省略記号 (**[...]**) を選択し、**[リソース グループの削除]** を選択します。

    ![Delete resource group](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)

4. リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにリソース グループの名前を再入力し、**[削除]** を選択します。 しばらくすると、リソース グループとそこに含まれているすべてのリソースが削除されます。






