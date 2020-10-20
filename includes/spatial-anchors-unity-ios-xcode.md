---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/14/2020
ms.author: rgarcia
ms.openlocfilehash: 9c20726ce412e651aba0fdd202031d342350c922
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971489"
---
**[Build]\(ビルド\)** を選択します。 ウィンドウが開いたら、Xcode プロジェクトのエクスポート先にするフォルダーを選択します。

   エクスポートが完了すると、エクスポートされた Xcode プロジェクトを含むフォルダーが表示されます。

   > [!NOTE]
   > 置き換えるか追加するかを尋ねるメッセージを含むウィンドウが表示された場合、 **[Append]\(追加\)** を選択することをお勧めします。その方が時間が節約されます。 **[Replace]\(置換\)** はシーンのアセットを変更している場合にのみ選択してください。 たとえば、親子関係を追加、削除、または変更する場合や、プロパティを追加、削除、または変更する場合です。 ソース コードを変更するだけの場合は、**[Append]\(追加\)** で十分です。

## <a name="open-the-xcode-project"></a>Xcode プロジェクトを開く

これで Xcode で `Unity-iPhone.xcodeproj` プロジェクトを開くことができるようになります。 

Xcode を起動してエクスポートされた `Unity-iPhone.xcodeproj` プロジェクトを開くか、プロジェクトをエクスポートした場所から次のコマンドを実行して、Xcode でプロジェクトを起動することができます。

 ```bash
open ./Unity-iPhone.xcodeproj
```

ルートの **Unity-iPhone** ノードを選択してプロジェクトの設定を表示し、**[General]\(全般\)** タブを選択します。

**[Signing]\(署名\)** で、 **[Automatically manage signing]\(署名を自動的に管理\)** を有効にします。 有効でない場合は有効にして、表示されたウィンドウで **[Enable Automatic]\(自動を有効にする\)** を選択して、ビルドの設定をリセットします。

**[Deployment Info]\(展開情報\)** で、 **[Deployment Target]\(展開ターゲット\)** が **11.0** に設定されていることを確認します。

## <a name="deploy-the-app-to-your-ios-device"></a>アプリを iOS デバイスにデプロイする

iOS デバイスを Mac に接続し、**アクティブ スキーム**を iOS デバイスに設定します。

   ![デバイスを選択するための [My iPhone] ボタンのスクリーンショット。](./media/spatial-anchors-unity/select-device.png)

**[Build and then run the current scheme]\(ビルドしてから現在のスキームを実行する\)** を選択します。

   !["デプロイと実行" の矢印ボタンのスクリーンショット。](./media/spatial-anchors-unity/deploy-run.png)
