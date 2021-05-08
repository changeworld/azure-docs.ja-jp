---
title: ServiceNow の統合について
description: ServiceNow 用 Defender for IoT ICS 管理アプリケーションを使用することで、SOC アナリストは産業環境に展開された特殊な OT プロトコルと IoT デバイスを多次元で可視性し、疑わしい動作や異常な動作を迅速に検出するための ICS 対応の行動分析することが可能になります。
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 6e39c3d018003983f6dc5b5e16a9791de84d6005
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786007"
---
# <a name="the-defender-for-iot-ics-management-application-for-servicenow"></a>ServiceNow 用 Defender for IoT ICS 管理アプリケーション

ServiceNow 用 Defender for IoT ICS 管理アプリケーションを使用することで、SOC アナリストは産業環境に展開された特殊な OT プロトコルと IoT デバイスを多次元で可視性し、疑わしい動作や異常な動作を迅速に検出するための ICS 対応の行動分析することが可能になります。 これは、スマート コンピューターやリアルタイム インテリジェンスなどの新しい IoT イニシアティブをサポートするために IT と OT の融合が進んでいることを考えると、重要な進化です。

また、このアプリケーションを使用すると、1 つの企業 SOC 内から、IT と OT 両方のインシデントに対応できます。

## <a name="about-defender-for-iot"></a>Defender for IoT について

Defender for IoT は、重要な国家インフラを守る実績を持つ Blue Team のエキスパートによって構築された唯一の ICS および IoT サイバーセキュリティ プラットフォームであるだけでなく、特許を取得した ICS 対応の脅威分析と機械学習を備えた唯一のプラットフォームです。 Defender for IoT は、以下を提供します。

- 属性に関するさまざまな詳細情報を含む、デバイス ランドスケープの ICS に関する分析情報を即座に提供。

- ICS に対応した、OT プロトコル、デバイス、アプリケーション、およびそれらの動作に関する組み込まれた深い知識。

- 脆弱性および既知のゼロデイ脅威に対する即座の分析情報。

- 独自の分析により、標的型 ICS 攻撃の最も可能性の高い経路を予測する自動 ICS 脅威モデル化テクノロジ。

> [!Note]
> CyberX について記載があった場合、これは Azure Defender for IoT を指しています。

## <a name="about-the-integration"></a>統合について

Defender for IoT と ServiceNow を統合することで、IoT と OT のランドスケープで、一元的な可視性、監視、制御を新たなレベルで実現することができます。 このようにブリッジされたプラットフォームを使用することで、これまで到達できなかった ICS および IoT デバイスに対する、自動でのデバイスの可視化と脅威管理が可能になります。

### <a name="threat-management"></a>脅威管理

Defender for IoT ICS 管理アプリケーションは、以下のことに役立ちます。

- 産業および重要なインフラストラクチャ組織がサイバー脅威を検出、調査、対処するのに必要な時間を短縮。

- OT リスクについてのリアルタイムのインテリジェンスを取得。

- Defender for IoT アラートを ServiceNow の脅威監視およびインシデント管理ワークフローと関連付ける。

- ServiceNow のチケットとワークフローを、ServiceNow プラットフォーム上の他のサービスやアプリケーションによってトリガーする。

