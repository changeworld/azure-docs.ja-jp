---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 662aced6df27febdf29f2645725962763e89cfa2
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752726"
---
Unity を開き、`Unity` フォルダーのプロジェクトを開きます。

**[ファイル]** -> **[ビルド設定]** を選択して、**[ビルド設定]** を開きます。

**[プラットフォーム]** セクションで、**[Android]** を選択します。 次に、**[ビルド システム]** を **[Gradle]** に変更し、**[プロジェクトのエクスポート]** オプションをオンにします。

**[Switch Platform]\(プラットフォームの切り替え\)** を選択して、プラットフォームを **[Android]** に変更します。 Android をサポートするコンポーネントが不足している場合は、Unity によってそれらをインストールすることを要求される場合があります。

![Unity のビルド設定](./media/spatial-anchors-unity/unity-android-build-settings.png)

**[ビルド設定]** ウィンドウを閉じます。

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Unity 用 ARCore SDK をダウンロードしてインポートする

[Unity 用 ARCore SDK のリリース](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0)から `unitypackage` ファイルをダウンロードします。 Unity プロジェクトに戻り、**[Assets]\(資産\)** -> **[パッケージのインポート]** -> **[カスタム パッケージ...]** を選択した後、先ほどダウンロードした `unitypackage` ファイルを選択します。 **[Unity パッケージのインポート]** ダイアログ で、すべてのファイルが選択されていることを確認し、**[インポート]** を選択します。
