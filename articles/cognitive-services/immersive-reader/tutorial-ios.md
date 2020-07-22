---
title: チュートリアル:Swift iOS コード サンプルを使用してイマーシブ リーダーを起動する
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、イマーシブ リーダーを起動するサンプル Swift アプリケーションを構成して実行します。
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: a9259026747102dd65be3bb8da853735098667db
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049334"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-swift-ios-code-sample"></a>チュートリアル:Swift iOS コード サンプルを使用してイマーシブ リーダーを起動する

[概要](./overview.md)に関するページでは、イマーシブ リーダーの機能とそのしくみ (どのようにして言語学習者、新しい読者、および学習方法の異なる学生が読解力向上のために実証済みの手法を実装するか) について説明しました。 このチュートリアルでは、イマーシブ リーダーを起動する iOS アプリケーションの作成方法について説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * サンプル プロジェクトを使用して iOS 用の Swift アプリを構成して実行する
> * アクセス トークンの取得
> * イマーシブ リーダーを起動してサンプル コンテンツを表示する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* Azure Active Directory 認証用に構成されたイマーシブ リーダー リソース。 設定するには、[これらの手順](./how-to-create-immersive-reader.md)に従ってください。 環境のプロパティを構成するときに、ここで作成した値の一部が必要になります。 後で参照するために、実際のセッションの出力をテキスト ファイルに保存します。
* [macOS](https://www.apple.com/macos)
* [Git](https://git-scm.com/)
* [イマーシブ リーダー SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)

## <a name="configure-authentication-credentials"></a>認証資格情報の構成

1. Xcode を開いて、**immersive-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj** を開きます。
2. 上部のメニューで、 **[製品]、[スキーム]、[スキームの編集]** の順にクリックします。
3. **[実行]** ビューで、 **[引数]** タブをクリックします。
4. **[環境変数]** セクションで、以下の名前と変数を追加し、イマーシブ リーダー リソースを作成したときに取得した値を指定します。

```text
TENANT_ID=<YOUR_TENANT_ID>
CLIENT_ID=<YOUR_CLIENT_ID>
CLIENT_SECRET<YOUR_CLIENT_SECRET>
SUBDOMAIN=<YOUR_SUBDOMAIN>
```

上記の変更には、公開すべきでない機密情報が含まれているため、ソース管理にはコミットしないでください。

## <a name="launch-the-immersive-reader-with-sample-content"></a>イマーシブ リーダーを起動してサンプル コンテンツを表示する

1. Xcode で、**Ctrl + R** キーを押してプロジェクトを実行します。

## <a name="next-steps"></a>次のステップ

* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) と [Immersive Reader SDK リファレンス](./reference.md)を探索する
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) でコード サンプルを見る
