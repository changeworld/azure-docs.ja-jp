---
title: オンプレミス管理コンソールをアクティブにしてセットアップする
description: 管理コンソールをアクティブにしてセットアップすると、センサーが Azure に登録され、オンプレミス管理コンソールに情報が送信されるようになります。また、オンプレミスの管理コンソールでは、接続されたセンサーに対する管理タスクを実行できます。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: d344b3d9ea54243d61cc6a1f3f0982eb5ab1d238
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538582"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>オンプレミス管理コンソールをアクティブにしてセットアップする 

オンプレミス管理コンソールをアクティブにしてセットアップすると、次のことが行われます。

- 接続されたセンサーを使用して監視しているネットワーク デバイスが、Azure アカウントに登録されます。

- センサーによって、オンプレミス管理コンソールに情報が送信されます。

- オンプレミス管理コンソールによって、接続されているセンサーに対して管理タスクが実行されます。

- SSL 証明書がインストールされます。

## <a name="sign-in-for-the-first-time"></a>初めてサインインする

管理コンソールにサインインするには、次のようにします。

- Web ブラウザーを開き、システムのインストール時にオンプレミス管理コンソール用に受け取った IP アドレスとパスワードを入力します。 パスワードを忘れた場合は、 **[パスワードの回復]** を選択し、[パスワードの回復](how-to-manage-the-on-premises-management-console.md#password-recovery)に関するページを参照してください。

## <a name="upload-an-activation-file"></a>アクティブ化ファイルをアップロードする

初回サインイン後、Azure Defender for IoT ポータルの **[価格]** ページからアクティブ化ファイルをダウンロードして、オンプレミス管理コンソールをアクティブにします。 このファイルには、オンボード プロセス中に定義された、集約されたコミット済みのデバイスが含まれています。 **[コミット済みのデバイス]** には、Defender for IoT によってサブスクリプションごとに監視されるデバイスの数が示されます。

アクティブ化ファイルをアップロードするには、次の手順を実行します。

1. Defender for IoT の **[価格]** ページに移動します。
1. **[管理コンソールのアクティブ化ファイルのダウンロード]** タブを選択します。アクティブ化ファイルがダウンロードされます。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="アクティブ化ファイルをダウンロードします。":::

1. 管理コンソールで **[システム設定]** を選択します。
1. **[アクティブ化]** を選択します。
1. **[ファイルの選択]** を選択し、保存したファイルを選択します。

最初にアクティブ化した後に、監視されているデバイスの数がオンボード中に定義されたコミット済みのデバイスの数を超えることがあります。 これは、たとえば、管理コンソールにより多くのセンサーを接続した場合に発生する可能性があります。 監視されているデバイスの数とコミット済みのデバイスの数が一致しない場合は、管理コンソールに警告が表示されます。 これが発生した場合は、新しいアクティブ化ファイルをアップロードする必要があります。

## <a name="set-up-a-certificate"></a>証明書の設定

管理コンソールのインストール後に、ローカルの自己署名証明書が生成され、コンソールにアクセスするために使用されます。 管理者が管理コンソールに初めてサインインすると、SSL/TLS 証明書を組み込むように求めるメッセージがそのユーザーに表示されます。 

次の 2 つのレベルのセキュリティを使用できます。

- CA 署名証明書をアップロードして、組織から要求された特定の証明書と暗号化の要件を満たします。
- 管理コンソールと接続されたセンサーの間の検証を許可します。 検証は、証明書失効リストと証明書の有効期限に対して評価されます。 *検証に失敗した場合は、管理コンソールとセンサー間の通信が停止し、検証エラーがコンソールに表示されます。* このオプションは、インストール後に既定で有効になります。  

コンソールでは、次の種類の証明書がサポートされています。

- プライベートおよびエンタープライズ キー インフラストラクチャ (プライベート PKI)

- 公開キー基盤 (パブリック PKI)

- アプライアンスでローカルに生成された証明書 (ローカルで自己署名) 

  > [!IMPORTANT]
  > 自己署名証明書は使用しないことをお勧めします。 この証明書はセキュリティで保護されていないため、テスト環境でのみ使用してください。 証明書の所有者を検証できないため、システムのセキュリティを維持できません。 運用ネットワークには、このオプションを使用しないでください。

証明書をアップロードするには、次の手順を実行します。

1. サインイン後に求められた場合は、証明書名を定義します。
1. CRT およびキー ファイルをアップロードします。
1. パスフレーズを入力し、必要に応じて PEM ファイルをアップロードします。

CA 署名証明書のアップロード後に、画面を更新する必要がある場合があります。

管理コンソールと接続されているセンサーの間の検証を無効にするには、次の手順を実行します。

1. **[次へ]** を選択します。
1. **[システム全体の検証を有効にする]** トグルをオフにします。

新しい証明書、サポートされている証明書ファイルのアップロード、および関連項目の詳細については、「[オンプレミス管理コンソールの管理](how-to-manage-the-on-premises-management-console.md)」を参照してください。

## <a name="connect-sensors-to-the-on-premises-management-console"></a>センサーをオンプレミス管理コンソールに接続する

センサーによってオンプレミス管理コンソールに情報が送信されること、およびオンプレミス管理コンソールでバックアップの実行、アラートの管理、センサーでのその他のアクティビティの実行が可能であることを確認する必要があります。 これを行うには、次の手順に従って、センサーとオンプレミス管理コンソールの間で最初の接続が確立されることを確認します。

Azure Defender for IoT センサーをオンプレミス管理コンソールに接続するには、次の 2 つのオプションを使用できます。

- センサー コンソールから接続する

- トンネリングを使用して接続する

接続後、これらのセンサーを使用してサイトをセットアップする必要があります。

### <a name="connect-sensors-from-the-sensor-console"></a>センサー コンソールからセンサーを接続する

センサー コンソールから特定のセンサーをオンプレミス管理コンソールに接続するには、次の手順を実行します。

1. センサー コンソールの左側のペインで、 **[システム設定]** を選択します。

2. **[管理への接続]** を選択します。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-not-connected.png" alt-text="接続されていないことを示す、オンプレミス管理コンソールの状態ウィンドウのスクリーンショット。":::

3. **[アドレス]** テキスト ボックスに、接続先のオンプレミス管理コンソールの IP アドレスを入力します。

4. **[接続]** を選択します。 状態が次のように変わります。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-connected.png" alt-text="接続されていることを示す、オンプレミス管理コンソールの状態ウィンドウのスクリーンショット。":::

### <a name="connect-sensors-by-using-tunneling"></a>トンネリングを使用してセンサーを接続する

組織のセンサーとオンプレミス管理コンソールの間で、セキュリティで保護されたトンネリング接続を有効にします。 このセットアップにより、組織のファイアウォールとのやり取りが回避されるため、攻撃面を減らすことができます。

トンネリングを使用すると、オンプレミス管理コンソールをその IP アドレスと 1 つのポート (つまり、9000) から任意のセンサーに接続することができます。

オンプレミス管理コンソールでトンネリングを設定するには、次のようにします。

- オンプレミス管理コンソールにサインインして、次のコマンドを実行します。

  ```bash
  cyberx-management-tunnel-enable
  service apache2 reload
  sudo cyberx-management-tunnel-add-xsense --xsenseuid <sensorIPAddress> --xsenseport 9000
  service apache2 reload
  ```

センサーでトンネリングを設定するには、次の手順を実行します。

1. センサー (network.properties) の TCP ポート 9000 を手動で開きます。 このポートが開いていない場合、オンプレミス管理コンソールからの接続がセンサーで拒否されます。

2. 各センサーにサインインして、次のコマンドを実行します。

   ```bash
   sudo cyberx-xsense-management-connect -ip <centralmanagerIPAddress>
   sudo cyberx-xsense-management-tunnel
   sudo vi /var/cyberx/properties/network.properties
   opened_tcp_incoming_ports=22,80,443,102,9000
   sudo cyberx-xsense-network-validation
   sudo /etc/network/if-up.d/iptables-recover
   sudo iptables -nvL
   ```

## <a name="set-up-a-site"></a>サイトをセットアップする

既定のエンタープライズ マップでは、複数のレベルの地理的な場所に従って、アセットの全体的なビューが提供されます。

組織の構造とユーザーのアクセス許可が複雑になっている場合は、アセットのビューが必要になることがあります。 このような場合、サイトのセットアップは、標準のサイトまたはゾーンの構造に加えて、グローバルな組織構造によって決定される場合があります。

この環境をサポートするには、組織の部署、リージョン、サイト、ゾーンに基づいたグローバルなビジネスのトポロジを作成する必要があります。 また、アクセス グループを使用して、これらのエンティティに関するユーザー アクセス許可を定義する必要があります。

アクセス グループを使用すると、ユーザーが Defender for IoT プラットフォームのアセットを管理および分析する場所をより良く制御できます。

### <a name="how-it-works"></a>しくみ

各サイトに対して、部署と地域を定義できます。 その後、ネットワーク内の論理エンティティであるゾーンを追加できます。 

各ゾーンに対して、少なくとも 1 つのセンサーを割り当てる必要があります。 5 レベルのモデルでは、組織の構造を反映した保護システムを提供するために必要な柔軟性と細分性が提供されます。

サイトは、任意のマップ ビューから直接編集できます。 マップ ビューからサイトを開くと、オープンされているアラートの数が各ゾーンの横に表示されます。

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="ベルリンのデータがオーバーレイされたオンプレミス管理コンソールのマップのスクリーンショット。":::

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="センサーと地域の関係を示す図。":::

サイトをセットアップするには、次の手順を実行します。

1. 組織の論理的な構造を反映するために新しい部署を追加します。

2. 組織のリージョンを反映するために新しいリージョンを追加します。

3. サイトを追加します。

4. サイトにゾーンを追加します。

5. センサーを接続します。

6. センサーをサイトのゾーンに割り当てます。

部署を追加するには、次の手順を実行します。

1. エンタープライズ ビューで、 **[すべてのサイト]**  >  **[部署の管理]** を選択します。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-business-unit-screen.png" alt-text="[部署の管理] ビューを示すスクリーンショット。":::

2. 新しい部署の名前を入力し、 **[追加]** を選択します。

新しいリージョンを追加するには、次の手順を実行します。

1. エンタープライズ ビューで、 **[すべてのリージョン]**  >  **[リージョンの管理]** を選択します。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-regions-screen.png" alt-text="[リージョンの管理] ビューを示すスクリーンショット。":::

2. 新しいリージョンの名前を入力し、 **[追加]** を選択します。

新しいサイトを追加するには、次の手順を実行します。

1. エンタープライズ ビューで、上部のバーにある :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false"::: を選択します。 カーソルがプラス記号 ( **+** ) として表示されます。

2. 新しいサイトの場所に **+** を合わせて、それを選択します。 **[新しいサイトの作成]** ダイアログ ボックスが表示されます。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="[新しいサイトの作成] ビューのスクリーンショット。":::

3. 新しいサイトの名前と物理アドレスを定義し、 **[保存]** を選択します。 新しいサイトがサイト マップに表示されます。

サイトを削除するには、次の手順を実行します。

1. **[サイトの管理]** ウィンドウで、サイト名が表示されているバーから :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: を選択し、 **[サイトの削除]** を選択します。 サイトの削除を確認する確認ボックスが表示されます。

2. 確認ボックスで **[はい]** を選択します。 確認ボックスが閉じて、 **[サイトの管理]** ウィンドウが表示されますが、削除したサイトは表示されません。

## <a name="create-enterprise-zones"></a>エンタープライズ ゾーンを作成する

ゾーンは論理エンティティであり、さまざまな特性に応じて、サイト内のアセットをグループに分割することができます。 たとえば、生産ライン、支所、サイト領域、またはアセットの種類のグループを作成できます。 組織に適した特性に基づいてゾーンを定義できます。

ゾーンは、サイト構成プロセスの一環として構成します。

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="[サイトの管理] のゾーン ビューのスクリーンショット。":::

次の表には、 **[サイトの管理]** ウィンドウのパラメーターが示されています。

| パラメーター | [説明] |
|--|--|
| 名前 | センサーの名前。 この名前はセンサーからのみ変更できます。 詳細については、Defender for IoT ユーザー ガイドを参照してください。 |
| IP | センサーの IP アドレス。 |
| Version | センサーのバージョン。 |
| 接続 | センサーの接続状態。 状態は、 **[接続済み]** または **[切断]** です。 |
| 前回のアップグレード | 前回のアップグレードの日付。 |
| [アップグレードの進行状況] | 進行状況バーには、次のようなアップグレード プロセスの状態が示されます。<br />- パッケージをアップロードしています<br />- インストールの準備をしています<br />- プロセスを停止しています<br />- データをバックアップしています<br />- スナップショットを作成しています<br />- 構成を更新しています<br />- 依存関係を更新しています<br />- ライブラリを更新しています<br />- データベースにパッチを適用しています<br />- プロセスを開始しています<br />- システムの正常性を検証しています<br />- 検証に成功しました<br />- 成功<br />- 失敗<br />- アップグレードを開始しました<br />- インストールを開始していますogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >アップグレードの詳細については、[Microsoft サポート](https://support.microsoft.com/)を参照してください。 |
| アセット | センサーによって監視される OT アセットの数。 |
| 警告 | そのセンサーでのアラートの数。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: | ゾーンにセンサーを割り当てできるようにします。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::| 切断されたセンサーをサイトから削除できるようにします。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/sensor-icon.png" border="false"::: | 現在ゾーンに接続されているセンサーの数を示します。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/ot-assets-icon.png" border="false"::: | 現在ゾーンに接続されている OT アセットの数を示します。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/number-of-alerts-icon.png" border="false"::: | このゾーンに割り当てられているセンサーによって送信されたアラートの数を示します。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: | ゾーンからセンサーを割り当て解除します。 |

ゾーンをサイトに追加するには、次の手順を実行します。

1. **[サイトの管理]** ウィンドウで、サイト名が表示されているバーから :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: を選択し、 **[ゾーンの追加]** を選択します。 **[新しいゾーンの作成]** ダイアログ ボックスが表示されます。

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-zone-screen.png" alt-text="[新しいゾーンの作成] ビューのスクリーンショット。":::

2. ゾーン名を入力します。

3. 新しいゾーンの説明を入力します。サイトをゾーンに分割するために使用した特性を明確に記述します。

4. **[SAVE]\(保存\)** を選択します。 新しいゾーンは、このゾーンが属しているサイトの **[サイトの管理]** ウィンドウに表示されます。

ゾーンを編集するには、次の手順を実行します。

1. **[サイトの管理]** ウィンドウで、ゾーン名が表示されているバーから :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: を選択し、 **[ゾーンの編集]** を選択します。 **[ゾーンの編集]** ダイアログ ボックスが表示されます。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="[ゾーンの編集] ダイアログ ボックスを示すスクリーンショット。":::

2. ゾーン パラメーターを編集し、 **[保存]** を選択します。

ゾーンを削除するには、次の手順を実行します。

1. **[サイトの管理]** ウィンドウで、ゾーン名が表示されているバーから :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: を選択し、 **[ゾーンの削除]** を選択します。

2. 確認ボックスで **[はい]** を選択します。

接続状態に従ってフィルター処理するには、次のようにします。

- 左上隅の **[接続]** の横にある :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: を選択し、次のオプションのいずれかを選択します。

  - **[すべて]** : このオンプレミス管理コンソールに報告しているすべてのセンサーが表示されます。

  - **接続済み**: 接続されているセンサーのみが表示されます。

  - **切断**: 切断されているセンサーのみが表示されます。

アップグレードの状態に従ってフィルター処理するには、次のようにします。

- 左上隅の **[アップグレードの状態]** の横にある :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: を選択し、次のオプションのいずれかを選択します。

  - **[すべて]** : このオンプレミス管理コンソールに報告しているすべてのセンサーが表示されます。

  - **有効**:アップグレード状態が有効なセンサーが表示されます。

  - **進行中**: アップグレード処理中のセンサーが表示されます。

  - **[失敗]** : アップグレード処理が失敗したセンサーが表示されます。

## <a name="assign-sensors-to-zones"></a>センサーをゾーンに割り当てる

各ゾーンに、ローカル トラフィックの分析とアラート処理を実行するセンサーを割り当てる必要があります。 オンプレミス管理コンソールに接続されているセンサーのみを割り当てることができます。

センサーを割り当てるには、次の手順を実行します。

1. **[サイトの管理]** を選択します。 未割り当てのセンサーが、ダイアログ ボックスの左上隅に表示されます。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassigned-sensors-view.png" alt-text="[未割り当てのセンサー] ビューのスクリーンショット。":::

2. **[接続]** の状態が [接続済み] であることを確認します。 接続されていない場合、接続の詳細については、「[センサーをオンプレミス管理コンソールに接続する](#connect-sensors-to-the-on-premises-management-console)」を参照してください。 

3. 割り当てるセンサーの :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: を選択します。

4. **[センサーの割り当て]** ダイアログ ボックスで、割り当てる部署、リージョン、サイト、ゾーンを選択します。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="[センサーの割り当て] ビューのスクリーンショット。":::

5. **[割り当て]** を選択します。

センサーの割り当てを解除して削除するには、次の手順を実行します。

1. オンプレミス管理コンソールからセンサーを切断します。 詳細については、「[センサーをオンプレミス管理コンソールに接続する](#connect-sensors-to-the-on-premises-management-console)」を参照してください。

2. **[サイトの管理]** ウィンドウで、センサーを選択して :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: を選択します。 しばらくすると、未割り当てのセンサーの一覧にそのセンサーが表示されます。

3. 未割り当てのセンサーをサイトから削除するには、未割り当てのセンサーの一覧からセンサーを選択して、:::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false"::: を選択します。

## <a name="see-also"></a>関連項目

[センサーとオンプレミスの管理コンソールのトラブルシューティング](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
