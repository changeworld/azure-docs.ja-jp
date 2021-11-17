---
title: アラート情報を転送する
description: 転送ルールを使用して、パートナーのシステムにアラート情報を送信することができます。
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: d863ab9de5e030ddd54ba1b4b40efc01e844f419
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278856"
---
# <a name="forward-alert-information"></a>アラート情報を転送する

Microsoft Defender for IoT と統合しているパートナー、Syslog サーバー、電子メール アドレスなどにアラート情報を送信することができます。 転送ルールを使用すると、セキュリティ利害関係者にアラート情報をすばやく配信できます。

転送ルールをトリガーする条件を定義します。 転送ルールの条件を使用すると、センサーから外部システムに送信される情報の量を特定して管理することができます。

Syslog やその他の既定の転送アクションが、お使いのシステムで配信されます。 Microsoft Sentinel、ServiceNow、Splunk などの取引先ベンダーと統合すると、より多くの転送アクションが利用可能になる可能性があります。

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-information-screen.png" alt-text="アラート情報。":::

Defender for IoT の管理者には、転送ルールを使用するためのアクセス許可があります。

## <a name="about-forwarded-alert-information"></a>転送されるアラート情報について

アラートにより、広範なセキュリティおよび操作イベントに関する情報が提供されます。 例:

- アラートの日付と時刻

- イベントを検出したエンジン

- アラートのタイトルと説明メッセージ

- アラートの重大度

- 送信元と送信先の名前と IP アドレス

- 検出された疑わしいトラフィック

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="アドレスのスキャンが検出されました。":::

転送ルールが作成されるときに、関連する情報がパートナーのシステムに送信されます。

## <a name="about-forwarding-rules-and-certificates"></a>転送ルールと証明書について

特定の転送ルールでは、センサーやオンプレミスの管理コンソールと統合ベンダーのサーバーとの間で、暗号化や証明書の検証を行うことができます。

このような場合、センサーやオンプレミスの管理コンソールがクライアントであり、セッションのイニシエーターとなります。  証明書は通常、サーバーから受信されます。そうでなければ、統合をセットアップするための特定の証明書が提供される非対称暗号化を使用します。

