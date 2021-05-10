---
title: オンプレミス管理コンソールをアクティブにしてセットアップする
description: 管理コンソールをアクティブにすると、センサーが Azure に登録され、オンプレミス管理コンソールに情報が送信されるようになります。また、オンプレミス管理コンソールでは、接続されたセンサーに対する管理タスクを実行できます。
ms.date: 4/6/2021
ms.topic: how-to
ms.openlocfilehash: db0d2a84feeb5bf52932842badda8c126994c05d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492156"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>オンプレミス管理コンソールをアクティブにしてセットアップする 

オンプレミス管理コンソールをアクティブにしてセットアップすると、次のことが行われます。

- 接続されたセンサーを使用して監視しているネットワーク デバイスが、Azure アカウントに登録されます。

- センサーによって、オンプレミス管理コンソールに情報が送信されます。

- オンプレミス管理コンソールによって、接続されているセンサーに対して管理タスクが実行されます。

- SSL 証明書がインストールされます。

## <a name="sign-in-for-the-first-time"></a>初めてサインインする

管理コンソールにサインインするには、次のようにします。

1. システムのインストール時にオンプレミス管理コンソール用に受け取った IP アドレスへ移動します。
 
1. システムのインストール時にオンプレミス管理コンソール用に受け取ったユーザー名とパスワードを入力します。 


