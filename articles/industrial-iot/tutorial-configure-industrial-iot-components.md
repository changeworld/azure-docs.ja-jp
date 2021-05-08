---
title: Azure Industrial IoT コンポーネントを構成する
description: このチュートリアルでは、構成の既定値を変更する方法について説明します。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 348276a71b2227063374da6455445118fcb00fcf
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787230"
---
# <a name="tutorial-configure-the-industrial-iot-components"></a>チュートリアル: Industrial IoT コンポーネントを構成する

デプロイ スクリプトを使用すると、既定値を使用してすべてのコンポーネントが相互に連携するように自動的に構成されます。 ただし、既定値の設定は、要件に合わせて変更できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * コンポーネントの構成をカスタマイズする


ここでは、各コンポーネントに対するより適切なカスタマイズ設定をいくつか示します。
* IoT Hub
    * ネットワーク → ｢パブリック アクセス: インターネット アクセス (IP フィルターなど) を構成する
    * [ネットワーク] → [プライベート エンドポイント接続]: インターネットを介してアクセスできず、他の Azure サービスまたはオンプレミスのデバイス (VPN 接続経由など) によって内部的に使用できるエンドポイントを作成する
    * IoT Edge: OPC UA サーバーに接続されているエッジ デバイスの構成を管理する 
* Cosmos DB
    * データをグローバルにレプリケートする: データの冗長性を構成する
    * ファイアウォールと仮想ネットワーク: インターネットおよび VNET のアクセスと、IP フィルターを構成する
    * プライベート エンドポイント接続: インターネット経由でアクセスできないエンドポイントを作成する 
* Key Vault
    * シークレット: プラットフォーム設定を管理する
    * アクセス ポリシー: Key Vault 内のデータにアクセスできるアプリケーションおよびユーザーと、それらがネットワーク、ファイアウォール、VNET、およびプライベート エンドポイントで実行することを許可されている操作 (読み取り、書き込み、一覧表示、削除など) を管理する
* Azure Active Directory (AAD) → [アプリの登録]
    * <APP_NAME>-Web → [認証]: 応答 URI (認証が成功した後にランディング ページとして使用できる URI の一覧) を管理します。 AAD 管理者権限がないなど、特定のシナリオでは、デプロイ スクリプトでこれを自動的に構成できない場合があります。 Web アプリのホスト名を変更するときに、URI を追加または変更できます (デバッグのために localhost で使用されるポート番号など)
* App Service
    * 構成: サービスまたは UI を制御する環境変数を管理する
* 仮想マシン
    * ネットワーク: サポートされているネットワークとファイアウォール規則を構成する
    * Serial console: 分析情報の取得またはデバッグのために SSH アクセスしたり、デプロイ スクリプトの出力から資格情報の取得したり、パスワードのリセットを行ったりする
* [IoT Hub] → [IoT Edge]
    * ハブにアクセスできる IoT Edge デバイスの ID を管理し、インストールされるモジュールとそれらによって使用される構成 (OPC Publisher のエンコード パラメーターなど) を設定する
* IoT Hub → IoT Edge → \<DEVICE> → ［モジュールの設定 → OpcPublisher (スタンドアロン OPC Publisher 操作専用)


## <a name="configuration-options"></a>構成オプション

|構成オプション (短縮形/完全名)    |    説明   |
|----------------------------------------------|------------------|
pf/publishfile |発行するノードを構成するためのファイル名。 このオプションを指定すると、OPC Publisher がスタンドアロン モードになります。
lf/logfile |使用するログ ファイルのファイル名。
ll/loglevel |使用するログ レベル (使用できる値: fatal、error、warn、info、debug、verbose)。
me/messageencoding |送信メッセージのメッセージング エンコード。使用できる値: Json、Uadp
mm/messagingmode |送信メッセージのメッセージング モード。使用できる値: PubSub、Samples
fm/fullfeaturedmessage |メッセージのフル機能モード (すべてのフィールドが入力されます)。 既定値は 'true'。レガシとの互換性を保つためには 'false' を使用する
aa/autoaccept |パブリッシャーは、接続先のすべてのサーバーを信頼します
bs/batchsize |バッチ処理のためにキャッシュされる OPC UA データ変更メッセージの数。
si/iothubsendinterval |トリガーのバッチ処理間隔 (秒単位)。
ms/iothubmessagesize |(IoT D2C) メッセージの最大サイズ。
om/maxoutgressmessages |(IoT D2C) メッセージ エグレス バッファーの最大サイズ。
di/diagnosticsinterval |指定した間隔 (秒単位) でパブリッシャーの診断情報を表示します (ログ レベル情報が必要)。 -1 にすると、リモート診断ログと診断出力が無効になります
lt/logflugtimespan |ログ ファイルをフラッシュする必要がある時間間隔 (秒単位)。
ih/iothubprotocol |ハブとの通信に使用するプロトコル。 使用できる値: AmqpOverTcp、AmqpOverWebsocket、MqttOverTcp、MqttOverWebsocket、Amqp、Mqtt、Tcp、Websocket、Any
hb/heartbeatinterval |パブリッシャーは、ハートビート間隔設定を使用せずに、ノードのハートビート間隔設定の既定値 (秒単位) としてこれを使用します。
ot/operationtimeout |パブリッシャー OPC UA クライアントの操作タイムアウト (ms)。
ol/opcmaxstringlen |opc が送受信できる文字列の最大長。
oi/opcsamplinginterval |値をサンプリングするようにサーバーに要求する間隔の既定値 (ミリ秒)
op/opcpublishinginterval |OPC UA サーバーに対するサブスクリプションの発行間隔設定の既定値 (ミリ秒)。
ct/createsessiontimeout |パブリッシャーが接続先のエンドポイント上の OPC サーバーにキープ アライブ メッセージを送信する間隔 (秒単位)。
kt/keepalivethresholt |セッションが切断されるまでに、サーバーが取得できなくても許容されるキープ アライブ パケットの数を指定します。
tm/trustmyself |パブリッシャー証明書は、信頼されたストアに自動的に格納されます。
at/appcertstoretype |独自のアプリケーション証明書ストアの種類 (使用できる値: Directory、X509Store)。


## <a name="next-steps"></a>次のステップ
構成の既定値を変更する方法を学んだので、次のことができます。 

> [!div class="nextstepaction"]
> [IIoT データを ADX にプルする](tutorial-industrial-iot-azure-data-explorer.md)

> [!div class="nextstepaction"]
> [Time Series Insights を使用してデータを視覚化および分析する](tutorial-visualize-data-time-series-insights.md)