ユーザーの Defender for IoT システムは、証明書を検証するか、証明書の検証を無視するように設定されています。  検証の有効化と無効化に関する情報については、[証明書の検証について](how-to-deploy-certificates.md#about-certificate-validation)のセクションを参照してください。

検証が有効になっていて、証明書を検証できない場合、Defender for IoT とサーバー間の通信は停止されます。  センサーには、検証エラーを示すエラー メッセージが表示されます。  検証が無効で、証明書が有効でない場合、通信はそのまま実行されます。

次の転送規則では、暗号化と証明書の検証が許可されます。
- Syslog CEF
- Microsoft Sentinel
- QRadar

## <a name="create-forwarding-rules"></a>転送ルールの作成

**センサーの新しい転送ルールを作成するには**:

1. センサーにサインインします。

1. サイド メニューの **[転送]** を選択します。

1. **[転送ルールの作成]** を選択し ます。

   :::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule-screen.png" alt-text="転送ルール作成アイコン。":::

1. 転送ルールの名前を入力します。

1. 重大度レベルを選択します。

   重大度レベルという観点では、これは転送する最小のインシデントです。 たとえば、 **[マイナー]** を選択すると、マイナー アラートおよびこの重大度レベルよりも上のすべてのアラートが転送されます。 レベルは事前定義されています。

1. 適用するプロトコルを選択します。

   検出されたトラフィックが特定のプロトコルを介して実行されていた場合にのみ転送ルールをトリガーします。 ドロップダウン リストから必要なプロトコルを選択するか、それらをすべて選択します。

1. ルールを適用するエンジンを選択します。

   必要なエンジンを選択するか、それらをすべて選択します。 選択したエンジンからのアラートが送信されます。 

1. 適用するアクションを選択し、選択したアクションに必要なパラメーターに入力します。

   転送ルール アクションでは、アラート情報を取引先ベンダーまたはサーバーに転送するようセンサーに指示します。 転送ルールごとに複数のアクションを作成できます。

1. 必要に応じて、別のアクションを追加します。

1. **[送信]** を選択します。

### <a name="email-address-action"></a>電子メール アドレスのアクション

アラート情報を含むメールを送信します。 ルールごとに電子メール アドレスを 1 つ入力できます。

転送ルール用の電子メールを定義するには:

1. 1 つの電子メール アドレスを入力します。 複数の電子メールを追加する必要がある場合、電子メール アドレスごとに別のアクションを作成する必要があります。

    :::image type="content" source="media/how-to-forward-alert-information-to-partners/forward-email.png" alt-text="アラート転送画面のスクリーンショット。電子メール アドレスにアラートを転送します。":::

1. SIEM でのアラート検出用のタイム スタンプのタイム ゾーンを入力します。

1. **[Submit]\(送信\)** をクリックします。

### <a name="syslog-server-actions"></a>Syslog サーバーのアクション

次の形式がサポートされています。

- テキスト メッセージ

- CEF メッセージ

- LEEF メッセージ

- オブジェクト メッセージ

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-actions-rule.png" alt-text="転送ルール アクションを作成します。":::

次のパラメーターを入力します。

- Syslog ホスト名とポート。

- プロトコル (TCP と UDP)。

- SIEM でのアラート検出用のタイム スタンプのタイム ゾーン。

- CEF サーバーの TLS 暗号化証明書ファイルとキー ファイル (省略可能)。

:::image type="content" source="media/how-to-work-with-alerts-sensor/configure-encryption.png" alt-text="転送ルール用の暗号化を構成します。":::

| Syslog テキスト メッセージの出力フィールド | [説明] |
|--|--|
| 日付と時刻 | Syslog サーバー マシンで情報が受信された日付と時刻。 |
| Priority | User.Alert |
| hostname | センサーの IP アドレス |
| Protocol | [TCP] または [UDP] |
| Message | Sensor:  センサー名。<br /> Alert:  アラートのタイトル。<br /> 型:  アラートの種類。 **[Protocol Violation]** 、 **[Policy Violation]** 、 **[Malware]** 、 **[Anomaly]** 、または **[Operational]** を指定できます。<br /> 重大度:  アラートの重大度。 **[Warning]** 、 **[Minor]** 、 **[Major]** 、または **[Critical]** を指定できます。<br /> ソース:送信元デバイスの名前。<br /> ソース IP: 送信元デバイスの IP アドレス。<br /> 変換先:送信先デバイスの名前。<br /> 宛先 IP:  送信先デバイスの IP アドレス。<br /> メッセージ: アラートのメッセージ。<br /> Alert group: アラートに関連付けられているアラート グループ。 |

| Syslog オブジェクトの出力 | [説明] |
|--|--|
| 日時 | Syslog サーバー マシンで情報が受信された日付と時刻。 |  
| Priority | User.Alert |
| hostname | センサーの IP |
| Message | Sensor name:  アプライアンスの名前。 <br /> Alert time:  アラートが検出された時刻。 Syslog サーバー マシンの時刻とは異なる場合があり、転送ルールのタイム ゾーン構成によって決まります。 <br /> Alert title:  アラートのタイトル。 <br /> Alert message: アラートのメッセージ。 <br /> Alert severity:  アラートの重大度: **[Warning]** 、 **[Minor]** 、 **[Major]** 、または **[Critical]** 。 <br /> Alert type: **[Protocol Violation]** 、 **[Policy Violation]** 、 **[Malware]** 、 **[Anomaly]** 、または **[Operational]** 。 <br /> プロトコル: アラートのプロトコル。  <br /> **Source_MAC**: 送信元デバイスの IP アドレス、名前、ベンダー、または OS。 <br /> Destination_MAC: 送信先デバイスの IP アドレス、名前、ベンダー、または OS。 データが見つからない場合、この値は **[N/A]** になります。 <br /> alert_group: アラートに関連付けられているアラート グループ。 |

| Syslog CEF の出力形式 | [説明] |
|--|--|
| 日付と時刻 | Syslog サーバー マシンで情報が受信された日付と時刻。 |
| Priority | User.Alert |
| hostname | センサーの IP アドレス |
| Message | CEF:0 <br />Microsoft Defender for IoT <br />Sensor name:  センサー アプライアンスの名前。 <br /><センサーのバージョン> <br />Alert title: アラートのタイトル。 <br />msg:  アラートのメッセージ。 <br />protocol: アラートのプロトコル。 <br />severity:  **[Warning]** 、 **[Minor]** 、 **[Major]** 、または **[Critical]** 。 <br />type:  **[Protocol Violation]** 、 **[Policy Violation]** 、 **[Malware]** 、 **[Anomaly]** 、または **[Operational]** 。 <br /> start:  アラートが検出された時刻。 <br />Syslog サーバー マシンの時刻とは異なる場合があり、転送ルールのタイム ゾーン構成によって決まります。 <br />src_ip: 送信元デバイスの IP アドレス。  <br />dst_ip: 送信先デバイスの IP アドレス。<br />cat: アラートに関連付けられているアラート グループ。  |

| Syslog LEEF の出力形式 | [説明] |
|--|--|
| 日付と時刻 | Syslog サーバー マシンで情報が受信された日付と時刻。 |  
| Priority | User.Alert |
| hostname | センサーの IP |
| Message | センサー名: Microsoft Defender for IoT アプライアンスの名前。 <br />LEEF:1.0 <br />Microsoft Defender for IoT <br />Sensor  <br /><センサーのバージョン> <br />Microsoft Defender for IoT アラート <br />title: アラートのタイトル。 <br />msg:  アラートのメッセージ。 <br />protocol:  アラートのプロトコル。<br />severity:  **[Warning]** 、 **[Minor]** 、 **[Major]** 、または **[Critical]** 。 <br />type:  アラートの種類: **[Protocol Violation]** 、 **[Policy Violation]** 、 **[Malware]** 、 **[Anomaly]** 、または **[Operational]** 。 <br />start: アラートの時刻。syslog サーバー マシンの時刻とは異なる場合があります。 (これはタイム ゾーンの構成によって決まります)。 <br />src_ip:  送信元デバイスの IP アドレス。<br />dst_ip:  送信先デバイスの IP アドレス。 <br />cat: アラートに関連付けられているアラート グループ。 |

すべての情報を入力したら、 **[送信]** を選択します。

### <a name="webhook-server-action"></a>Webhook サーバー アクション

webhook サーバーにアラート情報を送信します。 Webhook サーバーを使用すると、Defender for IoT によるアラート イベントにサブスクライブする統合を設定できます。 アラート イベントがトリガーされると、管理コンソールから webhook の構成済み URL に HTTP POST ペイロードが送信されます。 Webhook は、外部の SIEM システム、SOAR システム、インシデント管理システムなどを更新するために使用できます。

**Webhook アクションを定義するには、以下の手順を実行します。**

1. Webhook アクションを選択します。

   :::image type="content" source="media/how-to-work-with-alerts-sensor/webhook.png" alt-text="Webhook の転送規則を定義します。":::

1. **[URL]** フィールドにサーバー アドレスを入力します。

1. **[キー]** と **[値]** のフィールドで、キーと値の定義を使用して HTTP ヘッダーをカスタマイズします。 キーには、文字、数字、ダッシュ、およびアンダースコアのみを含めることができます。 値には、先頭または末尾にスペースを 1 つだけ含めることができます。

1. **[保存]** を選択します。

### <a name="webhook-extended"></a>Webhook 拡張

Webhook 拡張を使用して、エンドポイントに追加のデータを送信することができます。 拡張機能には、Webhook アラートのすべての情報が含まれ、レポートに次の情報が追加されます。

- sensorID
- sensorName
- zoneID
- zoneName
- siteID
- siteName
- sourceDeviceAddress
- destinationDeviceAddress
- remediationSteps
- handled
- additionalInformation

**Webhook 拡張アクションを定義するには**:

1. 管理コンソールで、左側のウィンドウから **[転送]** を選択します。

1. :::image type="icon" source="media/how-to-forward-alert-information-to-partners/add-icon.png" border="false"::: ボタンを選択して、転送ルールを追加します。

1. 転送アラートにわかりやすい名前を付けます。

1. 重要度を選択します。

1. **[追加]** を選択します。

1. 種類を選択するドロップダウン ウィンドウで、 **[Webhook 拡張]** を選択します。

   :::image type="content" source="media/how-to-forward-alert-information-to-partners/webhook-extended.png" alt-text="種類を選択するドロップダウン オプション メニューから [webhook 拡張] オプションを選択します。":::

1. [URL] フィールドにエンドポイント データ URL を追加します。

1. (省略可能) キーと値の定義を使用して HTTP ヘッダーをカスタマイズします。 :::image type="icon" source="media/how-to-forward-alert-information-to-partners/add-header.png" border="false"::: ボタンを選択してさらにヘッダーを追加します。

1. **[保存]** を選択します。

Webhook 拡張転送ルールを構成したら、管理コンソールの [転送] 画面からアラートをテストできます。

**Webhook 拡張転送ルールをテストするには**:

1. 管理コンソールで、左側のウィンドウから **[転送]** を選択します。

1. **[実行]** ボタンを選択し、アラートをテストします。

    :::image type="content" source="media/how-to-forward-alert-information-to-partners/run-button.png" alt-text="実行ボタンを選択して転送ルールをテストします。":::

転送ルールが機能しているかどうかは、成功通知が表示されるかで判断します。

:::image type="content" source="media/how-to-forward-alert-information-to-partners/success.png" alt-text="成功通知が表示された場合、アラートは正しく機能しています。":::

### <a name="netwitness-action"></a>NetWitness のアクション

NetWitness サーバーにアラート情報を送信します。

NetWitness の転送パラメーターを定義するには:

1. NetWitness の **ホスト名** と **ポート** の情報を入力します。

1. SIEM でのアラート検出用のタイム スタンプのタイム ゾーンを入力します。

   :::image type="content" source="media/how-to-work-with-alerts-sensor/add-timezone.png" alt-text="転送ルールにタイム ゾーンを追加します。":::

1. **[Submit]\(送信\)** をクリックします。

### <a name="integrated-vendor-actions"></a>統合されたベンダーのアクション

お使いのシステムをセキュリティ、デバイス管理、またはその他の業界ベンダーとすでに統合している方もいるでしょう。 こうした統合により、次のアクションが可能になります。

  - アラート情報の送信。

  - デバイス インベントリ情報の送信。

  - ベンダー側のファイアウォールとの通信。

統合を行うと、以前はサイロ化していたセキュリティ ソリューションをブリッジし、デバイスの可視性を向上させ、システム全体の応答を速めて、リスクをより迅速に軽減できます。

これらのアクションのセクションを使用して、統合されたベンダーとの通信に必要な資格情報やその他の情報を入力します。

統合用の転送ルールの設定について詳しくは、パートナーとの統合に関する関連記事をご覧ください。

## <a name="test-forwarding-rules"></a>転送ルールのテスト

転送ルールで定義されているセンサーとパートナー サーバー間の接続をテストします。

1. **[転送ルール]** ダイアログ ボックスでルールを選択します。

1. **[その他]** ボックスを選択します。

1. **[テスト メッセージの送信]** を選択します。

1. パートナーのシステムにアクセスして、センサーから送信された情報が受信されていることを確認します。

## <a name="edit-and-delete-forwarding-rules"></a>転送ルールの編集および削除 

**転送ルールを編集するには**:

- **[転送ルール]** 画面で、 **[More]\(その他\)** ドロップダウン メニューの **[編集]** を選択します。 必要な変更を行い、 **[送信]** を選択します。

**転送ルールを削除するには**:

- **[転送ルール]** 画面で、 **[More]\(その他\)** ドロップダウン メニューの **[削除]** を選択します。 **[警告]** ダイアログ ボックスで **[OK]** を選択します。

## <a name="forwarding-rules-and-alert-exclusion-rules"></a>転送ルールとアラート除外ルール

管理者が、アラート除外ルールを定義している可能性があります。 これらの規則は、管理者が各種パラメーターに基づいてアラート イベントを無視するようセンサーに指示して、アラートのトリガーをより細かく制御するのに役立ちます。 こうしたパラメーターには、デバイス アドレス、アラート名、特定のセンサーなどがあります。

これは、ご自身で定義した転送ルールが、管理者によって作成された除外ルールに基づいて無視される可能性があることを意味します。 除外ルールは、オンプレミス管理コンソールで定義されます。

## <a name="see-also"></a>関連項目

[アラート ワークフローの高速化](how-to-accelerate-alert-incident-response.md)
