---
title: Azure IoT Central エクスプローラーを使用してデバイスの接続を監視する
description: IoT Central エクスプローラー CLI を使用して、デバイスのメッセージを監視し、デバイス ツインの変更を観察します。
author: viv-liu
ms.author: viviali
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 962f394607d20869bf00db624533996b0060eaf2
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "45987243"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Azure IoT Central エクスプローラーを使用してデバイスの接続を監視する

*このトピックはビルダーおよび管理者向けです。*

IoT Central エクスプローラー CLI を使用して、デバイスから IoT Central に送信されるメッセージを確認し、IoT Hub ツインでの変更を観察します。 このオープン ソース ツールを使用して、デバイスの接続状態に関するより詳細な分析情報を取得し、クラウドに達しないデバイス メッセージの問題や、ツイン変更に応答しないデバイスの問題を診断することができます。

## <a name="visit-the-iotc-explorer-repo-in-githubhttpsakamsiotciotcexplorercligithub"></a>[GitHub の iotc-explorer リポジトリを参照する](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>前提条件
+ Node.js バージョン 8.x 以上 - https://nodejs.org
+ ご利用のアプリの管理者に連絡して、iotc-explorer で使用するアクセス トークンを作成してもらう必要があります。

## <a name="installing-iotc-explorer"></a>iotc-explorer のインストール

インストールするには、コマンド ラインから次のコマンドを実行します。

```
npm install -g iotc-explorer
```

> [!NOTE]
> 通常は、Unix のような環境で `sudo` を使用して、インストール コマンドを実行する必要があります。

## <a name="running-iotc-explorer"></a>iotc-explorer の実行

`iotc-explorer` の使用時に実行できるコマンドと一般的なオプションをいくつか以下に示します。 コマンドおよびオプションの完全なセットを表示する場合は、`--help` を `iotc-explorer` またはそのサブコマンドのいずれかに渡すことができます。

### <a name="login"></a>ログイン

作業を開始する前に、ご利用の IoT Central アプリケーションの管理者に連絡して、使用するアクセス トークンを取得してもらう必要があります。 管理者は次の手順を行います。
1. **[管理]、[アクセス トークン]** の順に移動します。 
1. **[Generate]\(生成\)** をクリックします。
![アクセス トークン ページのスクリーンショット](media/howto-use-iotc-explorer/accesstokenspage.png)

1. トークン名を入力し、**[次へ]** をクリックして、**トークンの値をコピー**します。
    > [!NOTE]
    > トークンの値が表示されるのは 1 回だけであるため、ダイアログを閉じる前にコピーする必要があります。 ダイアログを閉じた後、再び表示されることはありません。

    ![アクセス トークンのコピーに関するダイアログのスクリーンショット](media/howto-use-iotc-explorer/copyaccesstoken.png)

その後、そのトークンを使用し、以下のコマンドを実行することで CLI にログインできます。

```sh
iotc-explorer login "<Token value>"
```

シェル履歴にトークンを保持したくない場合は、以下のようにトークンを削除し、代わりに要求されたときに指定することができます。

```
iotc-explorer login
```

### <a name="monitor-device-messages"></a>デバイス メッセージを監視する

`monitor-messages` コマンドを使用して、ご利用のアプリケーションで特定のデバイスまたはすべてのデバイスからのメッセージを監視することができます。 これにより、新しいメッセージが到着したときにそれを継続的に出力する Watcher が起動します。

アプリケーションですべてのデバイスを監視するには、次のコマンドを実行します。

```
iotc-explorer monitor-messages
```
出力: ![monitor-messages コマンドの出力](media/howto-use-iotc-explorer/monitormessages.PNG)

特定のデバイスを監視する場合は、コマンドの末尾にデバイス ID を追加するだけです。

```
iotc-explorer monitor-messages <your-device-id>
```

コマンドに `--raw` オプションを追加して、コマンドの出力をコンピューターでより解釈しやすい形式にすることもできます。

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>デバイス ツインを取得する

`get-twin` コマンドを使用して、IoT Central デバイス用のツインのコンテンツを取得することができます。 これを行うには、次のコマンドを実行します。

```
iotc-explorer get-twin <your-device-id>
```

出力: ![get-twin コマンドの出力](media/howto-use-iotc-explorer/getdevicetwin.PNG)

`monitor-messages` の場合と同じように、`--raw` オプションを渡して、コンピューターでより解釈しやすい出力にすることができます。

```
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>次の手順
これで IoT Central エクスプローラーの使用方法についての学習が終わりました。推奨される次の手順は、[IoT Central でのデバイスの管理](howto-manage-devices.md)について確認することです。
