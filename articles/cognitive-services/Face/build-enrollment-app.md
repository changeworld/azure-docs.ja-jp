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
ms.openlocfilehash: 39a74c7f3d5fb8f8b60a66947fcce9837ed6ee13
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505107"
---
# <a name="build-a-react-app-to-add-users-to-a-face-service"></a>ユーザーを Face サービスに追加するための React アプリを作成する

このガイドでは、サンプルの顔登録アプリケーションの使用を始める方法について説明します。 このアプリでは、ユーザーを顔認識サービスに追加し、精度の高い顔データを取得することを目的として、意味のある同意を得るためのベスト プラクティスを示します。 統合されたシステムでは、このようなアプリを使用して、顔データに基づく、タッチレス アクセス制御、ID 検証、出社追跡、個人用設定キオスク、または ID 検証を提供できます。

アプリケーションを起動すると、ユーザーに詳細な同意画面が表示されます。 ユーザーが同意すると、アプリによってユーザー名とパスワードを入力するよう要求され、デバイスのカメラを使用して高品質の顔の画像がキャプチャされます。

サンプル アプリは、JavaScript と React Native フレームワークを使用して記述されています。 現在は、Android デバイスに展開できます。今後、展開オプションをさらに増やすことが予定されています。

## <a name="prerequisites"></a>必須コンポーネント 

* Azure サブスクリプション。[無料で作成できます](https://azure.microsoft.com/free/cognitive-services/)。  
* Azure サブスクリプションを入手したら、Azure portal で [Face リソースを作成](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace)し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** を選択します。  
  * アプリケーションを Face API に接続するには、作成したリソースのキーとエンドポイントが必要です。  
  * ローカル環境で開発とテストを行う場合は、API キーとエンドポイントを構成ファイルに貼り付けることができます。 最終的な展開では、API キーを安全な場所に格納し、コードには格納しません。  

> [!IMPORTANT]
> これらのサブスクリプション キーは、Cognitive Service API にアクセスするために使用されます。 キーを共有しないでください。 Azure Key Vault を使用するなどして、安全に保管してください。 これらのキーを定期的に再生成することもお勧めします。 API 呼び出しを行うために必要なキーは 1 つだけです。 最初のキーを再生成するときに、2 番目のキーを使用してサービスに継続的にアクセスすることができます。

## <a name="set-up-the-development-environment"></a>開発環境を設定する

1. [サンプル アプリ](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample)の Git リポジトリをクローンします。
1. 開発環境をセットアップするには、<a href="https://reactnative.dev/docs/environment-setup"  title="React Native のドキュメント"  target="_blank">React Native のドキュメント</a>に従います。 開発 OS として **React Native CLI Quickstart** を選択し、ターゲット OS として **Android** を選択します。 **依存関係のインストール** と **Android 開発環境** に関するセクションを完了します。
1. [Visual Studio Code](https://code.visualstudio.com/) などの任意のテキスト エディターで env.json ファイルを開き、エンドポイントとキーを追加します。 エンドポイントとキーは、Azure portal でリソースの **[概要]** タブを見るとわかります。 この手順は、ローカル環境でテストを行う場合にのみ必要です。Face API キーをリモート リポジトリにチェックインしないでください。
1. Android Studio から Android 仮想デバイス エミュレーターを使用して、または独自の Android デバイスを使用して、アプリを実行します。 物理デバイスでアプリをテストするには、関連する <a href="https://reactnative.dev/docs/running-on-device"  title="React Native のドキュメント"  target="_blank">React Native のドキュメント</a>に従ってください。  


## <a name="create-a-user-add-experience"></a>ユーザー追加エクスペリエンスを作成する  

サンプル アプリの設定が完了したので、独自のニーズに合わせてカスタマイズできます。

たとえば、状況に応じた情報を同意ページに追加することができます。

> [!div class="mx-imgBorder"]
> ![アプリの同意ページ](./media/enrollment-app/1-consent-1.jpg)

このサービスには、画像が顧客を追加したり顔認識を試みたりするのに十分な品質かどうかを判断するのに役立つ画像品質チェックが用意されています。 このアプリでは、デバイスのカメラからフレームにアクセスし、最高品質のフレームを選択して、検出された顔を Face API サービスに追加する方法を示します。 

顔認識の問題の多くは、低品質の参照画像が原因で発生します。 モデルのパフォーマンスを低下させる要因として、次のようなものがあります。
* 顔のサイズ (カメラから離れている顔)
* 顔の向き (カメラに対して横を向いたり斜めになったりしている顔)
* 露光が少ない画像やノイズが多すぎる画像など、よくない照明状態 (光量不足や逆光)
* 帽子や縁の太い眼鏡のようなアクセサリなどのオクルージョン (部分的に隠れた顔や妨げられている顔)
* ぼかし (写真撮影時に顔を急に動かした場合など)。 

> [!div class="mx-imgBorder"]
> ![アプリの画像キャプチャの指示ページ](./media/enrollment-app/4-instruction.jpg)

このアプリには、ユーザーの情報を削除するための機能と再追加のオプションも用意されていることに注意してください。

> [!div class="mx-imgBorder"]
> ![プロファイル管理ページ](./media/enrollment-app/10-manage-2.jpg)

完全なエクスペリエンスに対応するようにアプリの機能を拡張するには、[概要](enrollment-overview.md)に関するページで、実装する他の機能とベスト プラクティスについて参照してください。

## <a name="deploy-the-app"></a>アプリケーションのデプロイ

### <a name="android"></a>Android

最初に、アプリが運用環境に展開できる状態であることを確認します。アプリのコードからキーまたはシークレットを削除し、[セキュリティのベスト プラクティス](../cognitive-services-security.md?tabs=command-line%2ccsharp)に従っていることを確認します。

アプリを運用環境にリリースする準備ができたら、リリース対応の APK ファイルを生成します。これは、Android アプリのパッケージ ファイル形式です。 この APK ファイルに、秘密キーで署名する必要があります。 このリリース ビルドを使用して、デバイスへのアプリの直接的な配布を始めることができます。 

秘密キーを生成し、アプリケーションに署名して、リリース APK を生成する方法については、「<a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title="リリースの準備"  target="_blank">リリースの準備 </a>」のドキュメントを参照してください。  

署名された APK を作成した後は、「<a href="https://developer.android.com/studio/publish"  title="アプリの公開"  target="_blank">アプリの公開 </a>」のドキュメントでアプリをリリースする方法の詳細を参照してください。

## <a name="next-steps"></a>次のステップ  

このガイドでは、開発環境を設定し、サンプル アプリを使い始める方法について説明しました。 React Native を初めて使用する場合は、[入門ドキュメント](https://reactnative.dev/docs/getting-started)に関するページで詳細な背景情報を確認できます。 また、[Face API](Overview.md) について理解しておくことも役に立つ場合があります。 開発を始める前に、ユーザーの追加に関する他のセクションを参照してください。