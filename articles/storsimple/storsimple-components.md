<properties 
   pageTitle="StorSimple のコンポーネントについて" 
   description="StorSimple のデバイス、サービス、管理テクノロジについて説明します。" 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="AdinaH" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="06/11/2015"
   ms.author="v-sharos"/>


# StorSimple のコンポーネントについて 

Microsoft Azure StorSimple へようこそ。StorSimple は、オンプレミスのデバイスと Microsoft Azure クラウド ストレージとの間で実行されるストレージ タスクを管理する統合ストレージ ソリューションです。Azure StorSimple は、ストレージ コストを削減し、ストレージ管理を簡略化し、障害復旧の機能および効率を向上させ、データ モビリティを提供する設計になっています。

以降のセクションでは、Microsoft Azure StorSimple のコンポーネントについて説明します。また、このソリューションでデータを整理し、ストレージを割り当て、ストレージ管理とデータ保護を容易にする方法について説明します。

> [AZURE.NOTE]Microsoft Azure の Web サイトで発行されている StorSimple のデプロイメントに関する情報は、StorSimple 8000 シリーズ デバイスのみに適用されます。7000 シリーズ デバイスについては、[StorSimple ヘルプ](http://onlinehelp.storsimple.com/)を参照してください。

## StorSimple デバイス

Microsoft Azure StorSimple デバイスは、プライマリ ストレージを備え、このストレージに保存されているデータに対する iSCSI アクセスをサポートするオンプレミス ハイブリッド ストレージ デバイスです。また、クラウド ストレージとの通信を管理し、Microsoft Azure StorSimple ソリューションに格納されているすべてのデータのセキュリティと機密性の確保をサポートしています。

StorSimple デバイスには、ソリッド ステート ドライブ (SSD) とハード ディスク ドライブ (HDD) が含まれ、クラスタリングと自動フェールオーバーもサポートしています。また、共有プロセッサ、共有ストレージ、および 2 つのミラーリングされたコントローラーが含まれています。各コントローラーは、次の機能を提供しています。

- ホスト コンピューターへの接続
- ローカル エリア ネットワーク (LAN) に接続するための最大 6 個のネットワーク ポート
- ハードウェアの監視
- 非揮発性ランダム アクセス メモリ (NVRAM) (停電した場合でも情報が保持されます)
- クラスター対応更新: フェールオーバー クラスター内のサーバーでソフトウェアの更新を管理することで、更新によるサービスの可用性への影響を最小限にするか、まったくないようにします
- クラスター サービス: バックエンド クラスターのように機能して高可用性を提供し、HDD または SSD に障害が発生した場合やオフラインになった場合に発生する可能性がある悪影響を最小限に抑えます

どの時点でも、1 つのコントローラーのみがアクティブです。アクティブなコントローラーに障害が発生すると、2 つ目のコントローラーが自動的にアクティブになります。

詳細については、「[StorSimple デバイス](https://msdn.microsoft.com/library/azure/dn772363.aspx)」を参照してください。

## StorSimple 仮想デバイス

StorSimple を使用すると、実際のハイブリッド ストレージ デバイスのアーキテクチャと機能をレプリケートする仮想デバイスを作成できます。

StorSimple 仮想デバイス (仮想アプライアンスともいいます) は、Azure 仮想マシン内の単一ノードで動作します。(仮想デバイスは、Azure 仮想マシンにのみ作成できます。StorSimple デバイスまたはオンプレミス サーバーには作成できません)。 StorSimple 仮想デバイスと物理 StorSimple デバイスは次の点が異なります。

- 仮想デバイスのインターフェイスは 1 つのみですが、物理デバイスには 6 個のネットワーク インターフェイスがあります。 
- 仮想デバイスの登録は、別のタスクとしてではなく、デバイス構成時に行います。
- 仮想デバイスからサービス データの暗号化キーを再生成することはできません。キーのロールオーバー中に、物理デバイスでキーを再生成し、新しいキーで仮想デバイスを更新します。
- 仮想デバイスに更新プログラムを適用する必要がある場合、ある程度のダウン タイムが発生します。物理 StorSimple デバイスではダウン タイムが発生しません。

テスト デプロイメントや小規模な試験的デプロイメントなど、物理デバイスを使用できない障害復旧シナリオに対しては、StorSimple 仮想デバイスを使用することをお勧めします。

詳細については、「[StorSimple 仮想デバイス](https://msdn.microsoft.com/library/azure/dn772390.aspx)」を参照してください。


## ストレージ管理テクノロジ
 
専用の StorSimple デバイスと仮想デバイスに加え、Microsoft Azure StorSimple では、次のソフトウェア テクノロジを使用して、データへの迅速なアクセスを提供し、ストレージの消費を低減しています。

- 自動ストレージ階層化 
- 仮想プロビジョニング 
- 重複除去と圧縮 

### 自動ストレージ階層化

Microsoft Azure StorSimple では、現在の使用量、保有期間、および他のデータとの関係に基づいて、データが論理階層内で自動的に整理されます。最もアクティブなデータはローカルに格納され、それほどアクティブではないデータや非アクティブなデータはクラウドに自動的に移行されます。図 1 に、この格納方法を示します。
 
![StorSimple のストレージ階層](./media/storsimple-components/hcs-data-services-storsimple-components-tiers.png)

**図 1: StorSimple ストレージ**

迅速なアクセスを可能にするために、StorSimple では、非常にアクティブなデータ (ホット データ) は StorSimple デバイスの SSD に格納されます。また、ときどき使用されるデータ (ウォーム データ) は、デバイスまたはデータ センターにあるサーバーの HDD に格納されます。非アクティブなデータ、バックアップ データ、アーカイブやコンプライアンスの目的で保持されるデータは、クラウドに移動されます。

StorSimple は、使用パターンの変化に応じて、データとストレージの割り当てを調整し、再整理します。たとえば、時間の経過と共に一部の情報があまりアクティブではなくなったとします。アクティブな程度の低下に従って、データは SSD から HDD へ、また HDD からクラウドへと移行されます。この同じデータが再びアクティブになると、また元のストレージ デバイスに移行されます。

### 仮想プロビジョニング

仮想プロビジョニングは、物理リソースを超える空き容量がストレージにあるように見える仮想化テクノロジです。StorSimple では、事前に十分なストレージを確保するのではなく、仮想プロビジョニングを使用して、現在の要件を満たすために必要な容量のみを割り当てます。クラウド ストレージの柔軟な性質がこの手法を容易にし、StorSimple は変化する需要に合わせてクラウド ストレージを増減できます。

### 重複除去と圧縮

Microsoft Azure StorSimple は、重複除去とデータ圧縮を使用して、必要なストレージをさらに減らします。

重複除去では、格納されているデータ セットの重複を除去してデータを圧縮します。情報が変更されると、StorSimple は、変更されていないデータを無視して変更内容のみを取り込みます。さらに、StorSimple は、不要な情報を特定して削除することで、格納されるデータの量を減らします。

## StorSimple 用 Windows PowerShell

StorSimple 用 Windows PowerShell にはコマンドライン インターフェイスがあり、Microsoft Azure StorSimple サービスの作成と管理、および StorSimple デバイスのセットアップと監視に使用できます。これは Windows PowerShell ベースのコマンドライン インターフェイスであり、StorSimple デバイスの管理専用のコマンドレットが含まれています。StorSimple 用 Windows PowerShell には、次の機能があります。

- デバイスを登録する。
- デバイスのネットワーク インターフェイスを構成する。
- 特定の種類の更新プログラムをインストールする。
- サポート セッションにアクセスしてデバイスのトラブルシューティングを行う。
- デバイスの状態を変更する。

StorSimple 用 Windows PowerShell には、(デバイスに直接接続されているホスト コンピューター上の) シリアル コンソールを使用してアクセスできるほか、Windows PowerShell リモート処理を使用してリモート アクセスできます。デバイスの最初の登録など、StorSimple 用 Windows PowerShell の一部のタスクは、シリアル コンソールでのみ実行できます。

詳細については、「[StorSimple 用 Windows PowerShell](https://msdn.microsoft.com/library/azure/dn772425.aspx)」を参照してください。

## StorSimple Manager サービス

Microsoft Azure StorSimple には Web ベースのユーザー インターフェイス (StorSimple Manager サービス) があり、データ センターとクラウド ストレージを一元管理できます。StorSimple Manager サービスを使用して、次のタスクを実行できます。

- StorSimple デバイスのシステム設定を構成する。
- StorSimple デバイスのセキュリティ設定を構成および管理する。
- クラウドの資格情報とプロパティを構成する。
- サーバー上のボリュームを構成および管理する。
- ボリューム グループを構成する。
- データをバックアップおよび復元する。
- パフォーマンスを監視する。
- システム設定を確認し、問題の可能性を特定する。

StorSimple Manager サービスを使用すると、初期のセットアップや更新プログラムのインストールなど、システムのダウン タイムが必要なタスクを除き、すべての管理タスクを実行できます。

詳細については、「[StorSimple Manager サービス](https://msdn.microsoft.com/library/azure/dn772396.aspx)」を参照してください。

## StorSimple Snapshot Manager

StorSimple Snapshot Manager は、ローカル データとクラウド データについて、一貫性のある特定の時点のバックアップ コピーを作成するために使用できる Microsoft 管理コンソール (MMC) スナップインです。スナップインは、Windows Server ベースのホストで動作します。StorSimple Snapshot Manager には、次の機能があります。

- ボリュームを構成、バックアップ、削除する。
- ボリューム グループを構成してバックアップ データのアプリケーション整合性を確保する。
- 事前に設定したスケジュールに基づいてデータがバックアップされ、指定した場所 (ローカルまたはクラウド) に格納されるように、バックアップ ポリシーを管理する。
- ボリュームと個々のファイルを復元する。

バックアップはスナップショットとして取り込まれます。スナップショットは、最後のスナップショットが作成された時点以降の変更内容のみを記録するため、必要なストレージ容量は、完全バックアップよりもはるかに少なくなります。必要に応じて、バックアップ スケジュールを作成するか、バックアップを即時に実行することができます。さらに、StorSimple Snapshot Manager を使用すると、保存するスナップショット数を制御する保持ポリシーを設定できます。後でバックアップからデータを復元するときに、StorSimple Snapshot Manager で、ローカルまたはクラウドのスナップショットのカタログから選択できます。

障害が発生した場合、または他の理由でデータを復元する必要がある場合、StorSimple Snapshot Manager は必要に応じて段階的に復元します。データの復元において、ファイルを復元するとき、機器を交換するとき、または操作を別のサイトに移行するときに、システム全体をシャットダウンする必要はありません。

詳細については、「[StorSimple Snapshot Manager](https://msdn.microsoft.com/library/azure/dn772365.aspx)」を参照してください。

## SharePoint 用 StorSimple アダプター

Microsoft Azure StorSimple には SharePoint 用 StorSimple アダプターが含まれています。これは、StorSimple のストレージおよびデータ保護機能を透過的に SharePoint サーバー ファームに拡張するオプションのコンポーネントです。このアダプターはリモート BLOB ストレージ (RBS) プロバイダーおよび SQL Server RBS 機能と連携しているため、Microsoft Azure StorSimple システムでバックアップされるサーバーに BLOB を移動することができます。Microsoft Azure StorSimple は、使用状況に応じてローカルまたはクラウドに BLOB データを格納します。

SharePoint 用 StorSimple アダプターは、SharePoint サーバーの全体管理ポータル内から管理します。そのため、SharePoint は引き続き一元管理することができます。また、すべてのストレージが SharePoint ファーム内にあるように見えます。

詳細については、「[SharePoint 用 StorSimple アダプター](https://msdn.microsoft.com/library/azure/dn757737.aspx)」を参照してください。


## 次のステップ

[StorSimple のリリース ノート](https://msdn.microsoft.com/library/azure/dn772367.aspx)を読む




 

<!---HONumber=July15_HO3-->