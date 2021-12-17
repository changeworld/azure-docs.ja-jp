---
title: プロキシを使用してセンサーを接続する
description: インターネットへの直接アクセスがないセンサーと、プロキシを介して通信するように Microsoft Defender for IoT を構成する方法について説明します。
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 0a6e2ef526985ca5e8bee208989310ed01f18267
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278799"
---
# <a name="connect-microsoft-defender-for-iot-sensors-without-direct-internet-access-by-using-a-proxy"></a>インターネットへの直接アクセスがない Microsoft Defender for IoT センサーにプロキシを使用して接続する 

この記事では、インターネットへの直接アクセスがないセンサーと、プロキシを介して通信するように Microsoft Defender for IoT を構成する方法について説明します。 HTTP トンネリングがある転送プロキシを使用してセンサーに接続します。接続用の HTTP CONNECT コマンドが使用されます。 ここで説明する手順は、オープンソースの Squid プロキシを使用することを前提としていますが、CONNECT を使用できるその他のプロキシも使用できます。 

このプロキシでは、暗号化された SSL トンネルを使用してセンサーからサービスにデータが転送されます。 プロキシでは、データの検査、分析、キャッシュは行われません。 

次の図は、IT ネットワークに配置されたプロキシと産業用 DMZ を介して、データが OT セグメント内の Microsoft Defender for Cloud IoT センサーからクラウドに送信される様子を示しています。

:::image type="content" source="media/how-to-connect-sensor-by-proxy/cloud-access.png" alt-text="クラウドを通してセンサーをプロキシに接続する。":::

## <a name="set-up-your-system"></a>システムを設定する

このシナリオでは、最新バージョンの [Squid](http://www.squid-cache.org/) を Ubuntu 18 サーバーにインストールして構成します。

> [!Note]
> Microsoft Defender for IoT では、Squid またはその他のプロキシ サービスに対するサポートは提供されていません。

**Ubuntu 18 サーバーに Squid プロキシをインストールするには**:

1. 指定されたプロキシ Ubuntu マシンにサインインします。

1. ターミナル ウィンドウを開始します。
 
1. 次のコマンドを使用して、ソフトウェアを最新バージョンに更新します。

    ```bash
    sudo apt-get update 
    ```

1. 次のコマンドを使用して、Squid パッケージをインストールします。

    ```bash
    sudo apt-get install squid 
    ```

1. `/etc/squid/squid.conf` と `/etc/squid/conf.d/` に配置されている Squid 構成ファイルを見つけます。

1. 次のコマンドを使用して、元のファイルのバックアップを作成します。

    ```bash
    sudo cp -v /etc/squid/squid.conf{,.factory}'/etc/squid/squid.conf' -> '/etc/squid/squid.conf.factory sudo nano /etc/squid/squid.conf
    ```

1. テキスト エディターで `/etc/squid/squid.conf` を開きます。

1. `# INSERT YOUR OWN RULE(S) HERE TO ALLOW ACCESS FROM YOUR CLIENTS` を検索します。

1. ファイルに `acl sensor1 src <sensor-ip>` と `http_access allow sensor1` を追加します。

    :::image type="content" source="media/how-to-connect-sensor-by-proxy/add-lines.png" alt-text="テキストに次の 2 行を追加してファイルを保存する。":::

1. (省略可能) 各センサー用の行を追加することで、センサーを追加します。

1. 次のコマンドを使用して、起動時にSquid サービスを開始できるようにします。

    ```bash
    sudo systemctl enable squid 
    ```

## <a name="set-up-a-sensor-to-use-squid"></a>Squid を使用するセンサーを設定する

**Squid を使用するセンサーを設定するには**:

1. センサーにサインインします。

1. **[システム設定]**  >  **[ネットワーク]** に移動します。

1. **[プロキシを有効にする]** を選択します。

    :::image type="content" source="media/how-to-connect-sensor-by-proxy/enable-proxy.png" alt-text="[センサー ネットワーク構成] ウィンドウで [プロキシを有効にする] を選択する。":::

1. プロキシ アドレスを入力します。

1. ポートを入力します。 既定のポートは `3128` です。

1. (省略可能) プロキシ ユーザーとパスワードを入力します。

1. **[保存]** を選択します。

## <a name="see-also"></a>関連項目

[サブスクリプションを管理する](how-to-manage-subscriptions.md)。
