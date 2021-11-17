---
title: ServiceNow を Microsoft Defender for IoT と統合する
description: このチュートリアルでは、ServiceNow と Microsoft Defender for IoT を統合する方法について説明します。
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 9b01aa92848bc69bff37dc75be8e59350bc9f3f3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343555"
---
# <a name="tutorial-integrate-servicenow-with-microsoft-defender-for-iot"></a>チュートリアル: ServiceNow を Microsoft Defender for IoT と統合する

このチュートリアルでは、ServiceNow と Microsoft Defender for IoT の統合、および使用方法を学ぶことができます。

Defender for IoT と ServiceNow を統合することで、IoT と OT のランドスケープで、一元的な可視性、監視、制御を新たなレベルで実現することができます。 このようにブリッジされたプラットフォームを使用することで、これまで到達できなかった ICS および IoT デバイスに対する、自動でのデバイスの可視化と脅威管理が可能になります。

ServiceNow 構成管理データベース (CMDB) は、Defender for IoT プラットフォームによってプッシュされた一連の豊富なデバイス属性によって強化、補完されています。 これにより、デバイス ランドスケープを包括的かつ継続的に可視化できます。 可視化により、1 つのウィンドウから監視して対応できます。 

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * ServiceNow で Defender for IoT アプリケーションをダウンロードする
> * ServiceNow と通信するように Defender for IoT を設定する
> * ServiceNow でアクセス トークンを作成する
> * Defender for IoT デバイスの属性を ServiceNow に送信する
> * HTTPS プロキシを使用して統合を設定する
> * ServiceNow で Defender for IoT の検出を表示する
> * 接続されたデバイスの表示

## <a name="prerequisites"></a>前提条件

### <a name="software-requirements"></a>ソフトウェア要件

ServiceNow と Defender for IoT へのアクセス 

- ServiceNow サービス管理バージョン 3.0.2。

- Defender for IoT パッチ 2.8.11.1 以降。

> [!Note]
> すでに Defender for IoT と ServiceNow の統合を使用しており、オンプレミスの管理コンソールを使用してアップグレードしている場合は、Defender for IoT センサーによって取得された過去のデータを ServiceNow から消去する必要があります。

### <a name="architecture"></a>Architecture

- **オンプレミスの管理コンソールのアーキテクチャ**: オンプレミスの管理コンソールを設定して、ServiceNow の 1 つのインスタンスと通信します。 オンプレミスの管理コンソールは、REST API を使用して、センサー データを Defender for IoT アプリケーションにプッシュします。

    オンプレミスの管理コンソールを使用するようにシステムを設定するには、設定されたすべてのセンサーの ServiceNow 同期、転送ルール、およびプロキシ構成を無効にする必要があります。

- **センサー アーキテクチャ**: センサーと ServiceNow の間の直接通信を含めるように環境を設定する場合は、センサーごとに ServiceNow 同期、転送ルール、プロキシ構成 (プロキシが必要な場合) を定義します。

## <a name="download-the-defender-for-iot-application-in-servicenow"></a>ServiceNow で Defender for IoT アプリケーションをダウンロードする

ServiceNow 内で Defender for IoT アプリケーションにアクセスするには、ServiceNow アプリケーション ストアからアプリケーションをダウンロードする必要があります。 

**ServiceNow で Defender for IoT アプリケーションをダウンロードするには、次の手順を実行します。**

