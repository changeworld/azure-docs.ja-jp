---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b802c9dbd0cef65325cb03538b68b49c57b85bb3
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56890941"
---
**[Build]\(ビルド\)** を選択してダイアログを開きます。 次に、Xcode プロジェクトをエクスポートするフォルダーを選択します。

エクスポートが完了すると、エクスポートされた Xcode プロジェクトが含まれるフォルダーが表示されます。

### <a name="open-the-xcode-project"></a>Xcode プロジェクトを開く

エクスポートされた Xcode プロジェクトのフォルダーで、次のコマンドを実行して、プロジェクトに必要な CocoaPods をインストールします。

```bash
pod install --repo-update
```

`Unity-iPhone.xcworkspace` を開き、Xcode でプロジェクトを開くことができるようになります。

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> `library not found for -lPods-Unity-iPhone` エラーが表示される場合は、`.xcworkspace` ではなく `.xcodeproj` ファイルを開いた可能性があります。 `.xcworkspace` を開き、もう一度試してください。

ルートの **Unity-iPhone** ノードを選択してプロジェクトの設定を表示し、**[General]\(全般\)** タブを選択します。

**[Signing]\(署名\)** で、**[Automatically manage signing]\(署名を自動的に管理\)** を選択します。 表示されるダイアログで **[Enable Automatic]\(自動を有効にする\)** を選択して、ビルドの設定をリセットします。

**[Deployment Info]\(展開情報\)** で、**[Deployment Target]\(展開ターゲット\)** が `11.0` に設定されていることを確認します。

### <a name="deploy-the-app-to-your-ios-device"></a>アプリを iOS デバイスに展開する

iOS デバイスを Mac に接続し、**アクティブ スキーム**をお使いの iOS デバイスに設定します。

![デバイスを選択する](./media/spatial-anchors-unity/select-device.png)

**[Build and then run the current scheme]\(ビルドしてから現在のスキームを実行する\)** を選択します。

![展開して実行する](./media/spatial-anchors-unity/deploy-run.png)