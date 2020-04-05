---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 46cfb27b8bde95990d13ec4bca4e96f25cfe9dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "67181690"
---
Android SDK の開発が継続中であるため、Android Studio にインストールされている Android SDK のバージョンが、コードのバージョンと一致しない可能性があります。 このチュートリアルで参照している Android SDK は、記事の執筆時点で最新のバージョンである、バージョン 26 です。 SDK の新しいリリースが登場するにつれてバージョン番号が大きくなる可能性があるので、入手可能な最新バージョンを使用することをお勧めします。

バージョンの不一致の場合に見られる現象は、次の 2 つです。

- プロジェクトをビルドまたは再ビルドすると、`Gradle sync failed: Failed to find target with hash string 'android-XX'` などの Gradle エラー メッセージが表示される場合があります。
- `import` ステートメントに基づく解決が必要なコード内の標準の Android オブジェクトによって、エラー メッセージが生成される場合があります。

このいずれかが発生した場合、Android Studio にインストールされている Android SDK のバージョンが、ダウンロードしたプロジェクトの SDK ターゲットと一致していない可能性があります。 バージョンを確認するには、次の変更を加えます。

1. Android Studio で、 **[Tools (ツール)]**  >  **[Android]**  >  **[SDK Manager (SDK マネージャー)]** の順にクリックします。 SDK プラットフォームの最新バージョンをまだインストールしていない場合は、これをクリックしてインストールします。 バージョン番号をメモしておきます。

2. **[Project Explorer]\(プロジェクト エクスプローラー\)** タブの **[Gradle Scripts]\(Gradle スクリプト\)** で、**build.gradle (Module: app)** ファイルを開きます。 **[compileSdkVersion]** と **[targetSdkVersion]** が、インストールされた最新の SDK バージョンに設定されていることを確認します。 `build.gradle` は、次のようになります。

    ```gradle
    android {
        compileSdkVersion 26
        defaultConfig {
            targetSdkVersion 26
        }
    }
    ```
