---
title: BareMetal インフラストラクチャ上の Oracle 用に SnapCenter を構成する
description: BareMetal インフラストラクチャ上の Oracle 用に SnapCenter を構成する方法を説明します。
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 05/05/2021
ms.openlocfilehash: edc75bd8d5fb2a64e22b2dd8f53fceda092161c3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676107"
---
# <a name="configure-snapcenter-for-oracle-on-baremetal-infrastructure"></a>BareMetal インフラストラクチャ上の Oracle 用に SnapCenter を構成する

この記事では、BareMetal インフラストラクチャ上で Oracle を実行するために NetApp SnapCenter を構成する手順について説明します。

## <a name="add-storage-hosts-to-snapcenter"></a>SnapCenter にストレージ ホストを追加する

まず、SnapCenter にストレージ ホストを追加しましょう。 

SnapCenter セッションを開始してセキュリティの除外を保存すると、アプリケーションが起動します。 Active Directory の資格情報を使用して、仮想マシン (VM) 上の SnapCenter にサインインします。

https://\<hostname\>:8146/

これで、運用ストレージの場所とセカンダリ ストレージの場所の両方を追加する準備ができました。

### <a name="add-the-production-storage-location"></a>運用ストレージの場所を追加する

運用ストレージ仮想マシン (SVM) を追加するには:

1. 左側のメニューで **[Storage Systems]\(ストレージ システム\)** を選択し、 **[+ New]\(+ 新規\)** を選択します。

