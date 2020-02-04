---
title: インクルード ファイル
description: インクルード ファイル
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772418"
---
### <a name="create-an-environment-variable"></a>環境変数を作成する

ランタイム キーとランタイム エンドポイントを使用して、認証とアクセスのための環境変数を作成します。

* `LUIS_RUNTIME_KEY` - 要求を認証するためのランタイム リソース キー。
* `LUIS_RUNTIME_ENDPOINT` - 自分のキーに関連付けられたランタイム エンドポイント。
* `LUIS_APP_ID` - パブリック LUIS IoT アプリ ID は `df67dcdb-c37d-46af-88e1-8b97951ca1c2` です。
* `LUIS_APP_SLOT_NAME` - `production` または `staging`

このクイックスタートを使用して独自のアプリにアクセスする場合は、追加の手順を実行する必要があります。
* アプリを作成し、アプリ ID を取得する
* LUIS ポータル内でアプリにランタイム キーを割り当てる
* ブラウザーで URL を使用してアプリにアクセスできるかどうかをテストする

ご利用のオペレーティング システムの手順に従ってください。

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

環境変数を追加したら、コンソール ウィンドウを再起動します。

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source ~/.bashrc` を実行します。

#### <a name="macostabunix"></a>[macOS](#tab/unix)

次のように `.bash_profile` を編集し、環境変数を追加します。

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source .bash_profile` を実行します。

***