1. [ServiceNow アプリケーション ストア](https://store.servicenow.com/)に移動します。

1. `Defender for IoT` または `CyberX IoT/ICS Management` を検索します。

   :::image type="content" source="media/tutorial-servicenow/search-results.png" alt-text="ServiceNow の検索画面のスクリーンショット。":::

1. アプリケーションを選択します。

   :::image type="content" source="media/tutorial-servicenow/cyberx-app.png" alt-text="検索画面の結果のスクリーンショット。":::

1. **[アプリのリクエスト]** を選択します。

   :::image type="content" source="media/tutorial-servicenow/sign-in.png" alt-text="資格情報を使用してアプリケーションにサインインします。":::

1. サインインしてアプリケーションをダウンロードします。

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>ServiceNow と通信するように Defender for IoT を設定する

アラート情報を ServiceNow テーブルにプッシュするように Defender for IoT を構成します。 Defenders for IoT のアラートは、セキュリティ インシデントとして ServiceNow に表示されます。 これは、アラート情報を ServiceNow に送信するための、Defender for IoT の転送ルールを定義することによって実現できます。

**ServiceNow テーブルにアラート情報をプッシュするには、次の手順を実行します。**

1. オンプレミス管理コンソールにサインインします。

1. 左側のウィンドウで、 **[転送]** を選択します。

1. :::image type="icon" source="media/tutorial-servicenow/plus-icon.png" border="false"::: ボタンを選択します。

    :::image type="content" source="media/tutorial-servicenow/forwarding-rule.png" alt-text="[転送ルールの作成] ウィンドウのスクリーンショット。":::

1. ルールの名前を追加します。

1. Defender for IoT によって転送ルールがトリガーされる基準を定義します。 転送ルールの条件を使用すると、Defender for IoT から ServiceNow に送信される情報の量を特定して管理することができます。 次のオプションを使用できます。

    - **重大度レベル:** これは、転送するインシデントの最小セキュリティ レベルです。 たとえば、 **[マイナー]** を選択すると、マイナー アラートおよびこの重大度レベルよりも上のすべてのアラートが転送されます。 レベルは、Defender for IoT によって事前に定義されています。

    - **プロトコル:** 検出されたトラフィックが特定のプロトコルを介して実行されていた場合にのみ転送ルールをトリガーします。 ドロップダウン リストから必要なプロトコルを選択するか、それらをすべて選択します。

    - **エンジン:** 必要なエンジンを選択するか、それらをすべて選択します。 選択したエンジンからのアラートが送信されます。

1. **[Report Alert Notifications]\( アラート通知のレポート\)** が選択されていることを確認します。

1. [操作] セクションで、 **[追加]** を選択してから **[ServiceNow]** を選択します。

    :::image type="content" source="media/tutorial-servicenow/select-servicenow.png" alt-text="ドロップダウン オプションから [ServiceNow] を選択します。":::

1. 次のように ServiceNow アクションのパラメーターを入力します。

    :::image type="content" source="media/tutorial-servicenow/parameters.png" alt-text="ServiceNow アクションのパラメーターを入力します。":::

1. **[操作]** ペインで、次のパラメーターを設定します。

      | パラメーター | 説明 |
      |--|--|
      | Domain | ServiceNow サーバーの IP アドレスを入力します。 |
      | Username | ServiceNow サーバーのユーザー名を入力します。 |
      | Password | ServiceNow サーバーのパスワードを入力します。 |
      | クライアント ID | ServiceNow の **[Application Registries]\(アプリケーション レジストリ\)** ページで、Defender for IoT 用に受信したクライアント ID を入力します。 |
      | クライアント シークレット | Defender for IoT 用に ServiceNow の **[Application Registries]\(アプリケーション レジストリ\)** ページで作成したクライアント シークレット文字列を入力します。 |
      | レポートの種類 | **インシデント**:ServiceNow に示されたアラートの一覧を、各アラートのインシデント ID と簡単な説明と共に転送します。<br /><br />**Defender for IoT アプリケーション**:センサーの詳細、エンジン、ソース、宛先アドレスなどを含む、完全なアラート情報を転送します。 この情報は、ServiceNow アプリケーション上の Defender for IoT に転送されます。 |

1. **[SAVE]\(保存\)** を選択します。 

Defenders for IoT のアラートは、インシデントとして ServiceNow に表示されるようになります。

## <a name="create-access-tokens-in-servicenow"></a>ServiceNow でアクセス トークンを作成する

ServiceNow が Defender for IoT と通信するには、トークンが必要です。

Defender for IoT 転送ルールを作成するときに入力した `Client ID` と `Client Secret` が必要です。 転送ルールによりアラート情報が ServiceNow に転送されます。デバイス属性を ServiceNow のテーブルにプッシュするよう Defender for IoT を構成するときもです。

## <a name="send-defender-for-iot-device-attributes-to-servicenow"></a>Defender for IoT デバイスの属性を ServiceNow に送信する

さまざまなデバイス属性を ServiceNow テーブルにプッシュするように Defender for IoT を構成します。 属性を ServiceNow に送信するには、オンプレミスの管理コンソールを ServiceNow インスタンスにマップする必要があります。 こうすることで、Defender for IoT プラットフォームからインスタンスへ通信し、認証できるようになります。

**ServiceNow インスタンスを追加するには、次の手順を実行します。**

1. Defender for IoT のオンプレミスの管理コンソールにサインインします。

1. オンプレミスの管理コンソールの [統合] セクションで、 **[システム設定]** 、 **[ServiceNow]** の順に選択します。

      :::image type="content" source="media/tutorial-servicenow/servicenow.png" alt-text="[ServiceNow] ボタンの選択のスクリーンショット。":::

1. [ServiceNow Sync]\(ServiceNow の同期\) ダイアログ ボックスで、次の同期パラメーターを入力します。

    :::image type="content" source="media/tutorial-servicenow/sync.png" alt-text="[ServiceNow 同期] ダイアログ ボックスのスクリーンショット。":::

     パラメーター | [説明] |
    |--|--|
    | 同期を有効にする | パラメーターを定義した後に同期を有効または無効にします。 |
    | Sync Frequency (minutes) (同期の頻度 (分)) | 既定では、情報は 60 分ごとに ServiceNow にプッシュされます。 最小値は 5 分です。 |
    | ServiceNow インスタンス | ServiceNow インスタンスの URL を入力します。 |
    | クライアント ID | ServiceNow の **[Application Registries]\(アプリケーション レジストリ\)** ページで、Defender for IoT 用に受信したクライアント ID を入力します。 |
    | クライアント シークレット | Defender for IoT 用に ServiceNow の **[Application Registries]\(アプリケーション レジストリ\)** ページで作成したクライアント シークレット文字列を入力します。 |
    | Username | このインスタンスのユーザー名を入力します。 |
    | Password | このインスタンスのパスワードを入力します。 |

1. **[SAVE]\(保存\)** を選択します。

"最後の同期" が実行された日付を確認して、オンプレミスの管理コンソールが ServiceNow インスタンスに接続されていることを確認します。

:::image type="content" source="media/tutorial-servicenow/sync-confirmation.png" alt-text="前回の同期を確認することで発生する通信のスクリーンショット。":::

## <a name="set-up-the-integrations-using-an-https-proxy"></a>HTTPS プロキシを使用して統合を設定する

Defender for IoT と ServiceNow の統合を設定する際、オンプレミスの管理コンソールと ServiceNow サーバーは、ポート 443 を使用して通信します。 ServiceNow サーバーがプロキシの内側にある場合、既定のポートは使用できません。

Defender for IoT では、統合に使用する規定のポートの変更を有効にすることで、ServiceNow 統合で HTTPS プロキシがサポートされるようになります。

**プロキシを構成するには、次の手順を実行します。**

1. 次のコマンドを使用して、オンプレミスの管理コンソールのグローバル プロパティを編集します。

    ```bash
    sudo vim /var/cyberx/properties/global.properties
    ```

2. 次のパラメーターを追加します:

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. **[保存して終了]** を選択します。

4. 次のコマンドを使用して、オンプレミスの管理コンソールをリセットします。 

    ```bash
    sudo monit restart all
    ```

構成が設定されると、構成されたプロキシを使用してすべての ServiceNow データが転送されます。

## <a name="view-defender-for-iot-detections-in-servicenow"></a>ServiceNow で Defender for IoT の検出を表示する

この記事では、ServiceNow に表示されるデバイス属性とアラート情報について説明します。

**デバイス属性を表示するには、以下の手順を実行します。**

1. ServiceNow にサインインします。

2. **[CyberX Platform]\(CyberX プラットフォーム\)** に移動します。

3. **[インベントリ]** または **[アラート]** に移動します。

   [:::image type="content" source="media/tutorial-servicenow/alert-list.png" alt-text="インベントリまたはアラートのスクリーンショット。":::](media/tutorial-servicenow/alert-list.png#lightbox)

## <a name="view-connected-devices"></a>接続されたデバイスの表示

接続されているデバイスを表示するには、次の手順を実行します。

1. デバイスを選択してから、そのデバイスに一覧表示されている **[アプライアンス]** を選択します。

    :::image type="content" source="media/tutorial-servicenow/appliance.png" alt-text="一覧の目的のアプライアンスのスクリーンショット。":::

1. **[デバイスの詳細]** ダイアログ ボックスで、 **[接続されているデバイス]** を選択します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップするリソースがありません。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ServiceNow 統合の使用を開始する方法を学習しました。 [Cisco 統合](./tutorial-forescout.md)の詳細については、続行してください。

> [!div class="nextstepaction"]
> [次のステップのボタン](./tutorial-forescout.md)
