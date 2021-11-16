---
title: 個々のセンサーの管理
description: アクティブ化ファイルの管理、証明書、バックアップの実行、スタンドアロン センサーの更新など、個々のセンサーを管理する方法について説明します。
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: dbbf08f1e139a265b947cc99c7287765d6a6d03a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339955"
---
# <a name="manage-individual-sensors"></a>個々のセンサーの管理

この記事では、個々のセンサーを管理する方法について説明します。 具体的には、アクティブ化ファイルの管理、バックアップの実行、スタンドアロン センサーの更新などのタスクがあります。

また、オンプレミス管理コンソールから、複数のセンサーを同時に管理できる特定のセンサー管理タスクを実行することもできます。

センサーのオンボードと登録には Azure portal を使用します。

## <a name="manage-sensor-activation-files"></a>センサーのアクティブ化ファイルの管理

お使いのセンサーは、Azure portal から Microsoft Defender for IoT でオンボードされました。 各センサーは、ローカル接続のセンサーまたはクラウド接続のセンサーとしてオンボードされました。

デプロイする各センサーには一意のアクティブ化ファイルがアップロードされます。 新しいファイルを使用するタイミングと方法について詳しくは、「[新しいアクティブ化ファイルをアップロードする](#upload-new-activation-files)」をご覧ください。 ファイルをアップロードできない場合は、「[アクティブ化ファイルのアップロードのトラブルシューティング」](#troubleshoot-activation-file-upload)をご覧ください。

### <a name="about-activation-files-for-locally-connected-sensors"></a>ローカル接続のセンサー用のアクティブ化ファイルについて

ローカル接続のセンサーは、Azure サブスクリプションに関連付けられます。 ローカル接続のセンサー用のアクティブ化ファイルには、有効期限が含まれています。 この日付の 1 か月前になると、センサーのコンソールの一番上に警告メッセージが表示されます。 アクティブ化ファイルの更新が済むまで、警告は表示されたままになります。

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="システム設定のスクリーンショット。":::

アクティブ化ファイルの有効期限が切れている場合でも、引き続き Defender for IoT の機能を使用できます。 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>クラウド接続のセンサー用のアクティブ化ファイルについて

クラウド接続のセンサーは、Defender for IoT ハブに関連付けられます。 これらのセンサーには、アクティブ化ファイルの期間による制限はありません。 クラウド接続のセンサー用のアクティブ化ファイルは、Defender for IoT ハブとの接続を確保する目的で使用されます。

### <a name="upload-new-activation-files"></a>新しいアクティブ化ファイルをアップロードする

次の場合に、オンボードされたセンサー用の新しいアクティブ化ファイルのアップロードが必要になることがあります。

- ローカル接続のセンサーでアクティブ化ファイルの有効期限が切れる。 

- 別のセンサー管理モードで作業する。 

- クラウド接続のセンサーに新しい Defender for IoT ハブを割り当てる必要がある。

**新しいアクティブ化ファイルを追加するには:**

1. **[センサー管理]** ページにアクセスします。

2. 新しいアクティブ化ファイルをアップロードするセンサーを選択します。

3. それを削除します。

4. 新しいモードの **[オンボード]** ページから、または新しい Defender for IoT ハブを使用して、センサーをもう一度オンボードします。

5. **[アクティブ化ファイルのダウンロード]** ページからアクティブ化ファイルをダウンロードします。

6. ファイルを保存します。

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="Defender for IoT ハブからアクティブ化ファイルをダウンロードします。":::

7. Defender for IoT センサー コンソールにサインインします。

8. センサー コンソールで、 **[システム設定]**  >  **[再アクティブ化]** の順に選択します。

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="[システム設定] 画面の [再アクティブ化] の選択。":::

9. **[アップロード]** を選択し、保存したファイルを選択します。

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="保存したファイルをアップロードします。":::

10. **[アクティブ化]** を選びます。

### <a name="troubleshoot-activation-file-upload"></a>アクティブ化ファイルのアップロードをトラブルシューティングする

アクティブ化ファイルをアップロードできなかった場合は、エラー メッセージが表示されます。 次のイベントが発生している可能性があります。

- **ローカル接続のセンサーの場合**: アクティブ化ファイルが有効ではありません。 ファイルが有効でない場合は、[Azure portal で Defender for IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) に移動します。 **[センサー管理]** ページで、無効なファイルを含むセンサーを選択し、新しいアクティブ化ファイルをダウンロードします。

- **クラウド接続のセンサーの場合**: センサーがインターネットに接続できません。 センサーのネットワーク構成を確認します。 センサーがインターネットにアクセスするために Web プロキシ経由で接続する必要がある場合は、 **[センサーのネットワーク構成]** 画面でプロキシ サーバーが正しく構成されていることを確認します。 ファイアウォールやプロキシで \*.azure-devices.net:443 が許可されていることを確認します。 ワイルドカードがサポートされていない場合、または細かく制御する必要がある場合は、特定の Defender for IoT ハブの FQDN をお使いのファイアウォールやプロキシで開く必要があります。 詳しくは、「[リファレンス - IoT Hub エンドポイント](../../iot-hub/iot-hub-devguide-endpoints.md)」をご覧ください。  

- **クラウド接続のセンサーの場合**: アクティブ化ファイルは有効ですが、Defender for IoT で拒否されました。 この問題を解決できない場合は、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)の **[サイトとセンサー]** ページから別のアクティブ化をダウンロードできます。 それでもうまくいかない場合は、Microsoft サポートにお問い合わせください。

