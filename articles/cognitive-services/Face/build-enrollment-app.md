---
title: ユーザーを Face サービスに追加するための React アプリを作成する
titleSuffix: Azure Cognitive Services
description: 開発環境を設定し、顧客からの同意を得るための Face アプリをデプロイする方法について説明します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: db6ee292a088d530ea858d75e6649684e03d92b6
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2021
ms.locfileid: "123540480"
---
# <a name="build-a-react-app-to-add-users-to-a-face-service"></a>ユーザーを Face サービスに追加するための React アプリを作成する

このガイドでは、サンプルの顔登録アプリケーションの使用を始める方法について説明します。 このアプリでは、ユーザーを顔認識サービスに追加し、精度の高い顔データを取得することを目的として、意味のある同意を得るためのベスト プラクティスを示します。 統合されたシステムでは、このようなアプリを使用して、顔データに基づく、タッチレス アクセス制御、ID 検証、出社追跡、または個人用設定キオスクを提供できます。

アプリケーションを起動すると、ユーザーに詳細な同意画面が表示されます。 ユーザーが同意すると、アプリによってユーザー名とパスワードを入力するよう要求され、デバイスのカメラを使用して高品質の顔の画像がキャプチャされます。

サンプル アプリは、JavaScript と React Native フレームワークを使用して記述されています。 現在は、Android および iOS デバイスに展開できます。今後、展開オプションをさらに増やすことが予定されています。

## <a name="prerequisites"></a>必須コンポーネント 