パスワードを忘れた場合は、 **[Recover Password]\(パスワードを回復する\)** オプションを選択してください。パスワードを回復する手順については、「[パスワードの回復](how-to-manage-the-on-premises-management-console.md#password-recovery)」を参照してください。

## <a name="activate-the-on-premises-management-console"></a>オンプレミスの管理コンソールをアクティブ化する

初めてサインインした後に、アクティブ化ファイルを取得してアップロードし、オンプレミス管理コンソールをアクティブ化する必要があります。 

オンプレミスの管理コンソールをアクティブ化するには、次のようにします。

1. オンプレミス管理コンソールにサインインします。

1. 画面の上部にあるアラート通知で、 **[対応策を取る]** リンクを選択します。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/take-action.png" alt-text="画面の上部にあるアラートから [対応策を取る] リンクを選択する。":::

1. [アクティブ化] ポップアップ画面で、**Azure portal** へのリンクを選択します。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/azure-portal.png" alt-text="ポップアップ メッセージから Azure portal リンクを選択する。":::
 
1. オンプレミスの管理コンソールを関連付けるサブスクリプションを選択し、 **[Download on-premises management console activation file]\(オンプレミスの管理コンソールのアクティブ化ファイルをダウンロード\)** ボタンを選択します。 アクティブ化ファイルがダウンロードされます。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="アクティブ化ファイルをダウンロードします。":::

   サブスクリプションをまだオンボードしていない場合は、[サブスクリプションをオンボード](how-to-manage-subscriptions.md#onboard-a-subscription)します。

1. **[アクティベーション]** ポップアップ画面に戻り、 **[Choose File]\(ファイルの選択\)** を選択します。

1. ダウンロードしたファイルを選択します。

最初にアクティブ化した後に、監視するデバイスの数がオンボード中に定義したコミット済みデバイスの数を超えることがあります。 これは、管理コンソールに余分なセンサーを接続した場合に発生します。 監視するデバイスの数とコミット済みデバイスの数が一致しない場合は、管理コンソールに警告が表示されます。 これが発生した場合は、新しいアクティブ化ファイルをアップロードしてください。

## <a name="set-up-a-certificate"></a>証明書の設定

管理コンソールをインストールすると、ローカルの自己署名証明書が生成されます。 この証明書は、コンソールにアクセスするために使用されます。 管理者が管理コンソールに初めてサインインすると、SSL/TLS 証明書を組み込むように求めるメッセージがそのユーザーに表示されます。 

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

CA 署名証明書のアップロード後に、画面を最新の情報に更新する必要がある場合があります。

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

### <a name="connect-sensors-to-the-on-premises-management-console-from-the-sensor-console"></a>センサー コンソールからセンサーをオンプレミス管理コンソールに接続する

次の方法でセンサー コンソールからセンサーをオンプレミス管理コンソールに接続することができます。

1. オンプレミス管理コンソールで、 **[システム設定]** を選択します。

1. **[Copy Connection String]\(接続文字列のコピー\)** をコピーします。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/connection-string.png" alt-text="センサー用の接続文字列をコピーする。":::

1. センサーで **[システム設定]** に移動し、 **[Connection to Management Console]\(管理コンソールへの接続\)** を選択します。:::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/connection-to-management-console.png" border="false":::

1. オンプレミスの管理コンソールからコピーした接続文字列を **[Connection string]\(接続文字列\)** フィールドに貼り付けます。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/paste-connection-string.png" alt-text="コピーした接続文字列を、接続文字列フィールド貼り付ける。":::

1. **[接続]** を選択します。

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

既定のエンタープライズ マップでは、複数のレベルの地理的な場所に従って、デバイスの全体的なビューが提供されます。

組織の構造とユーザーのアクセス許可が複雑になっている場合は、デバイスのビューが必要になることがあります。 このような場合、サイトのセットアップは、標準のサイトまたはゾーンの構造に加えて、グローバルな組織構造によって決定される場合があります。

この環境をサポートするには、組織の部署、リージョン、サイト、ゾーンに基づいたグローバルなビジネスのトポロジを作成する必要があります。 また、アクセス グループを使用して、これらのエンティティに関するユーザー アクセス許可を定義する必要があります。

アクセス グループを使用すると、ユーザーが Defender for IoT プラットフォームのデバイスを管理および分析する場所をより適切に制御できます。

### <a name="how-it-works"></a>しくみ

組織内の各サイトに対して、部署とリージョンを定義できます。 その後、ネットワーク内に存在する論理エンティティであるゾーンを追加できます。 

各ゾーンに、少なくとも 1 つのセンサーを割り当てる必要があります。 5 レベルのモデルでは、組織の構造を反映した保護システムを提供するために必要な柔軟性と細分性が提供されます。

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="センサーと地域の関係を示す図。":::

[Enterprise View]\(エンタープライズ ビュー\) を使用すると、サイトを直接編集できます。 [Enterprise View]\(エンタープライズ ビュー\) でサイトを選択すると、開いているアラートの数が各ゾーンの横に表示されます。

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="ベルリンのデータがオーバーレイされたオンプレミス管理コンソールのマップのスクリーンショット。":::

サイトをセットアップするには、次の手順を実行します。

1. 組織の論理的な構造を反映するために新しい部署を追加します。

   1. エンタープライズ ビューで、 **[すべてのサイト]**  >  **[部署の管理]** を選択します。

      :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/manage-business-unit.png" alt-text="エンタープライズ ビュー画面の [All Sites]\(すべてのサイト\) ドロップダウン メニューから [Manage Business Unit]\(ビジネス ユニットの管理\) を選択。":::

   1. 新しい部署の名前を入力し、 **[追加]** を選択します。

1. 組織のリージョンを反映するために新しいリージョンを追加します。

   1. [Enterprise View]\(エンタープライズ ビュー\)で、 **[All Regions]\(すべてのリージョン\)**  >  **[Manage Regions]\(リージョンの管理\)** を選択します。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/manage-regions.png" alt-text="[All Regions]\(すべてのリージョン\)、[Manage Regions]\(リージョンの管理\) の順に選択して企業内のリージョンを管理する。":::

   1. 新しいリージョンの名前を入力し、 **[追加]** を選択します。

1. サイトを追加します。

   1. エンタープライズ ビューで、上部のバーにある :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false"::: を選択します。 カーソルがプラス記号 ( **+** ) として表示されます。

   1. 新しいサイトの場所に **+** を合わせて、それを選択します。 **[新しいサイトの作成]** ダイアログ ボックスが表示されます。

      :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="[新しいサイトの作成] ビューのスクリーンショット。":::

   1. 新しいサイトの名前と物理アドレスを定義し、 **[保存]** を選択します。 新しいサイトがサイト マップに表示されます。

4. [サイトにゾーンを追加する](#create-enterprise-zones)。

5. [センサーを接続する](how-to-manage-individual-sensors.md#connect-a-sensor-to-the-management-console)。

6. [センサーをサイトのゾーンに割り当てる](#assign-sensors-to-zones)。

### <a name="delete-a-site"></a>サイトの削除

サイトが不要になった場合は、オンプレミス管理コンソールから削除できます。

サイトを削除するには、次の手順を実行します。

1. **[サイトの管理]** ウィンドウで、サイト名が表示されているバーから :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: を選択し、 **[サイトの削除]** を選択します。 サイトの削除を確認する確認ボックスが表示されます。

2. 確認ボックスで **[確認]** を選択します。

## <a name="create-enterprise-zones"></a>エンタープライズ ゾーンを作成する

ゾーンは論理エンティティであり、さまざまな特性に応じて、サイト内のデバイスをグループに分割することができます。 たとえば、生産ライン、支所、サイト領域、またはデバイスの種類のグループを作成できます。 組織に適した特性に基づいてゾーンを定義できます。

ゾーンは、サイト構成プロセスの一環として構成します。

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="[サイトの管理] のゾーン ビューのスクリーンショット。":::

次の表には、 **[サイトの管理]** ウィンドウのパラメーターが示されています。

| パラメーター | 説明 |
|--|--|
| 名前 | センサーの名前。 この名前はセンサーからのみ変更できます。 詳細については、Defender for IoT ユーザー ガイドを参照してください。 |
| IP | センサーの IP アドレス。 |
| Version | センサーのバージョン。 |
| 接続 | センサーの接続状態。 状態は、 **[接続済み]** または **[切断]** です。 |
| 前回のアップグレード | 前回のアップグレードの日付。 |
| [アップグレードの進行状況] | 進行状況バーには、次のようなアップグレード プロセスの状態が示されます。<br />- パッケージをアップロードしています<br />- インストールの準備をしています<br />- プロセスを停止しています<br />- データをバックアップしています<br />- スナップショットを作成しています<br />- 構成を更新しています<br />- 依存関係を更新しています<br />- ライブラリを更新しています<br />- データベースにパッチを適用しています<br />- プロセスを開始しています<br />- システムの正常性を検証しています<br />- 検証に成功しました<br />- 成功<br />- 失敗<br />- アップグレードを開始しました<br />- インストールを開始していますogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >アップグレードの詳細については、[Microsoft サポート](https://support.microsoft.com/)を参照してください。 |
| デバイス | センサーによって監視される OT デバイスの数。 |
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

1. ゾーン名を入力します。

1. 新しいゾーンの説明を入力します。サイトをゾーンに分割するために使用した特性を明確に記述します。

1. **[SAVE]\(保存\)** を選択します。 新しいゾーンは、このゾーンが属しているサイトの **[サイトの管理]** ウィンドウに表示されます。

ゾーンを編集するには、次の手順を実行します。

1. **[サイトの管理]** ウィンドウで、ゾーン名が表示されているバーから :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: を選択し、 **[ゾーンの編集]** を選択します。 **[ゾーンの編集]** ダイアログ ボックスが表示されます。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="[ゾーンの編集] ダイアログ ボックスを示すスクリーンショット。":::

1. ゾーン パラメーターを編集し、 **[保存]** を選択します。

ゾーンを削除するには、次の手順を実行します。

1. **[サイトの管理]** ウィンドウで、ゾーン名が表示されているバーから :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: を選択し、 **[ゾーンの削除]** を選択します。

1. 確認ボックスで **[はい]** を選択します。

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

1. **[接続]** の状態が [接続済み] であることを確認します。 接続されていない場合、接続の詳細については、「[センサーをオンプレミス管理コンソールに接続する](#connect-sensors-to-the-on-premises-management-console)」を参照してください。 

1. 割り当てるセンサーの :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: を選択します。

1. **[センサーの割り当て]** ダイアログ ボックスで、割り当てる部署、リージョン、サイト、ゾーンを選択します。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="[センサーの割り当て] ビューのスクリーンショット。":::

1. **[割り当て]** を選択します。

センサーの割り当てを解除して削除するには、次の手順を実行します。

1. オンプレミス管理コンソールからセンサーを切断します。 詳細については、「[センサーをオンプレミス管理コンソールに接続する](#connect-sensors-to-the-on-premises-management-console)」を参照してください。

1. **[サイトの管理]** ウィンドウで、センサーを選択して :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: を選択します。 しばらくすると、未割り当てのセンサーの一覧にそのセンサーが表示されます。

1. 未割り当てのセンサーをサイトから削除するには、未割り当てのセンサーの一覧からセンサーを選択して、:::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false"::: を選択します。

## <a name="see-also"></a>関連項目

[センサーとオンプレミスの管理コンソールのトラブルシューティング](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