## <a name="manage-certificates"></a>証明書の管理

センサーのインストール後に、ローカルの自己署名証明書が生成され、センサーの Web アプリケーションにアクセスするために使用されます。 管理者ユーザーは、センサーに初めてログインするときに、SSL/TLS 証明書を指定するように求められます。 

センサー管理者には、最初のログイン後にアップロードされた証明書の更新が求められることがあります。 これは証明書の有効期限が切れた場合などに発生することがあります。

**証明書を更新するには:**

1. **[システム設定]** を選択します。

1. **[SSL/TLS 証明書]** を選択します。

    :::image type="content" source="media/how-to-manage-individual-sensors/certificate-upload.png" alt-text="証明書のアップロード":::

1. [SSL/TLS 証明書] ダイアログ ボックスで、既存の証明書を削除し、新しい証明書を追加します。

    - 証明書名を追加します。
    - CRT ファイルとキー ファイルをアップロードします。
    - 必要に応じて PEM ファイルをアップロードします。

アップロードに失敗した場合、セキュリティ管理者または IT 管理者に問い合わせるか、「[証明書について](how-to-deploy-certificates.md)」の情報をご確認ください。

**証明書の検証設定を変更するには:**

1. **[証明書の検証を有効にする]** トグルを有効または無効にします。 オプションが有効になっていて、検証に失敗した場合は、関連コンポーネント間の通信が停止し、検証エラーがコンソールに表示されます。 無効にした場合、証明書検証は実行されません。詳細については、「[証明書の検証について](how-to-deploy-certificates.md#about-certificate-validation)」を参照してください。

1. **[保存]** を選択します。

初回の証明書のアップロードの詳細については、「[初回サインインとアクティブ化のチェックリスト](how-to-activate-and-set-up-your-sensor.md#first-time-sign-in-and-activation-checklist)」を参照してください

## <a name="connect-a-sensor-to-the-management-console"></a>管理コンソールへのセンサーの接続

ここでは、センサーとオンプレミス管理コンソール間の接続を確認する方法について説明します。 この操作は、エアギャップ ネットワークで作業しているときに、センサーから管理コンソールにデバイスやアラートの情報を送信する場合に必要となります。 この接続により、管理コンソールでセンサーにシステム設定をプッシュしたり、センサーに対して他の管理タスクを実行したりすることもできます。

接続するには:

1. オンプレミス管理コンソールにサインインします。

2. **[システム設定]** を選択します。

3. **[センサーのセットアップ – 接続文字列]** セクションで、自動生成された接続文字列をコピーします。

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="この画面から接続文字列をコピーします。"::: 

4. センサー コンソールにサインインします。

5. 左側のペインで、 **[システム設定]** を選択します。

6. **[管理コンソール接続]** を選択します。

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="[管理コンソール接続] ダイアログ ボックスのスクリーンショット。":::

7. 接続文字列を **[接続文字列]** ボックスに貼り付けて、 **[接続]** を選択します。

8. オンプレミス管理コンソールの **[サイトの管理]** ウィンドウで、センサーをゾーンに割り当てます。

## <a name="change-the-name-of-a-sensor"></a>センサーの名前の変更

センサー コンソールの名前を変更できます。 コンソールの Web ブラウザー、各種コンソール ウィンドウ、トラブルシューティング ログに新しい名前が表示されます。

センサー名を変更するプロセスは、ローカル接続のセンサーとクラウド接続のセンサーで異なります。 既定の名前は **sensor** です。

### <a name="change-the-name-of-a-locally-connected-sensor"></a>ローカル接続のセンサーの名前を変更する

名前を変更するには:

1. コンソールの左ペインの下部にある現在のセンサー ラベルを選択します。

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="センサー ラベルを示すスクリーンショット。":::

1. **[センサー名の編集]** ダイアログ ボックスで、名前を入力します。

1. **[保存]** を選択します。 新しい名前が適用されます。

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>クラウド接続のセンサーの名前を変更する

センサーをクラウド接続のセンサーとして登録した場合は、登録中に割り当てられた名前によってセンサー名が定義されています。 その名前は、初めてサインインしたときにアップロードしたアクティブ化ファイルに含まれています。 センサーの名前を変更するには、新しいアクティブ化ファイルをアップロードする必要があります。

名前を変更するには:

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) で [サイトとセンサー] ページに移動します。

1. [サイトとセンサー] ページからセンサーを削除します。

1. [はじめに] ページで **[センサーのオンボード]** を選択して、新しい名前で登録します。

1. 新しいアクティブ化ファイルをダウンロードします。

1. Defender for IoT センサー コンソールにサインインします。

1. センサー コンソールで、 **[システム設定]** を選択し、 **[再アクティブ化]** を選択します。

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="アクティブ化ファイルをアップロードしてセンサーを再アクティブ化します。":::

1. **[アップロード]** を選択し、保存したファイルを選択します。

1. **[アクティブ化]** を選びます。

## <a name="update-the-sensor-network-configuration"></a>センサーのネットワーク構成の更新

センサーのネットワーク構成は、センサーのインストール中に定義されています。 構成パラメーターを変更できます。 また、プロキシ構成を設定することもできます。

新しい IP アドレスを作成する場合は、もう一度サインインすることが必要になる場合があります。

構成を変更するには:

1. サイド メニューで、 **[システム設定]** を選択します。

2. **[システム設定]** ウィンドウで、 **[ネットワーク]** を選択します。

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="ネットワーク設定を構成します。":::

3. パラメーターを設定します。

    | パラメーター | [説明] |
    |--|--|
    | IP アドレス | センサーの IP アドレス |
    | サブネット マスク | マスク アドレス |
    | デフォルト ゲートウェイ | 既定のゲートウェイ アドレス |
    | DNS | DNS サーバー アドレス |
    | hostname | センサーのホスト名 |
    | プロキシ | プロキシ ホストとポート名 |

4. **[保存]** を選択します。

## <a name="synchronize-time-zones-on-the-sensor"></a>センサーでのタイム ゾーンの同期

すべてのユーザーに同じ時刻とリージョンが表示されるように、センサーの時刻とリージョンを構成できます。

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="時刻とリージョンを構成します。":::

| パラメーター | [説明] |
|--|--|
| タイム ゾーン | タイム ゾーン定義の対象:<br />- アラート<br />- 傾向および統計ウィジェット<br />- データ マイニング レポート<br />   \- リスク評価レポート<br />- 攻撃ベクトル |
| 日付の形式 | 次の形式オプションのいずれかを選択します。<br />- dd/MM/yyyy HH:mm:ss<br />- MM/dd/yyyy HH:mm:ss<br />- yyyy/MM/dd HH:mm:ss |
| 日付と時刻 | 選択した形式で現在の日付とローカル時刻を表示します。<br />たとえば、実際の場所が米国のニューヨークであっても、タイム ゾーンがヨーロッパのベルリンに設定されている場合、時刻はベルリンのローカル時刻に従って表示されます。 |

センサーの時刻を構成するには:

1. サイド メニューで、 **[システム設定]** を選択します。

2. **[システム設定]** ウィンドウで、 **[時刻とリージョン]** を選択します。

3. パラメーターを設定し、 **[保存]** を選択します。

## <a name="set-up-backup-and-restore-files"></a>バックアップおよび復元ファイルの設定

システムのバックアップは、毎日午前 3 時に自動的に実行されます。 データはセンサーの別のディスクに保存されます。 既定の場所は `/var/cyberx/backups` です。

このファイルを内部ネットワークに自動的に転送できます。

> [!NOTE]
> - バックアップと復元手順は、同じバージョン間でのみ実行できます。
> - 一部のアーキテクチャでは、バックアップが無効になっています。 これは `/var/cyberx/properties/backup.properties` ファイルで有効にすることができます。

オンプレミス管理コンソールを使用してセンサーを制御すると、センサーのバックアップ スケジュールを使ってこれらのバックアップを収集し、管理コンソールまたは外部のバックアップ サーバーに保存することができます。

**バックアップされるもの**: 構成とデータ。

**バックアップされないもの**: PCAP ファイルとログ。 PCAP とログのバックアップと復元は手動で行うことができます。

センサーのバックアップ ファイルには、次の形式で自動的に名前が付けられます: `<sensor name>-backup-version-<version>-<date>.tar`。 たとえば `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar` です。

バックアップを構成するには:

- 管理者アカウントにサインインし、「`$ sudo cyberx-xsense-system-backup`」と入力します。

最新のバックアップ ファイルを復元するには:

- 管理者アカウントにサインインし、「`$ sudo cyberx-xsense-system-restore`」と入力します。

外部の SMB サーバーにバックアップを保存するには:

1. 外部の SMB サーバーに共有フォルダーを作成します。

    SMB サーバーへのアクセスに必要なフォルダー パス、ユーザー名、パスワードを取得します。

2. センサーで、バックアップ用のディレクトリを作成します。

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. `fstab`を編集します:

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. SMB サーバー用に共有する資格情報を編集および作成します。

    `sudo nano /etc/samba/user` 

5. 次の項目を追加します。

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. ディレクトリをマウントします。

    `sudo mount -a`

7. Defender for IoT センサー上の共有フォルダーに、バックアップ ディレクトリを構成します。  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>センサーを復元する

バックアップは、センサー コンソールからと、CLI を使用して復元できます。

**コンソールから復元するには:**

- センサーの **[システム設定]** ウィンドウから **[イメージの復元]** を選択します。

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="ボタンを選択してイメージを復元します。":::

コンソールに復元エラーが表示されます。

**CLI を使用して復元するには:**

- 管理者アカウントにサインインし、「`$ sudo cyberx-management-system-restore`」と入力します。

## <a name="update-a-standalone-sensor-version"></a>スタンドアロン センサーのバージョンの更新

次の手順では、センサー コンソールを使用してスタンドアロン センサーを更新する方法について説明します。 更新プロセスには約 30 分かかります。

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。

2. Defender for IoT にアクセスします。

3. **[更新プログラム]** ページに移動します。

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="Defender for IoT の [更新プログラム] ページのスクリーンショット。":::

4. **[センサー]** セクションの **[ダウンロード]** を選択し、ファイルを保存します。

5. センサー コンソールのサイドバーで、 **[システム設定]** を選択します。

6. **[バージョンのアップグレード]** ペインで、 **[アップグレード]** を選択します。

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="[更新プログラム] ペインのスクリーンショット。":::

7. Defender for IoT の **[更新プログラム]** ページからダウンロードしたファイルを選択します。

8. 更新プロセスが開始され、その間にシステムが 2 回再起動されます。 最初の再起動後 (更新プロセスが完了する前) に、サインイン ウィンドウが表示された状態でシステムが開きます。 サインインすると、サイドバーの左下にアップグレード バージョンが表示されます。

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="サインイン後に表示されるアップグレード バージョンのスクリーンショット。":::

## <a name="forward-sensor-failure-alerts"></a>センサーの障害アラートの転送

以下に関する詳細情報を提供するために、サード パーティにアラートを転送できます。

- 切断されたセンサー

- リモート バックアップの失敗

この情報は、システム通知の転送ルールを作成するときに送信されます。

> [!NOTE]
> 管理者はシステム通知を送信できます。

通知を送信するには:

1. オンプレミス管理コンソールにサインインします。
1. サイド メニューから **[転送]** を選択します。
1. 転送ルールを作成します。
1. **[システム通知の報告]** を選択します。

転送ルールについて詳しくは、「[アラート情報を転送する](how-to-forward-alert-information-to-partners.md)」をご覧ください。

## <a name="adjust-system-properties"></a>システム プロパティの調整

システム プロパティにより、センサーのさまざまな操作と設定が制御されます。 それらを編集または変更すると、センサー コンソールの操作に悪影響を及ぼす可能性があります。

設定を変更する前に、[Microsoft サポート](https://support.microsoft.com/)にお問い合わせください。

システム プロパティにアクセスするには:

1. オンプレミス管理コンソールまたはセンサーにサインインします。

2. **[システム設定]** を選択します。

3. **[全般]** セクションの **[システム プロパティ]** を選択します。

## <a name="see-also"></a>関連項目

[脅威インテリジェンスの調査とパッケージ](how-to-work-with-threat-intelligence-packages.md)

[管理コンソールからセンサーを管理する](how-to-manage-sensors-from-the-on-premises-management-console.md)