2. **[Add Storage System]\(ストレージ システムの追加\)** の情報を入力します。

      - ストレージ システム: Microsoft Operations によって提供された SVM IP アドレスを入力します。
      - 作成したユーザー名を入力します。既定値は **snap center** です。
      - Microsoft Operations により [REST を使用して顧客のパスワードが変更された](set-up-snapcenter-to-route-traffic.md#steps-to-integrate-snapcenter)ときに作成されたパスワードを入力します。
      - **[Send Autosupport notification for failed operations to storage system]\(失敗した操作の自動サポート通知をストレージ システムに送信する\)** はオフのままにします。
      - **[Log SnapCenter events to syslog]\(SnapCenter イベントのログを syslog に記録する\)** を選択します。

3. **[Submit]\(送信\)** をクリックします。

    ストレージが検証された後、追加したストレージ システムの IP アドレスが、入力したユーザー名と共に表示されます。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/oracle-baremetal-snapcenter-add-production-storage-complete.png" alt-text="追加されたストレージ システムの IP アドレスを示すスクリーンショット。":::

    既定では、テナントごとに 1 つの SVM があります。 複数のテナントがある場合は、ここで SnapCenter にすべての SVM を構成することをお勧めします。

### <a name="add-secondary-storage-location"></a>セカンダリ ストレージの場所を追加する

ディザスター リカバリー (DR) ストレージの場所を追加するには、プライマリと DR の両方の場所にあるお客様のサブスクリプションをピアリングする必要があります。 Microsoft Operations にサポートを依頼してください。

セカンダリ ストレージの場所の追加は、プライマリ ストレージの場所の追加と似ています。 ただし、プライマリと DR の場所をピアリングした後、セカンダリの場所のストレージに ping を実行して、セカンダリ ストレージの場所へのアクセスを確認する必要があります。 

>[!NOTE]
>ping が失敗する場合は、通常、クライアントの仮想 LAN (VLAN) への既定のルートがホストに存在しないことが原因です。

ping を検証した後、プライマリ ストレージの追加に使用した手順を、DR ホスト上の DR の場所に対してのみ繰り返します。 両方の場所で同じ証明書を使用することをお勧めしますが、必須ではありません。

## <a name="set-up-oracle-hosts"></a>Oracle ホストをセットアップする

>[!NOTE]
>このプロセスは、場所 (運用またはディザスター リカバリー) に関係なく、すべての Oracle ホストを対象としています。

1. SnapCenter にホストを追加し、SnapCenter プラグインをインストールする前に、JAVA 1.8 をインストールする必要があります。 続ける前に、各ホストに Java 1.8 がインストールされていることを確認します。

2. 各 Oracle Real Application Clusters (RAC) ホストに同じ非ルート ユーザーを作成し、/etc/sudoers に追加します。 新しいパスワードを指定する必要があります。

3. ユーザーを作成したら、明示的なアクセス許可のセットを使用して /etc/sudoers に追加する必要があります。 これらのアクセス許可は、SnapCenter VM の C:\ProgramData\NetApp\SnapCenter\Package Repository を参照して、oracle\_checksum ファイルを開くことによって確認できます。

4. sudo パッケージに応じて、適切なコマンドのセットをコピーします。LINUXUSER は新しく作成したユーザー名に置き換えます。 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/netapp-snapcenter-oracle-checksum-details.png" alt-text="oracle_checksum ファイルの詳細のスクリーンショット。":::

    sudo パッケージ 1.8.7 以降のコマンドのセットが、/etc/sudoers にコピーされます。

5. sudoers にユーザーを追加したら、SnapCenter で **[Settings]\(設定\)**  >  **[Credential]\(資格情報\)**  >  **[New]\(新規\)** を選択します。

6. [Credential]\(資格情報\) ボックスを次のように設定します。

    - **[Credential Name]\(資格情報名\)** : ユーザー名と sudoers を示す名前を指定します。
    - **[Authentication]\(認証\)** : Linux
    - **[Username]\(ユーザー\)** : 新しく作成したユーザー名を指定します。
    - **パスワード**: \<enter password\>
    - **[Use sudo privileges]\(sudo の特権を使用する\)** チェック ボックスをオンにします
    
7. **[OK]** を選択します。

8. 左のナビゲーションで **[Hosts]\(ホスト\)** を選択し、 **[+ Add]\(+ 追加\)** を選択します。

9. **[Add Host]\(ホストの追加\)** に次の値を入力します。

    - **[Host Type]\(ホストの種類\)** : Linux
    - **[Host Name]\(ホスト名\)** : プライマリ RAC ホストの FQDN または IP アドレスを入力します。
    - **[Credentials]\(資格情報\)** : ドロップダウンを選択し、sudoers 用に新しく作成した資格情報を選択します。
    - **[Oracle Database]\(Oracle データベース\)** チェック ボックスをオンにします。

    >[!NOTE]
    >実際の Oracle ホスト IP アドレスのいずれかを入力する必要があります。 ノード VIP またはスキャン IP の入力はサポートされていません。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-add-hosts-details.png" alt-text="新しいホストの詳細を示すスクリーンショット。":::
    
10. **[More Options]\(その他のオプション\)** を選択します。 **[Add all hosts in the Oracle RAC]\(Oracle RAC 内のすべてのホストを追加する\)** がオンになっていることを確認します。 **[Save]\(保存\)** を選択し、 **[Submit]\(送信\)** を選択します。

11. プラグイン インストーラーによって、指定した IP アドレスとの通信が試みられす。 通信が成功した場合は、 **[Confirm and Submit]\(確認して送信\)** を選択して、指定した Oracle RAC ホストの ID を確認します。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-add-hosts-confirm-fingerprint.png" alt-text="新しいホスト フィンガープリントを示すスクリーンショット。":::

12. 最初の Oracle RAC を確認した後、SnapCenter によって、クラスターの一部として他のすべての Oracle RAC サーバーとの通信と確認が試みられます。 **[Confirm Others and Submit]\(他の確認と送信\)** を選択します。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-add-hosts-fingerprint-authentication.png" alt-text="フィンガープリントの認証を示すスクリーンショット。":::

    マネージド ホストの状態画面が表示され、選択した Oracle プラグインが SnapCenter によってインストールされていることがわかります。 任意の Oracle RAC ホストにある /opt/NetApp/snapcenter/scc/logs/DISCOVER-PLUGINS\_0.log を見ることで、インストールの進行状況を確認できます。 

    プラグインが正常にインストールされると、次の画面が表示されます。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-installed-plugins.png" alt-text="インストールされているすべての SnapCenter プラグインを示すスクリーンショット。":::

## <a name="add-credentials-for-oracle-recovery-manager"></a>Oracle Recovery Manager の資格情報を追加する

Oracle Recovery Manager (RMAN) カタログの認証方法によって、RMAN カタログ データベースに対する認証が行われます。 外部カタログ メカニズムを構成し、データベースをカタログに登録した場合は、RMAN カタログの認証を追加する必要があります。 RMAN の資格情報はいつでも追加できます。

1. SnapCenter の左側のメニューで **[Settings]\(設定\)** を選択し、 **[Credential]\(資格情報\)** を選択します。 右上隅にある **[New]\(新規\)** を選択します。

2. SnapCenter に格納されている RMAN 資格情報を呼び出すための **[Credential Name]\(資格情報名\)** を入力します。 **[Authentication]\(認証\)** ドロップダウンで、 **[Oracle RMAN Catalog for Authentication]\(認証用の Oracle RMAN カタログ\)** を選択します。 ユーザー名とパスワードを入力します。 **[OK]** を選択します。

3. 資格情報を追加した後は、SnapCenter でデータベースの設定を変更する必要があります。 リソースでデータベースを選択します。 メイン ウィンドウの右上隅にある **[Database Settings]\(データベースの設定\)** を選択します。

4. [Database Settings]\(データベースの設定\) 画面で、 **[Configure Database]\(データベースの構成\)** を選択します。

5. **[Configure RMAN Catalog Settings]\(RMAN カタログ設定の構成\)** を展開します。 このデータベースに対して以前に設定された **[Use Existing Credential]\(既存の資格情報を使用する\)** のドロップダウンを選択し、適切なオプションを選択します。 この個別のデータベースの TNS 名を追加します。 **[OK]** を選択します。

    >[!NOTE]
    >前のステップでは、ユーザー名とパスワードの一意の組み合わせごとに、異なる資格情報を作成する必要があります。 必要に応じて、すべてのデータベースに対して 1 つの資格情報セットを SnapCenter で設定できます。
    >
    >バックアップ ポリシーに従った保護ポリシーの作成に関する次のセクションで説明するように、RMAN 資格情報をデータベースに追加しましたが、各保護ポリシーについて [Catalog Backup with Oracle Recovery Manager (RMAN)]\(Oracle Recovery Manager (RMAN) でのカタログ バックアップ\) もオンにしない限り、RMAN はカタログ化されません。

## <a name="create-protection-policies"></a>保護ポリシーを作成する

ホストが SnapCenter に正常に追加されると、保護ポリシーを作成できる状態になります。 

左側のメニューで **[Resources]\(リソース\)** を選択します。 SnapCenter に、RAC ホスト上に存在していて識別されたすべてのデータベースが表示されます。 bn6p1X と grid の単一のインスタンスの種類は、保護スキームの一部として無視されます。 この時点では、すべての状態が [Not Protected]\(保護されていません\) になっている必要があります。

[概要](netapp-snapcenter-integration-oracle-baremetal.md#oracle-disaster-recovery-architecture)に関する記事で説明されているように、ファイルの種類によってスナップショットの頻度が異なり、アーカイブ ログ バックアップを 15 分ごとに作成することで、約 15 分のローカル RPO が可能になります。 データファイルは、1 時間おき以上のように、より長い間隔でバックアップされます。 そのため、2 つの異なるポリシーが作成されます。

RAC データベースが識別されると、データファイルと制御ファイルのポリシーや、アーカイブ ログ用のポリシーなど、いくつかの保護ポリシーが作成されます。

### <a name="datafiles-protection-policy"></a>データファイルの保護ポリシー

データファイルの保護ポリシーを作成するには、次の手順のようにします。

1. SnapCenter の左側のメニューで **[Settings]\(設定\)** を選択し、上部メニューの **[Policies]\(ポリシー\)** を選択します。 **[新規]** を選択します。

2. **[Datafiles and control files]\(データファイルと制御ファイル\)** のラジオ ボタンを選択して、下にスクロールします。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-new-database-backup-policy.png" alt-text="新しいデータベース バックアップ ポリシーの詳細を示すスクリーンショット。":::

3. **[hourly]\(1 時間ごと\)** ラジオ ボタンを選択します。 カタログの目的に RMAN との統合が望ましく、RMAN 資格情報が既に追加されている場合は、 **[Catalog backup with Oracle Recovery Manager (RMAN)]\(Oracle Recovery Manager (RMAN) でのカタログ バックアップ\)** チェック ボックスをオンにします。 **[次へ]** を選択します。 カタログ バックアップをオンにした場合、カタログ化が行われるには [RMAN 資格情報を追加する必要があります](#add-credentials-for-oracle-recovery-manager)。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-database-backup-policy-frequency.png" alt-text="バックアップ ポリシーでスケジュールの頻度を選択するオプションを示すスクリーンショット。":::

    >[!IMPORTANT]
    >毎時や毎日以外のバックアップ頻度が必要な場合でも、スケジュール ポリシーを選択する必要があります。 実際のバックアップ頻度は、プロセスで後ほど設定します。 このポリシーでのすべてのバックアップがオンデマンドのみである場合を除き、[none]\(なし\) を選択しないでください。

4. 左側のメニューで **[Retention]\(保持\)** を選択します。 バックアップ ポリシーには、2 種類の保持設定が設定されます。 最初の保持設定は、保持するオンデマンド スナップショットの最大数です。 バックアップ ポリシーに基づいて、設定された数のスナップショットが保持されます (次の例では、48 個のオンデマンド スナップショットが 14 日間保持されます)。 バックアップ ポリシーに従って、適切なオンデマンド保持ポリシーを設定します。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-new-database-backup-policy-retention.png" alt-text="データベース バックアップ ポリシーの保持設定を示すスクリーンショット。":::

5. 次の保持期間設定セットは、前のエントリ (毎時、毎日、毎週など) に基づいて保持するスケジュールされたスナップショットの数です。 **[次へ]** を選択します。

6. **[Replication]\(レプリケーション\)** 画面で、 **[Update SnapMirror after creating a local snapshot copy]\(ローカル スナップショット コピーの作成後に SnapMirror を更新 する\)** チェック ボックスをオンにして、 **[次へ]** を選択します。 その他のエントリは既定値のままにします。

    >[!NOTE]
    >現在、SnapVault は、Oracle BareMetal インフラストラクチャ環境ではサポートされていません。

    後で、[Protection Policy]\(保護ポリシー\) 画面に戻り、左側のメニューで **[modify protection policy]\(保護ポリシーの変更\)** と **[Replication]\(レプリケーション\)** を選択することにより、レプリケーションを追加できます。

7. **[Script]\(スクリプト\)** ページでは、Oracle バックアップが行われる前または後に実行する必要があるスクリプトを入力します。 現在、SnapCenter プロセスの一部としてのスクリプトはサポートされていません。 **[次へ]** を選択します。

8. 左側のメニューで **[Verification]\(検証\)** を選択します。 回復性の整合性のスナップショットを検証する機能が必要な場合は、[hourly]\(毎時\) のチェック ボックスをオンにします。 検証スクリプト コマンドは現在はサポートされていません。 **[次へ]** を選択します。

    >[!NOTE]
    >検証プロセスの実際の場所とスケジュールは、後の「保護ポリシーをリソースに割り当てる」セクションで追加します。

9. **[Summary]\(概要\)** ページで、すべての設定が想定どおりに入力されていることを確認し、 **[Finish]\(完了\)** を選択します。

### <a name="archive-logs-protection-policy"></a>アーカイブ ログ保護ポリシー

再度前の手順に従って、アーカイブ ログ保護ポリシーを作成します。 ステップ 2 で、[Datafiles and control files]\(データファイルと制御ファイル\) ではなく、 **[Archive logs]\(アーカイブ ログ\)** のラジオ ボタンを選択します。

## <a name="assign-protection-policies-to-resources"></a>保護ポリシーをリソースに割り当てる

ポリシーを作成した後は、SnapCenter がそのポリシーに従って実行し始めるように、それをリソースに関連付ける必要があります。

### <a name="datafiles-resource-group"></a>データファイル リソース グループ

1. 左側のメニューで **[Resources]\(リソース\)** を選択し、メイン ウィンドウの右上隅にある **[New Resource Group]\(新しいリソース グループ\)** を選択します。

2. 検索を容易にするために、このリソース グループの **[Name]\(名前\)** とタグを追加します。

    >[!NOTE]
    >**[Use custom name format for Snapshot copy]\(スナップショットのコピーにカスタム名形式を使用する\)** チェック ボックスをオンにし、 **$ResourceGroup**、 **$Policy**、 **$ScheduleType** の各エントリを追加することをお勧めします。 これにより、スナップショットのプレフィックスが SnapCenter の標準を満たし、スナップショット名で必要な詳細がすぐわかるようになります。 **[Use custom name format for Snapshot copy]\(スナップショットのコピーにカスタム名形式を使用する\)** をオフのままにすると、 **[Name]\(名前\)** に入力したものが作成されるスナップショットのプレフィックスになります。 データベースと保護対象がわかるような名前を入力してください (データファイルや制御ファイルなど)。 

3. バックアップの設定で、アーカイブ ログを除外するために **/u95** を追加します。

4. [Resource]\(リソース\) ページで、同じバックアップ保護ポリシーで保護するデータベースを、[Available Resources]\(使用可能なリソース\) から [Selected Resources]\(選択済みリソース\) に移動します。 ホストごとに Oracle データベース インスタンスを追加しないでください。データベースだけです。 **[次へ]** を選択します。

5. 前に作成したデータファイルと制御ファイルの保護ポリシーを選択します。 ポリシーを選択した後、[Configure schedules]\(スケジュールの構成\) の下にある鉛筆を選択します。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/new-datafiles-resource-group-available-policies.png" alt-text="スケジュールを構成するための保護ポリシーの選択を示すスクリーンショット。":::

6. 選択したポリシーを実行するスケジュールを追加します。 開始日時を、現在の日時より後にする必要があります。

    >[!NOTE]
    >スケジュールの頻度は、ポリシーの作成時に選択した頻度と一致する必要はありません。 毎時の場合は、[Repeat every 1 hours]\(1 時間ごとに繰り返す\) のままにすることをお勧めします。 開始時刻は、1 時間ごとにバックアップを実行する開始時刻として機能します。 特定の時刻にアクティブにできるバックアップは 1 つだけなので、データファイルを保護するためのスケジュールが、アーカイブ ログを保護するためのスケジュールと重複しないようにしてください。

7. 検証により、作成したスナップショットが使用できることを確認します。 検証プロセスは広範囲に及び、すべての Oracle データベース ボリュームの複製の作成、プライマリ ホストへのデータベースのマウント、回復可能性の検証などが含まれます。 このプロセスには時間がかかり、多くのリソースが必要になります。 検証スケジュールを構成する場合は、 **[+]** 記号を選択します。

    >[!NOTE]
    >検証プロセスにより、ホスト上のリソースが長時間にわたって占有されます。 検証を追加する場合は、セカンダリの場所のホストで行うことをお勧めします (可能な場合)。
    
    次のスクリーンショットは、作成された時点でレプリケーションが有効になっておらず、レプリケートされるスナップショットがない、新しいリソース グループの検証を示したもので。 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/new-resource-group-verification-schedules.png" alt-text="リソース グループの検証スケジュールを構成する方法を示すスクリーンショット。":::

    レプリケーションが有効になっていて、ディザスター リカバリーの場所のホストを検証に使用する場合は、次のサブセクションのステップ 2 に進みます。 そのステップでは、セカンダリ ロケーターを読み込んで、セカンダリの場所のバックアップを検証することができます。

### <a name="add-verification-schedule-for-new-resource-group"></a>新しいリソース グループの検証スケジュールを追加する

1. **[Run verification after backup]\(バックアップの後で検証を実行する\)** を選択するか、 **[Run scheduled verification]\(スケジュールした検証を実行する\)** を選択してドロップダウンでスケジュール済みの頻度を選択します。 DR が有効になっている場合は、 **[Verify on secondary storage location]\(セカンダリ ストレージの場所で検証する\)** を選択し、DR の場所で検証を実行して、運用環境でのリソースの負荷を軽減できます。 **[OK]** を選択します。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/new-resource-group-add-verification-schedules.png" alt-text="検証スケジュールの追加方法を示すスクリーンショット。":::

    検証が必要ない場合、または既にローカル環境で設定してある場合は、後の SMTP のセットアップ (ステップ 5) に進みます。

2. データファイル保護ポリシーを作成するときに SnapMirror の更新を有効にし、セカンダリ ストレージの場所を追加した場合は、SnapCenter によって 2 つの場所の間のレプリケーションが検出されます。 その場合は、セカンダリ ロケーターを読み込んでセカンダリの場所でバックアップを検証できる画面が表示されます。 **[Load locators]\(ロケーターを読み込む\)** を選択します。

3. ロケーターの読み込みを選択すると、SnapCenter により、データファイルと制御ファイルを含むことが検出された SnapMirror の関係が表示されます。 これらは、Microsoft Operations によって提供されるディザスター リカバリー フレームワークと一致している必要があります。 [Configure Schedules]\(スケジュールの構成\) の下にある鉛筆を選択します。

4. **[Verify on secondary storage location]\(セカンダリ ストレージの場所で検証する\)** のチェック ボックスをオンにします。 

5. SMTP サーバーが使用可能な場合は、SnapCenter で SnapCenter 管理者にメールを送信できます。 メールを受け取る場合は、以下を入力します。

    - **[Email Preference]\(メールの設定\)** : メールの受信頻度の設定を入力します。
    - **[From]\(送信元\)** : SnapCenter によるメールの送信元として使用するメール アドレスを入力します。
    - **[To]\(送信先\)** : SnapCenter によるメールの送信先のメール アドレスを入力します。
    - **[Subject]\(件名\)** : SnapCenter によるメールの送信時に使用する件名を入力します。
    - **[Attach job report]\(ジョブ レポートを添付する\)** チェック ボックスをオンにします。
    - **[次へ]** を選択します。

6. [Summary]\(概要\) ページで設定を確認し、 **[Finish]\(完了\)** を選択します。

リソース グループが作成された後で、リソース グループを識別するには:

1. 左側のメニューで **[Resources]\(リソース\)** を選択します。
2. メイン ウィンドウの [View]\(表示\) の横にあるドロップダウン メニューを選択し、 **[Resource Group]\(リソース グループ\)** を選択します。
 
    これが初めてのリソース グループの場合は、データファイルと制御ファイル用に新しく作成されたリソース グループだけが存在します。 アーカイブ ログ リソース グループも作成した場合は、そのグループも表示されます。

### <a name="archive-log-resource-group"></a>アーカイブ ログ リソース グループ

アーカイブ ログ リソース グループに保護ポリシーを割り当てるには、データファイルのリソース グループに保護ポリシーを割り当てたときと同じ手順に従います。 唯一の違いは次のとおりです。

- ステップ 3 で、アーカイブ ログを除外するための **/u95** を追加しません。このフィールドは空白のままにします。
- ステップ 6 では、15 分ごとにアーカイブ ログをバックアップすることをお勧めします。
- アーカイブ ログの場合、検証タブには何も表示されません。 

## <a name="next-steps"></a>次のステップ

SnapCenter で Oracle Database のオンデマンド バックアップを作成する方法を学習します。

> [!div class="nextstepaction"]
> [SnapCenter でオンデマンド バックアップを作成する](create-on-demand-backup-oracle-baremetal.md)
