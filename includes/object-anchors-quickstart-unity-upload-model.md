---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 08/02/2021
ms.author: crtreasu
ms.openlocfilehash: ce942908ffa068b0502bdac7f9472543355dbbce
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254202"
---
## <a name="upload-your-model"></a>モデルをアップロードする

アプリを実行する前に、モデルをアプリで使用できるようにする必要があります。
Object Anchors モデルをまだお持ちでない場合は、[モデルの作成](../articles/object-anchors/quickstarts/get-started-model-conversion.md)の手順に従ってモデルを作成します。 その後、ここに戻ります。

HoloLens をオンにして開発デバイス (PC) に接続したら、次の手順に従って、HoloLens の **3D Objects** フォルダーにモデルをアップロードします。

1. Ctrl キーと C キー (Ctrl + C) を押しながら、操作するモデルを選択してコピーします。

2. Windows ロゴ キーと E キー (Win + E) を押して、エクスプローラーを起動します。 HoloLens が他のドライブやフォルダーと共に、左側のウィンドウに表示されます。

    :::image type="content" source="./media/object-anchors-quickstarts-unity/file-explorer-launch.png" alt-text="エクスプローラー":::

3. HoloLens のリンクをタップすると、HoloLens デバイスのストレージが右側のペインに表示されます。

    :::image type="content" source="./media/object-anchors-quickstarts-unity/file-explorer-internal-storage.png" alt-text="HoloLens の内部ストレージを開く":::

4. エクスプローラーで、 **[Internal Storage] > [3D Objects]** にアクセスします。 これで、Ctrl キーを押しながら V キー (Ctrl + V) を押すことで、**3D Objects** フォルダーにモデルを貼り付けることができます。

    :::image type="content" source="./media/object-anchors-quickstarts-unity/file-explorer-open-3d-objects.png" alt-text="3D Objects フォルダーにモデルを貼り付ける":::