ICS と SCADA のセキュリティ脅威は、Defender for IoT セキュリティ エンジンによって識別され、これによりマルウェア攻撃、ネットワーク、セキュリティ ポリシーの逸脱だけでなく、運用上またはプロトコルの異常などに対するアラート応答を即座に提供します。 ServiceNow に送信されるアラートの詳細については、「[アラートのレポート](#alert-reporting)」を参照してください。

### <a name="device-visibility-and-management"></a>デバイスの可視性と管理

ServiceNow 構成管理データベース (CMDB) は、Defender for IoT プラットフォームによってプッシュされた一連の豊富なデバイス属性によって強化、および補完されています。 これにより、デバイス ランドスケープを包括的かつ継続的に可視化できるようになり、1 つのウィンドウで監視および対応することが可能になります。 ServiceNowSee に送信されるデバイス属性の詳細については、「[ServiceNow で Defender for IoT の検出を表示する](#view-defender-for-iot-detections-in-servicenow)」を参照してください。

## <a name="system-requirements-and-architecture"></a>システム要件とアーキテクチャ

この記事では、次の内容について説明します。

- **ソフトウェア要件**  
- **アーキテクチャ**

## <a name="software-requirements"></a>ソフトウェア要件

- ServiceNow サービス管理バージョン 3.0.2

- Defender for IoT パッチ 2.8.11.1 以降

> [!Note]
> すでに Defender for IoT と ServiceNow の統合を使用しており、オンプレミスの管理コンソールを使用してアップグレードしている場合は、Defender for IoT センサーによって取得された過去のデータを ServiceNow から消去する必要があります。

## <a name="architecture"></a>Architecture

### <a name="on-premises-management-console-architecture"></a>オンプレミスの管理コンソールのアーキテクチャ

オンプレミスの管理コンソールは、ServiceNow に送信されるすべてのデバイスとアラート情報の統合ソースを提供します。

オンプレミスの管理コンソールを設定して、ServiceNow の 1 つのインスタンスと通信することができます。 オンプレミスの管理コンソールは、REST API を使用して、センサー データを Defender for IoT アプリケーションにプッシュします。

オンプレミスの管理コンソールを使用するようにシステムを設定する場合は、センサーの ServiceNow 同期、転送ルール、およびプロキシ構成を無効にします (設定されている場合)。

これらの構成は、オンプレミスの管理コンソール用に設定する必要があります。 構成手順については、この記事で説明します。

### <a name="sensor-architecture"></a>センサーのアーキテクチャ

センサーと ServiceNow の間の直接通信を含めるように環境を設定する場合は、センサーごとに ServiceNow 同期、転送ルール、プロキシ構成 (プロキシが必要な場合) を定義します。

ServiceNow との通信には、オンプレミスの管理コンソールを使用して統合を設定することをお勧めします。

## <a name="create-access-tokens-in-servicenow"></a>ServiceNow でアクセス トークンを作成する

この記事では、ServiceNow でアクセス トークンを作成する方法について説明します。 このトークンは、Defender for IoT と通信するために必要です。

アラート情報を ServiceNow に転送する Defender for IoT の転送ルールを作成するとき、およびデバイス属性を ServiceNow のテーブルにプッシュするよう Defender for IoT を構成するときに、**クライアント ID** と **クライアント シークレット** が必要になります。

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>ServiceNow と通信するように Defender for IoT を設定する

この記事では、ServiceNow と通信するように Defender for IoT を設定する方法について説明します。

### <a name="send-defender-for-iot-alert-information"></a>Defender for IoT のアラート情報を送信する

この記事では、アラート情報を ServiceNow テーブルにプッシュするように Defender for IoT を構成する方法について説明します。 送信されるアラート データの詳細については、「[アラートのレポート](#alert-reporting)」を参照してください。

Defenders for IoT のアラートは、セキュリティ インシデントとして ServiceNow に表示されます。

アラート情報を ServiceNow に送信するための、Defender for IoT の "*転送*" ルールを定義します。

ルールを定義するには、次の手順を実行します。

1. Defender for IoT の左側のペインで、 **[転送]** を選択します。  

1. :::image type="content" source="media/integration-servicenow/plus.png" alt-text="プラス アイコンのボタン":::を選択します。 アイコンをクリックします。 [転送ルールの作成] ダイアログ ボックスが表示されます。  

    :::image type="content" source="media/integration-servicenow/forwarding-rule.png" alt-text="転送ルールの作成":::

1. ルールの名前を追加します。

1. Defender for IoT によって転送ルールがトリガーされる基準を定義します。 転送ルールの条件を使用すると、Defender for IoT から ServiceNow に送信される情報の量を特定して管理することができます。 次のオプションを使用できます。

    - **重大度レベル:** これは、転送するインシデントの最小セキュリティ レベルです。 たとえば、 **[マイナー]** を選択すると、マイナー アラートおよびこの重大度レベルよりも上のすべてのアラートが転送されます。 レベルは、Defender for IoT によって事前に定義されています。

    - **プロトコル:** 検出されたトラフィックが特定のプロトコルを介して実行されていた場合にのみ転送ルールをトリガーします。 ドロップダウン リストから必要なプロトコルを選択するか、それらをすべて選択します。

    - **エンジン:** 必要なエンジンを選択するか、それらをすべて選択します。 選択したエンジンからのアラートが送信されます。

1. **[Report Alert Notifications]\( アラート通知のレポート\)** が選択されていることを確認します。

1. [操作] セクションで、 **[追加]** を選択してから **[ServiceNow]** を選択します。

    :::image type="content" source="media/integration-servicenow/select-servicenow.png" alt-text="ドロップダウン オプションから [ServiceNow] を選択します。":::

1. 次のように ServiceNow アクションのパラメーターを入力します。

    :::image type="content" source="media/integration-servicenow/parameters.png" alt-text="ServiceNow アクションのパラメーターを入力します":::

1. **[操作]** ペインで、次のパラメーターを設定します。

  | パラメーター | 説明 |
  |--|--|
  | Domain | ServiceNow サーバーの IP アドレスを入力します。 |
  | Username | ServiceNow サーバーのユーザー名を入力します。 |
  | Password | ServiceNow サーバーのパスワードを入力します。 |
  | クライアント ID | ServiceNow の **[Application Registries]\(アプリケーション レジストリ\)** ページで、Defender for IoT 用に受信したクライアント ID を入力します。 |
  | クライアント シークレット | Defender for IoT 用に ServiceNow の **[Application Registries]\(アプリケーション レジストリ\)** ページで作成したクライアント シークレット文字列を入力します。 |
  | レポートの種類 | **インシデント**:ServiceNow に示されたアラートの一覧を、各アラートのインシデント ID と簡単な説明と共に転送します。<br /><br />**Defender for IoT アプリケーション**:センサーの詳細、エンジン、ソース、宛先アドレスなどを含む、完全なアラート情報を転送します。 この情報は、ServiceNow アプリケーション上の Defender for IoT に転送されます。 |

1. **[SAVE]\(保存\)** を選択します。 Defenders for IoT のアラートは、インシデントとして ServiceNow に表示されます。

### <a name="send-defender-for-iot-device-attributes"></a>Defender for IoT デバイスの属性を送信する

この記事では、さまざまなデバイス属性を ServiceNow テーブルにプッシュするように Defender for IoT を構成する方法について説明します。 ServiceNow にプッシュされる情報の種類の詳細については、***インベントリ情報*** を参照してください。

属性を ServiceNow に送信するには、オンプレミスの管理コンソールを ServiceNow インスタンスにマップする必要があります。 こうすることで、Defender for IoT プラットフォームからインスタンスへ通信し、認証できるようになります。

ServiceNow インスタンスを追加するには、次の手順を実行します。

1. Defender for IoT のオンプレミスの管理コンソールにサインインします。

1. オンプレミスの管理コンソールの [統合] セクションで、 **[システム設定]** 、 **[ServiceNow]** の順に選択します。

      :::image type="content" source="media/integration-servicenow/servicenow.png" alt-text="[ServiceNow] ボタンを選択します。":::

1. [ServiceNow Sync]\(ServiceNow の同期\) ダイアログ ボックスで、次の同期パラメーターを入力します。

    :::image type="content" source="media/integration-servicenow/sync.png" alt-text="[ServiceNow Sync]\(ServiceNow の同期\) ダイアログ ボックス。":::

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

## <a name="verify-communication"></a>通信を検証する

"*最後の同期*" が実行された日付を確認して、オンプレミスの管理コンソールが ServiceNow インスタンスに接続されていることを確認します。

:::image type="content" source="media/integration-servicenow/sync-confirmation.png" alt-text="最後の同期を確認して通信が行われたことを確認します。":::

## <a name="set-up-the-integrations-using-the-https-proxy"></a>HTTPS プロキシを使用して統合を設定する

Defender for IoT と ServiceNow の統合を設定する際、オンプレミスの管理コンソールと ServiceNow サーバーは、ポート 443 を使用して通信します。 ServiceNow サーバーがプロキシの内側にある場合、既定のポートは使用できません。

Defender for IoT では、統合に使用する規定のポートの変更を有効にすることで、ServiceNow 統合で HTTPS プロキシがサポートされるようになります。

プロキシを構成するには、次の手順を実行します。

1. オンプレミスの管理コンソールでグローバル プロパティを編集します:  
    `sudo vim /var/cyberx/properties/global.properties`

2. 次のパラメーターを追加します:

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. 保存して終了します。

4. 次のコマンドを実行します。`sudo monit restart all`

構成が完了すると、構成されたプロキシを使用してすべての ServiceNow データが転送されます。

## <a name="download-the-defender-for-iot-application"></a>Defender for IoT アプリケーションをダウンロードする

この記事では、アプリケーションをダウンロードする方法について説明します。

Defender for IoT アプリケーションにアクセスするには、次の手順を実行します。

1. <https://store.servicenow.com/> に移動します

2. `Defender for IoT` または `CyberX IoT/ICS Management` を検索します。

   :::image type="content" source="media/integration-servicenow/search-results.png" alt-text="検索バーで CyberX を検索します。":::

3. アプリケーションを選択します。

   :::image type="content" source="media/integration-servicenow/cyberx-app.png" alt-text="一覧からアプリケーションを選択します。":::

4. **[Request App]\(アプリのリクエスト\)** を選択します。

   :::image type="content" source="media/integration-servicenow/sign-in.png" alt-text="資格情報を使用してアプリケーションにサインインします。":::

5. サインインしてアプリケーションをダウンロードします。

## <a name="view-defender-for-iot-detections-in-servicenow"></a>ServiceNow で Defender for IoT の検出を表示する

この記事では、ServiceNow に表示されるデバイス属性とアラート情報について説明します。

デバイス属性を表示するには、以下の手順を実行します。

1. ServiceNow にサインインします。

2. **[CyberX Platform]\(CyberX プラットフォーム\)** に移動します。

3. **[インベントリ]** または **[アラート]** に移動します。

   [:::image type="content" source="media/integration-servicenow/alert-list.png" alt-text="インベントリまたはアラート":::](media/integration-servicenow/alert-list.png#lightbox)

## <a name="inventory-information"></a>インベントリ情報

構成管理データベース (CMDB) は、Defender for IoT から ServiceNow に送信されるデータによって強化および補完されています。 Defender for IoT では、ServiceNow の CMDB 構成項目テーブルのデバイス属性を追加または更新することにより、ServiceNow ワークフローとビジネス ルールをトリガーできます。

利用可能な情報は以下のとおりです。

- 検出されたデバイス属性 (デバイスの MAC、OS、ベンダー、プロトコルなど)。

- ファームウェアの情報 (ファームウェアのバージョンやシリアル番号など)。

- 接続されているデバイスの情報 (ソースと宛先の間のトラフィックの方向など)。

### <a name="devices-attributes"></a>デバイスの属性

この記事では、ServiceNow にプッシュされるデバイス属性について説明します。

| Item | [説明] |
|--|--|
| アプライアンス | トラフィックを検出したセンサーの名前。 |
| id | Defender for IoT によって割り当てられたデバイス ID。 |
| 名前 | デバイス名。 |
| IP アドレス | デバイスの IP アドレスまたはアドレス。 |
| Type | デバイスの種類 (スイッチ、PLC、ヒストリアン、ドメイン コントローラーなど)。 |
| MAC アドレス | デバイスの MAC アドレスまたはアドレス。 |
| オペレーティング システム | デバイスのオペレーティング システム。 |
| ベンダー | デバイスのベンダー。 |
| プロトコル | デバイスによって生成されたトラフィックで検出されたプロトコル。 |
| 所有者 | デバイスの所有者の名前を入力します。 |
| 場所 | デバイスの物理的な場所を入力します。 |

このビューで、デバイスに接続されているデバイスを表示します。

接続されているデバイスを表示するには、次の手順を実行します。

1. デバイスを選択してから、そのデバイスに一覧表示されている **[アプライアンス]** を選択します。

    :::image type="content" source="media/integration-servicenow/appliance.png" alt-text="一覧から目的のアプライアンスを選択します。":::

1. **[デバイスの詳細]** ダイアログ ボックスで、 **[接続されているデバイス]** を選択します。

### <a name="firmware-details"></a>ファームウェアの詳細

この記事では、ServiceNow にプッシュされるデバイス ファームウェアの情報について説明します。

| Item | [説明] |
|--|--|
| アプライアンス | トラフィックを検出したセンサーの名前。 |
| Device | デバイス名。 |
| Address | デバイスの IP アドレス。 |
| モジュール アドレス | デバイス モデルとスロットの番号または ID。 |
| シリアル | デバイスのシリアル番号。 |
| モデル | デバイス モデルの番号。 |
| バージョン | ファームウェアのバージョン番号。 |
| 追加データ | ベンダーによって定義された、ファームウェアに関するその他のデータ (デバイスの種類など)。 |

### <a name="connection-details"></a>接続の詳細情報

この記事では、ServiceNow にプッシュされるデバイスの接続情報について説明します。

:::image type="content" source="media/integration-servicenow/connections.png" alt-text="デバイスの接続情報":::

| Item | [説明] |
|--|--|
| アプライアンス | トラフィックを検出したセンサーの名前。 |
| Direction | トラフィックの方向。 <br /> <br /> -  **[One Way]\(一方向\)** は、宛先がサーバーで、ソースがクライアントであることを示します。 <br /> <br /> -  **[Two Way]\(双方向\)** は、ソースと宛先の両方がサーバーであるか、またはクライアントが不明であることを示しています。 |
| ソース デバイス ID | 接続されているデバイスと通信したデバイスの IP アドレス。 |
| ソース デバイスの名前 | 接続されているデバイスと通信したデバイスの名前。 |
| 送信先デバイスの ID | 接続されているデバイスの IP アドレス。 |
| 送信先デバイスの名前 | 接続されているデバイスの名前。 |

## <a name="alert-reporting"></a>アラートのレポート

注意が必要なネットワーク トラフィックと動作の変化が Defenders for IoT エンジンによって検出されると、アラートがトリガーされます。 各エンジンで生成されるアラートの種類の詳細については、「[アラート エンジンについて](#about-alert-engines)」を参照してください。

この記事では、ServiceNow にプッシュされるデバイス アラートの情報について説明します。

| Item | 説明 |
|--|--|
| 作成済み | アラートが生成された日時。 |
| エンジン | イベントを検出したエンジン。 |
| タイトル | アラートのタイトル。 |
| [説明] | アラートの説明。 |
| プロトコル | トラフィックで検出されたプロトコル。 |
| 重大度 | Defender for IoT によって定義されたアラートの重要度。 |
| アプライアンス | トラフィックを検出したセンサーの名前。 |
| ソース名 | ソース名です。 |
| 送信元 IP アドレス| 送信元 IP アドレス |
| 送信先の名前 | 送信先の名前。 |
| 宛先 IP アドレス | 宛先 IP アドレス |
| 担当者 | チケットに割り当てられている個人の名前を入力します。 |

### <a name="updating-alert-information"></a>アラート情報を更新する

アラート情報をフォームに表示するには、作成された列のエントリを選択します。 アラートの詳細を更新して、レビューおよび処理を担当する個人にアラートを割り当てることができます。

[:::image type="content" source="media/integration-servicenow/alert.png" alt-text="アラートの情報を表示する。":::](media/integration-servicenow/alert.png#lightbox)

### <a name="about-alert-engines"></a>アラート エンジンについて

この記事では、各エンジンによってトリガーされるアラートの種類について説明します。

| アラートの種類 | [説明] |
|--|--|
| ポリシー違反アラート | 以前に学習したトラフィックからの逸脱がポリシー違反エンジンによって検出されたときにトリガーされます。 例: <br /><br />- 新しいデバイスが検出された。 <br /><br />- デバイス上に新しい構成が検出された。 <br /><br />- プログラミング デバイスとして定義されていないデバイスによってプログラミングの変更が行われている。 <br /><br />- ファームウェアのバージョンが変更された。 |
| プロトコル違反アラート | プロトコル仕様に準拠していないパケット構造またはフィールド値がプロトコル違反エンジンによって検出されたときにトリガーされます。 |
| 操作のアラート | ネットワークの運用上のインシデントまたはデバイスの誤動作が運用エンジンによって検出されたときにトリガーされます。 たとえば、ネットワーク デバイスが Stop PLC コマンドを使用して停止された場合、またはセンサーのインターフェイスによってトラフィックの監視が停止された場合などです。 |
| マルウェアのアラート | 悪意のあるネットワーク アクティビティ (Conficker などの既知の攻撃) がマルウェア対策エンジンによって検出されたときにトリガーされます。 |
| 異常のアラート | 異常エンジンによって逸脱が検出されたときにトリガーされます。 たとえば、ネットワーク スキャンを実行しているデバイスがスキャン デバイスとして定義されていない場合などです。 |

## <a name="next-steps"></a>次の手順

[アラート情報を転送する](how-to-forward-alert-information-to-partners.md)方法を学習します。