* Azure サブスクリプション。[無料で作成できます](https://azure.microsoft.com/free/cognitive-services/)。  
* Azure サブスクリプションを入手したら、Azure portal で [Face リソースを作成](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace)し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** を選択します。  
  * アプリケーションを Face API に接続するには、作成したリソースのキーとエンドポイントが必要です。  
  * ローカルの開発とテストの場合に限り、API キーとエンドポイントが環境変数になります。 最終的なデプロイでは、API キーを安全な場所に格納し、コードまたは環境変数には決して格納しないでください。  

### <a name="important-security-considerations"></a>セキュリティに関する重要な考慮事項
* ローカル開発および最初の制限付きテストでは、API キーとエンドポイントを保持するために環境変数を使用することは可能です (ただし、ベスト プラクティスではありません)。 パイロットおよび最終的なデプロイでは、API キーを安全に保存する必要があります。これには、通常、中間サービスを使用して、ログイン時に生成されたユーザー トークンを検証することが含まれます。 
* API キーまたはエンドポイントをコードに格納することも、それらをバージョン管理システム (Git など) にコミットすることも行わないでください。 それが誤って行われた場合は、すぐに新しい API キーまたはエンドポイントを生成し、以前のものを取り消す必要があります。
* ベスト プラクティスとして、開発および運用のために個別の API キーを用意することを検討してください。

## <a name="set-up-the-development-environment"></a>開発環境を設定する

#### <a name="android"></a>[Android](#tab/android)
 
1. [サンプル アプリ](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample)の Git リポジトリをクローンします。
1. 開発環境をセットアップするには、<a href="https://reactnative.dev/docs/environment-setup"  title="React Native のドキュメント"  target="_blank">React Native のドキュメント<span class="docon docon-navigate-external x-hidden-focus"></span></a>に従います。 **[React Native CLI Quickstart]\(React Native CLI クイックスタート\)** を選択します。 開発 OS と **Android**  をターゲット OS として選択します。 **依存関係のインストール** と **Android 開発環境** に関するセクションを完了します。
1. [Visual Studio Code](https://code.visualstudio.com/) など、好みのテキスト エディターをダウンロードします。
1. Azure portal で、ご利用のリソースの **[概要]** タブの下にある FaceAPI エンドポイントおよびキーを取得します。 Face API キーをリモート リポジトリにチェックインしないでください。
1. Android Studio から Android 仮想デバイス エミュレーターを使用して、または独自の Android デバイスを使用して、アプリを実行します。 物理デバイスでアプリをテストするには、関連する <a href="https://reactnative.dev/docs/running-on-device"  title="React Native のドキュメント"  target="_blank">React Native のドキュメント<span class="docon docon-navigate-external x-hidden-focus"></span></a>に従ってください。

#### <a name="ios"></a>[iOS](#tab/ios)

1. [サンプル アプリ](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample)の Git リポジトリをクローンします。
1. 開発環境をセットアップするには、<a href="https://reactnative.dev/docs/environment-setup"  title="React Native のドキュメント"  target="_blank">React Native のドキュメント<span class="docon docon-navigate-external x-hidden-focus"></span></a>に従います。 **[React Native CLI Quickstart]\(React Native CLI クイックスタート\)** を選択します。 開発 OS として **macOS** を選択し、ターゲット OS として **iOS** を選択します。 **依存関係のインストール** に関するセクションを完了します。
1. [Visual Studio Code](https://code.visualstudio.com/) など、好みのテキスト エディターをダウンロードします。 Xcode を置き換える必要もあります。 
1. Azure portal で、ご利用のリソースの **[概要]** タブの下にある FaceAPI エンドポイントおよびキーを取得します。 Face API キーをリモート リポジトリにチェックインしないでください。
1. Xcode からのシミュレートされたデバイスまたはお客様独自の iOS デバイスのいずれかを使用して、アプリを実行します。 物理デバイスでアプリをテストするには、関連する <a href="https://reactnative.dev/docs/running-on-device"  title="React Native のドキュメント"  target="_blank">React Native のドキュメント<span class="docon docon-navigate-external x-hidden-focus"></span></a>に従ってください。

---

## <a name="create-a-user-add-experience"></a>ユーザー追加エクスペリエンスを作成する  

サンプル アプリの設定が完了したので、独自のニーズに合わせてカスタマイズできます。

たとえば、状況に応じた情報を同意ページに追加することができます。

> [!div class="mx-imgBorder"]
> ![アプリの同意ページ](./media/enrollment-app/1-consent-1.jpg)

顔認識の問題の多くは、低品質の参照画像が原因で発生します。 モデルのパフォーマンスを低下させる要因として、次のようなものがあります。
* 顔のサイズ (カメラから離れている顔)
* 顔の向き (カメラに対して横を向いたり斜めになったりしている顔)
* 露光が少ない画像やノイズが多すぎる画像など、よくない照明状態 (光量不足や逆光)
* 帽子や縁の太い眼鏡のようなアクセサリなどのオクルージョン (部分的に隠れた顔や妨げられている顔)
* ぼかし (写真撮影時に顔を急に動かした場合など)。 

このサービスには、その画像が上記の要因に基づいて、顧客を追加したり、顔認識を試みたりするのに十分な品質であるかどうかの選択を行うために役立つ画像品質チェックが用意されています。 このアプリは、デバイスのカメラからフレームにアクセスして品質を検出し、ユーザーがより高品質の画像をキャプチャするために役立つユーザー インターフェイス メッセージを表示し、最高品質のフレームを選択して、検出された顔を Face API サービスに追加する方法を示します。 


> [!div class="mx-imgBorder"]
> ![アプリの画像キャプチャの指示ページ](./media/enrollment-app/4-instruction.jpg)

このアプリには、ユーザーの情報を削除するための機能と再追加のオプションも用意されていることに注意してください。

> [!div class="mx-imgBorder"]
> ![プロファイル管理ページ](./media/enrollment-app/10-manage-2.jpg)

完全なエクスペリエンスに対応するようにアプリの機能を拡張するには、[概要](enrollment-overview.md)に関するページで、実装する他の機能とベスト プラクティスについて参照してください。

## <a name="deploy-the-app"></a>アプリケーションのデプロイ

#### <a name="android"></a>[Android](#tab/android)

最初に、アプリが運用環境に展開できる状態であることを確認します。アプリのコードからキーまたはシークレットを削除し、[セキュリティのベスト プラクティス](../cognitive-services-security.md?tabs=command-line%2ccsharp)に従っていることを確認します。

アプリを運用環境にリリースする準備ができたら、リリース対応の APK ファイルを生成します。これは、Android アプリのパッケージ ファイル形式です。 この APK ファイルに、秘密キーで署名する必要があります。 このリリース ビルドを使用して、デバイスへのアプリの直接的な配布を始めることができます。 

秘密キーを生成し、アプリケーションに署名して、リリース APK を生成する方法については、「<a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title="リリースの準備"  target="_blank">リリースの準備 <span class="docon docon-navigate-external x-hidden-focus"></span></a>」のドキュメントを参照してください。  

署名された APK を作成した後は、「<a href="https://developer.android.com/studio/publish"  title="アプリの公開"  target="_blank">アプリの公開 <span class="docon docon-navigate-external x-hidden-focus"></span></a>」のドキュメントでアプリをリリースする方法の詳細を参照してください。

#### <a name="ios"></a>[iOS](#tab/ios)

最初に、アプリが運用環境に展開できる状態であることを確認します。アプリのコードからキーまたはシークレットを削除し、[セキュリティのベスト プラクティス](../cognitive-services-security.md?tabs=command-line%2ccsharp)に従っていることを確認します。 配布を準備するには、アプリ アイコンと起動画面を作成し、さらにデプロイ情報設定を構成する必要があります。 [Xcode からのドキュメント](https://developer.apple.com/documentation/Xcode/preparing_your_app_for_distribution)に従って、アプリを配布するための準備を行います。 

アプリを運用環境にリリースする準備ができたら、そのアプリのアーカイブを作成します。 アーカイブ ビルドを作成する方法と、アプリを配布するためのオプションについては、[Xcode からのドキュメント](https://developer.apple.com/documentation/Xcode/distributing_your_app_for_beta_testing_and_releases)を参照してください。  

---

## <a name="next-steps"></a>次のステップ  

このガイドでは、開発環境を設定し、サンプル アプリを使い始める方法について説明しました。 React Native を初めて使用する場合は、[入門ドキュメント](https://reactnative.dev/docs/getting-started)に関するページで詳細な背景情報を確認できます。 また、[Face API](Overview.md) について理解しておくことも役に立つ場合があります。 開発を始める前に、ユーザーの追加に関する他のセクションを参照してください。
