---
title: "Veritas NetBackup と統合して StorSimple を構成する | Microsoft ドキュメント"
description: "Veritas NetBackup と統合して StorSimple バックアップ ターゲットを構成する方法を説明します。"
services: storsimple
documentationcenter: 
author: hkanna
manager: matd
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/05/2016
ms.author: hkanna
translationtype: Human Translation
ms.sourcegitcommit: 45fc96674d52a1e1273eed390195c59889b86a38
ms.openlocfilehash: 50c4be545f3b852d99968548784ffdff048d5355

---

# <a name="configure-storsimple-with-veritas-netbackup8482"></a>Veritas NetBackup&#8482; と統合して StorSimple を構成する

## <a name="overview"></a>概要

Microsoft Azure StorSimple は、Azure ストレージ アカウントをオンプレミス ソリューションの拡張機能として使用し、オンプレミス ストレージとクラウド ストレージをまたがって自動的にデータを階層化することで複雑なデータの急増に対応するハイブリッド クラウド ストレージ ソリューションです。

この記事では、Veritas NetBackup と StorSimple の統合について取り上げ、両方のソリューションを統合するためのベスト プラクティスについて説明します。 StorSimple と最適に統合するために Veritas NetBackup を構成する方法に関する推奨事項もご紹介します。 Microsoft は、お客様のバックアップ要件と SLA に合わせて Veritas NetBackup を最適に構成する方法に関し、Veritas のベスト プラクティス、バックアップ アーキテクト、管理者に従います。


この記事は、構成手順の概要と主要な概念を説明するものであり、構成またはインストールの方法をステップ バイ ステップで説明するガイドではありません。 基本的なコンポーネントとインフラストラクチャが正常に動作しており、ここで説明する概念をサポートする準備が整っていることを前提としています。

## <a name="why-storsimple-as-a-backup-target"></a>StorSimple をバックアップ ターゲットとして使用する理由

StorSimple がバックアップ ターゲットとしてふさわしい理由は、次の通りです。

-   何も変更することなく、高速バックアップ先となるバックアップ アプリケーション用の標準的なローカル ストレージを提供します。 最新のバックアップからのすばやい復元にも使用できます。

-   StorSimple のクラウド階層化はクラウド ストレージ アカウントとシームレスに統合され、費用効果の高い Microsoft Azure ストレージを使用できます。

-   障害復旧のためのオフサイト ストレージを自動的に提供します。


## <a name="target-audience"></a>対象読者

この記事の対象読者は、ストレージ、Windows Server 2012 R2、イーサネット、クラウド サービス、NetBackup に関する知識を持つ、バックアップ管理者、ストレージ管理者、ストレージ アーキテクトです。

## <a name="supported-versions"></a>サポートされているバージョン

-   NetBackup 7.7.x 以降。

-   [StorSimple Update 3 以降](storsimple-overview.md#storsimple-workload-summary)。

## <a name="key-concepts"></a>主要な概念

その他のストレージ ソリューションと同様、ソリューションのストレージ性能、SLA、変更率、容量増加の必要性について慎重に評価することが不可欠です。 クラウドの階層を導入することで、お客様のクラウドへのアクセス時間と処理能力が、StorSimple のジョブを実行する能力に根本的な影響を与えます。

StorSimple は、適切に定義されたデータのワーキング セット (ホットデータ) 上で動作するアプリケーションにストレージを提供するよう設計されています。 このモデルでは、データのワーキング セットはローカルの階層に格納され、残りの非ワーキング/クラウド/アーカイブ セットはクラウドに格納されます。 このモデルを示したのが次のグラフです。 ほぼ平らな緑の線は、StorSimple デバイスのローカル階層に格納されているデータを表しています。 赤い線は、すべての階層で StorSimple ソリューションに格納されているデータの総量を表しています。 平らな緑の線と右上がりの曲線を描く赤い線の間の空間は、クラウドに格納されているデータの総量を表しています。

**StorSimple の階層化**
![Storsimple の階層化の図](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)


このグラフを見ると、StorSimple をバックアップ ターゲットとして稼働させることが適切であることがわかります。 StorSimple では次のことができます。

-   ローカルのデータのワーキングセットから最も頻繁に復元を実行します。

-   オフサイトの障害復旧と復元の頻度が低いデータにクラウドを使用します。

## <a name="storsimple-benefits"></a>StorSimple の利点

StorSimple は、オンプレミスとクラウドのストレージへのシームレスなアクセスを生かして、Microsoft Azure とシームレスに統合するオンプレミス ソリューションを提供します。

StorSimple は、ソリッド ステート デバイス (SSD) ストレージ、シリアル接続 SCSI (SAS) ストレージと Azure Storage を含むオンプレミス デバイス間で自動的に階層化します。 自動的に階層化されることで、頻繁にアクセスされるデータは SSD 階層と SAS 階層でローカルに保持され、アクセス頻度の低いデータは Azure Storage に移動されます。

StorSimple には次の利点があります。

-   クラウドを使用してこれまでにない重複除去レベルを実現するための一意の重複除去と圧縮アルゴリズム

-   高可用性

-   Azure を使用した geo レプリケーション

-   Azure の統合

-   クラウドのデータ暗号化

-   障害復旧とコンプライアンスの強化

StorSimple は 2 つの主要なデプロイメント シナリオ (プライマリとセカンダリ バックアップ ターゲット) を提示しますが、これは基本的にシンプルなブロック ストレージ デバイスであることにご留意ください。 StorSimple は、すべての圧縮と重複除去を実行し、クラウドからアプリケーションとファイル システムの両方にシームレスにデータの送受信を行います。

StorSimple の詳細については、「[StorSimple 8000 シリーズ: ハイブリッド クラウド ストレージ ソリューション](storsimple-overview.md)」と「[StorSimple 8000 シリーズの技術的仕様](storsimple-technical-specifications-and-compliance.md)」をご覧ください。


> [!IMPORTANT] 
> バックアップ ターゲットとしての StorSimple デバイスをサポートしているのは、StorSimple 8000 Update 3 以降のみです。


## <a name="architecture-overview"></a>アーキテクチャの概要

以下の表は、デバイス モデルとそのアーキテクチャをまとめたものです。

#### <a name="storsimple-capacities-for-local-and-cloud-storage"></a>StorSimple のローカル ストレージとクラウド ストレージの容量


| ストレージの容量       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| ローカル ストレージの容量 | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| クラウド ストレージの容量 | &gt; 200 TiB\* | &gt; 500 TiB\* |

\*ストレージの容量は、重複除去または圧縮がないことを前提としています。

#### <a name="storsimple-capacities-for-primary-and-secondary-backups"></a>StorSimple のプライマリバックアップとセカンダリ バックアップの容量


| バックアップ シナリオ  | ローカル ストレージの容量                                         | クラウド ストレージの容量                      |
|------------------|----------------------------------------------------------------|---------------------------------------------|
| プライマリ バックアップ   | 高速復旧 (RPO) 用のローカル ストレージに格納される最近のバックアップ。 | バックアップ履歴 (RPO) はクラウドの容量に収まります。 |
| セカンダリ バックアップ | クラウドの容量では、バックアップ データのセカンダリ コピーを格納できます。  |

## <a name="storsimple-as-a-primary-backup-target"></a>プライマリ バックアップ ターゲットとしての StorSimple

このシナリオでは、StorSimple ボリュームが、バックアップの唯一のリポジトリとしてバックアップ アプリケーションに提供されます。 次の図は、すべてのバックアップが、バックアップと復元の両方に StorSimple 階層化ボリュームを使用するソリューション アーキテクチャを表しています。

![プライマリ バックアップ ターゲットとしての StorSimple の論理図](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>プライマリ ターゲット バックアップの論理的ステップ

1.  バックアップ サーバーが対象のバックアップ エージェントに接続され、バックアップ エージェントからデータがバックアップ サーバーに転送されます。

2.  バックアップ サーバーから StorSimple 階層化ボリュームにデータが書き込まれます。

3.  バックアップ サーバーがカタログ データベースを更新し、バックアップ ジョブを完了します。

4.  スナップショット スクリプトで StorSimple のクラウド スナップショット管理 (開始 - 削除) がトリガーされます。

5.  リテンション期間ポリシーに基づき、バックアップ サーバーから期限切れのバックアップが削除されます。

###  <a name="primary-target-restore-logical-steps"></a>プライマリ ターゲットの復元の論理的ステップ

1.  バックアップ サーバーが、ストレージ リポジトリから適切なデータの復元を開始します。

2.  バックアップ エージェントがバックアップ サーバーからデータを受信します。

3.  バックアップ サーバーが復元ジョブを完了します。

## <a name="storsimple-as-a-secondary-backup-target"></a>セカンダリ バックアップ ターゲットとしての StorSimple

このシナリオでは、StorSimple ボリュームを主に使用して主に長期リテンションまたはアーカイブを行います。

次の図は、初回のバックアップと復元が高性能ボリュームをターゲットとしているアーキテクチャを表しています。 これらのバックアップは、指定のスケジュールで StorSimple 階層化ボリュームにコピー、アーカイブされます。

高性能ボリュームに、リテンション期間ポリシー、容量、性能の要件に対応できる十分な領域と性能があることが重要です。

![セカンダリ バックアップ ターゲットとしての StorSimple の論理図](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>セカンダリ ターゲット バックアップの論理的ステップ

1.  バックアップ サーバーが対象のバックアップ エージェントに接続され、バックアップ エージェントからデータがバックアップ サーバーに転送されます。

2.  バックアップ サーバーが高性能ストレージにデータを書き込みます。

3.  バックアップ サーバーがカタログ データベースを更新し、バックアップ ジョブを完了します。

4.  リテンション期間ポリシーに基づき、バックアップ サーバーが StorSimple にバックアップをコピーします。

5.  スナップショット スクリプトが StorSimple のクラウド スナップショット管理 (開始 - 削除) をトリガーします。

6.  リテンション期間ポリシーに基づき、バックアップ サーバーから期限切れのバックアップが削除されます。

### <a name="secondary-target-restore-logical-steps"></a>セカンダリ ターゲットの復元の論理的ステップ

1.  バックアップ サーバーが、ストレージ リポジトリから適切なデータの復元を開始します。

2.  バックアップ エージェントがバックアップ サーバーからデータを受信します。

3.  バックアップ サーバーが復元ジョブを完了します。

## <a name="deploy-the-solution"></a>ソリューションのデプロイ方法

このソリューションのデプロイメントは、3 つの手順で構成されています。ネットワーク インフラストラクチャの準備、StorSimple デバイスのバックアップ ターゲットとしてのデプロイ、Veritas NetBackup のデプロイです。 これらの各手順を以下のセクションで詳しく説明します。

### <a name="configure-the-network"></a>ネットワークの構成方法

Azure クラウドとの統合ソリューションである StorSimple は、Azure クラウドへのアクティブで有効な接続を必要とします。 この接続は、クラウド スナップショット、管理、メタデータの転送、Azure のクラウド ストレージへの古いデータやアクセスの少ないデータの階層化などのオペレーションに使用されます。

ソリューションを最適に実行するには、次のネットワークのベスト プラクティスに従うことをお勧めします。

-   StorSimple の階層と Azure 間の接続は、適切なクオリティ オブ サービス (QoS) をインフラストラクチャ スイッチに適用して RPO/RTO の SLA に合致した帯域幅要件を満たす必要があります。

-   Azure Blob Storage のアクセスの最大待機時間は、80 ミリ秒の範囲内で指定する必要があります。

### <a name="deploy-storsimple"></a>StorSimple のデプロイ方法

StorSimple のデプロイメントのステップ バイ ステップ ガイダンスは、「[オンプレミスの StorSimple デバイスのデプロイ](storsimple-deployment-walkthrough-u2.md)」をご覧ください。

### <a name="deploy-netbackup"></a>NetBackup のデプロイ方法

NetBackup 7.7.x のデプロイメントのステップ バイ ステップ ガイダンスは、「[NetBackup 7.7.x のドキュメント](https://www.veritas.com/support/article.000094423)」をご覧ください。

## <a name="configure-the-solution"></a>ソリューションの構成方法

このセクションでは、構成の例をいくつかご紹介します。 次の例と推奨事項は、最も基本的な実装を示しています。 この実装例は特定のバックアップ要件に直接適用されない場合があります。

### <a name="configure-storsimple"></a>StorSimple の構成方法

| StorSimple のデプロイメント タスク                                                                                                                 | その他のコメント                                                                                                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| オンプレミスの StorSimple デバイスをデプロイする                                                                                                 | サポートされているバージョン: Update 3 以降。                                                                                                                                                                                                                                                                 |
| バックアップ ターゲット モードを有効にする                                                                                                                   | 次のコマンドを使用して有効化/無効化し、ステータスを取得します。 詳細は、「[StorSimple デバイスにリモート接続する](storsimple-remote-connect.md)」をご覧ください。</br> バックアップ モードの有効化: `Set-HCSBackupApplianceMode -enable`</br>  バックアップ モードの無効化: `Set-HCSBackupApplianceMode -disable`</br> バックアップ モード設定の現在のステータス: `Get-HCSBackupApplianceMode` |
| バックアップ データを格納するボリュームの共通ボリューム コンテナーを作成する。   ボリューム コンテナー内のすべてのデータは重複除去される。 | StorSimple のボリューム コンテナーでは、重複除去のドメインを定義します。                                                                                                                                                                                                                                             |
| StorSimple ボリュームを作成する                                                                                                                 | ボリューム サイズがクラウド スナップショットの継続時間に影響を与えるため、予想される使用量に可能な限り近いサイズのボリュームを作成します。 ボリュームのサイズを変更する方法の詳細については「[リテンション期間ポリシー](#retention-policies)」をご覧ください。</br> </br> StorSimple 階層化ボリュームを使用し、[**Use this volume for less frequently accessed archival data] (アクセス頻度の低いアーカイブ データにこのボリュームを使用します**) チェックボックスをオンにします。 </br> ローカル固定ボリュームのみの作成はサポートされていません。|
| すべてのバックアップ ターゲット ボリュームに対し、一意の StorSimple バックアップ ポリシーを作成する。                                                               | ボリュームの整合性グループは、StorSimple のバックアップ ポリシーで定義されます。                                                                                                                                                                                                                                       |
| スナップショットとしてスケジュールを無効にする。                                                                                                    | スナップショットは後処理オペレーションとしてトリガーされます。                                                                                                                                                                                                                                                         |
|                                                                                                     |                                             |



### <a name="configure-host-backup-server-storage"></a>ホスト バックアップ サーバー ストレージの構成方法

次のガイドラインに沿ってホスト バックアップ サーバー ストレージが構成されていることを確認します。  

- (Windows Disk マネージャーで作成された) スパン ボリュームはサポートされていません。
- アロケーション ユニット サイズ 64 KB の NTFS を使用してボリュームをフォーマットします。
- StorSimple ボリュームを NetBackup サーバーに直接マッピングします。 
    - 物理サーバーに iSCSI を使用します。
    - 仮想サーバーにパススルー ディスクを使用します。


## <a name="best-practices-for-storsimple-and-netbackup"></a>StorSimple と NetBackup のベスト プラクティス

次のガイドラインに従って、ソリューションを構成します。

### <a name="operating-system"></a>オペレーティング システム

-   Windows Server の暗号化と、NTFS ファイル システムの重複除去を無効にします。

-   StorSimple ボリュームでの Windows Server の最適化を無効にします。

-   StorSimple ボリュームでの Windows Server のインデックス作成を無効にします。

-   (StorSimple ボリュームに対してではなく) ソース ホストでウィルス対策スキャンを実行します。

-   タスク マネージャーの規定値の[ Windows Server メンテナンス](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx)を無効にします。

    - Windows タスク スケジューラのメンテナンス構成を無効にします。

        または

    - [PSEXEC – Microsoft Sysinternals](https://technet.microsoft.com/sysinternals/bb897553.aspx)をダウンロードします。

      - PSEXEC をダウンロードしたら、管理者として Windows PowerShell を実行し、次を入力します。

            `psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable`

### <a name="storsimple"></a>StorSimple

-   StorSimple デバイスが[Update 3 以降](storsimple-install-update-3.md)に更新されていることを確認します。

-   iSCSI とクラウド トラフィックを分離します。 StorSimple とバックアップ サーバーの間のトラフィックに専用の iSCSI 接続を使用します。

-   StorSimple デバイスが専用のバックアップ ターゲットであることを確認します。 混合ワークロードは、RTO/RPO に影響を与えるため、サポートされていません。

### <a name="netbackup"></a>NetBackup

-   NetBackup データベースは、サーバーにローカルである必要があり、StorSimple ボリューム上に存在してはなりません。

-   DR 用に、NetBackup データベースを StorSimple ボリュームにバックアップします。

-   このソリューションについて、Microsoft では NetBackup の完全バックアップと増分バックアップをサポートしています。 合成バックアップや差分バックアップは使用しないことをお勧めします。

-   バックアップ データ ファイルには、特定のジョブのデータのみ含める必要があります。 たとえば、複数のジョブにまたがってメディアを追加することはできません。

詳細は、[NetBackup のドキュメント](https://www.veritas.com)で最新の NetBackup 設定と要件を実装する方法に関するベスト プラクティスをご確認ください。


## <a name="retention-policies"></a>リテンション期間ポリシー

最も使用されているバックアップ リテンション期間ポリシーは、Grandfather-Father-Son (GFS) 方式です。 この方式では、増分バックアップは毎日実行されます。 完全バックアップは、週単位と月単位で実行されます。 この方式では 6 つの StorSimple 階層化ボリュームを使用します。

-   1 つのボリュームには、週、月、年単位の完全バックアップが格納されます。

-   残りの 5 つのボリュームには、毎日の増分バックアップが格納されます。

次の例では、GFS 方式を使用しています。 この例では、次の条件を前提としています。

-   非重複除去または圧縮データを使用します。

-   完全バックアップはそれぞれ 1 TiB です。

-   毎日の増分バックアップはそれぞれ 500 GiB です。

-   4 週間分のバックアップは 1 か月間保持されます。

-   12 か月分のバックアップは 1 年間保持されます。

-   1 年分のバックアップは 10 年間保持されます。

上記の前提条件に基づき、月単位と年単位の完全バックアップ用に 26 TiB の StorSimple 階層化ボリュームを作成します。 毎日の増分バックアップそれぞれに対し、5 TiB の StorSimple 階層化ボリュームを作成します。

| バックアップ タイプとリテンション | サイズ TiB | GFS 乗数\*                                       | 合計容量 TiB          |
|-----------------------|----------|--------------------------------------------------------|-----------------------------|
| 週単位 - 完全           | 1        | 4                                                      | 4                           |
| 毎日 - 増分     | 0.5      | 20 (1 か月あたり同じ週数をサイクルする) | 12 (追加のクォータに対し 2) |
| 月単位 - 完全          | 1        | 12                                                     | 12                          |
| 年単位 - 完全           | 1        | 10                                                     | 10                          |
| GFS 要件       |          |                                                        | 38                          |
| 追加のクォータ      | 4        |                                                        | GFS 要件合計 42   |

\*GFS 乗数は、データの保護とバックアップ ポリシー遵守を維持するために必要なコピー数を指します。

## <a name="configure-netbackup-storage"></a>NetBackup ストレージの構成方法


1.  NetBackup 管理コンソールで、[デバイス] \>を選択し、ディスク プールの構成ウィザードを開始し\>、[AdvancedDisk]\> を選択し\>、[次へ] をクリックします。

    ![NetBackup 管理コンソールのディスク プールの構成](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

1.  サーバーを選択します。

    ![NetBackup 管理コンソールでサーバーを選択します。](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

1.  [次へ] を選択し、StorSimple ボリュームを選択します。

    ![NetBackup 管理コンソールで使用する StorSimple ディスクを選択します。](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

1.  名前を付け、[次へ] > [次へ] の順に選択し終了します。

    ![NetBackup 管理コンソールの名前と説明の画面](./media/storsimple-configure-backup-target-using-netbackup/nbimage4.png)

    設定を確認したら [完了] をクリックします。

    1.  各割り当ての最後には、前述のベスト プラクティスのセクションで紹介した推奨事項に合わせてストレージ デバイスの設定を変更します。

    2.  StorSimple ボリュームの割り当てが完了するまで手順 1 ～ 4 を繰り返します。

    ![NetBackup 管理コンソールのディスクの構成](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="storsimple-as-a-primary-backup-target"></a>プライマリ バックアップ ターゲットとしての StorSimple

> [!NOTE]
> クラウドに階層化されたバックアップからデータの復元が必要な場合、クラウドのスピードで復元されることにご注意ください。

次の図は、バックアップ ジョブに対する一般的なボリュームのマッピングを示しています。 この場合、週単位のバックアップはすべて「土曜日 - 完全」のディスクにマッピングされ、増分バックアップは月曜日～金曜日の増分ディスクにマッピングされています。 すべてのバックアップと復元は、StorSimple 階層化ボリュームから行われます。

![プライマリ バックアップ ターゲット構成の論理図 ](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

#### <a name="storsimple-as-a-primary-backup-target-grandfather-father-and-son-gfs-schedule-example"></a>プライマリ バックアップ ターゲットとしての StorSimple の Grandfather-Father-Son (GFS) スケジュールの例

| 4 週間、月単位、年単位の GFS ローテーション スケジュール |               |             |
|--------------------------------------------------------------------------|---------------|-------------|
| 頻度/バックアップ タイプ   | 完全          | 増分 (1 ～ 5 日目)  |
| 週単位 (第 1 ～ 4 週)    | 土曜日 | 月曜日～金曜日 |
| 月単位     | 土曜日  |             |
| 年単位      | 土曜日  |             |


### <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>StorSimple ボリュームを NetBackup バックアップ ジョブに割り当てる

次の手順は、NetBackup とターゲット ホストが NetBackup エージェントのガイドラインに従って構成されていることを前提としています。

1.  NetBackup 管理コンソールで、[ポリシー] を選択し、\>[新しいポリシー] を\>右クリックします

    ![NetBackup 管理コンソール画面](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

1.  ポリシー名と [Select a policy name and Use the policy wizard] (ポリシー ウィザードを使用します) を選択します

    ![NetBackup 管理コンソール、新しいポリシー](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

1.  適切なバックアップ タイプ、今回の場合[ファイル システム]、[次へ] の順に選択します

    ![NetBackup 管理コンソール、新しいポリシー](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

1.  その後、[standard] (標準) を選択します

    ![NetBackup 管理コンソール、新しいポリシー](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

1.  ホストを選択し、[detect client operating system] (クライアント オペレーティング システムを検出) チェック ボックスをオンにし、[追加] を選択します

    ![NetBackup 管理コンソール、新しいポリシー](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

1.  [次へ] を選択します

2.  バックアップするドライブを選択します。今回の場合、G:\\ DataChange3 \\を選択します

    ![NetBackup 管理コンソール、新しいポリシー](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

1.  要件に合致したローテーションを選択します

    ![NetBackup 管理コンソール、新しいポリシー](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

1.  [次へ] > [次へ] の順に選択し、完了します。 ポリシーを作成したらスケジュールを変更します。

2.  作成したポリシーを展開し、スケジュールを選択します。

    ![NetBackup 管理コンソール、新しいポリシー](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

1.  [differential-inc] (差分 - 増分) を右クリックし、[新規にコピー]、今回の場合[Mon-inc] (月曜日 - 増分) の順に選択し、[OK] をクリックします

    ![NetBackup 管理コンソール、新しいポリシー スケジュール](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

1.  次に、新しく作成したスケジュールを右クリックし、[変更] を選択します。

2.  [属性] タブで [Override policy storage selection] (ポリシー ストレージの選択を上書きする) チェック ボックスをオンにし、月曜日の増分バックアップが格納されるボリュームを選択します。 今回の場合 [SS1] を選択します。

    ![NetBackup 管理コンソール、スケジュールの変更](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

1.  [Start Window] (開始時間帯) でバックアップの時間帯を選択します。 今回の場合、月曜日の午後 8 時 から午後 9 時までを選択します。

    ![NetBackup 管理コンソール、スケジュールの変更](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

1.  [OK] を選択します。

2.  各増分バックアップに対し手順 11 ～ 15 を繰り返し、適切なボリュームとスケジュールを選択します。

3.  [差分 - 増分] スケジュールを右クリックし削除します。

4.  [差分 - 増分] スケジュールを削除したら、要件に合わせて完全のスケジュールを変更します。

    ![NetBackup 管理コンソール、スケジュールの変更](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

1.  開始時間帯を、今回の場合土曜日の午前 6 時に変更します。

    ![NetBackup 管理コンソール、スケジュールの変更](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

1.  最終的なスケジュールは次のようになります。

    ![NetBackup 管理コンソール、スケジュールの変更](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="storsimple-as-a-secondary-backup-target"></a>セカンダリ バックアップ ターゲットとしての StorSimple

> [!NOTE]
> クラウドに階層化されたバックアップからデータの復元が必要な場合、クラウドのスピードで復元されることにご注意ください。

このモデルでは、一時キャッシュとして機能する (StorSimple 以外の) ストレージ メディアが必要です。 たとえば、領域、I/O、帯域幅に合わせて、RAID ボリュームを使用できます。 RAID 5、50、10 を使用することをお勧めします。

次の図は、一般的な短期リテンションの ローカル ボリューム (サーバーにローカル) と長期リテンションのアーカイブ ボリュームを表しています。 この場合、すべてのバックアップはローカル RAID ボリューム (サーバーにローカル) で実行されます。 これらのバックアップは、アーカイブ ボリュームに定期的に複製、アーカイブされます。 ローカル RAID ボリューム (サーバーにローカル) に、短期リテンション容量と性能要件に対応できる容量があることが重要です。

#### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>セカンダリ バックアップ ターゲットとしての StorSimple の GFS の例

![セカンダリ バックアップ ターゲットとしての StorSimple の論理図 ](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

次の表は、ローカル ディスクと StorSimple ディスク上でバックアップを実行するためのバックアップ構成方法と個別の容量と合計容量の要件をまとめたものです。

#### <a name="backup-configuration-and-capacity-requirements"></a>バックアップの構成と容量要件

| バックアップ タイプとリテンション                    |構成済みストレージ| サイズ (TiB) | GFS 乗数 | 合計容量 (TiB)        |
|----------------------------------------------|-----|----------|----------------|------------------------|
| 第 1 週 (完全と増分) |ローカル ディスク (短期)| 1        | 1              | 1           |
| StorSimple 第 2 ～ 4 週           |StorSimple ディスク (長期) | 1        | 4              | 4                   |
| 月単位 - 完全                                 |StorSimple ディスク (長期) | 1        | 12             | 12                   |
| 年単位 - 完全                               |StorSimple ディスク (長期) | 1        | 1              | 1                   |
|GFS ボリュームのサイズ要件 | |          |                | 18*|

\*合計容量は、StorSimple ディスクの 17 TiB とローカル RAID ボリュームの 1 TiB の合計です。


#### <a name="gfs-example-schedule"></a>GFS スケジュールの例

|週、月、年単位の GFS ローテーション スケジュール|                    |                   |                   |                   |                   |                   |
|--------------------------------------------------------------------------|--------------------|-------------------|-------------------|-------------------|-------------------|-------------------|
| 週                                                                     | 完全               | 増分 (1 日目)        | 増分 (2 日目)        | 増分 (3 日目)        | 増分 (4 日目)        | 増分 (5 日目)        |
| 第 1 週                                                                   | ローカル RAID ボリューム  | ローカル RAID ボリューム | ローカル RAID ボリューム | ローカル RAID ボリューム | ローカル RAID ボリューム | ローカル RAID ボリューム |
| 第 2 週                                                                   | StorSimple 第 2 ～ 4 週 |                   |                   |                   |                   |                   |
| 第 3 週                                                                   | StorSimple 第 2 ～ 4 週 |                   |                   |                   |                   |                   |
| 第 4 週                                                                   | StorSimple 第 2 ～ 4 週 |                   |                   |                   |                   |                   |
| 月単位                                                                  | StorSimple 月単位 |                   |                   |                   |                   |                   |
| 年単位                                                                   | StorSimple 年単位  |                   |                   |                   |                   |                   |


### <a name="assign-storsimple-volumes-to-netbackup-archiveduplication-job"></a>StorSimple ボリュームを NetBackup アーカイブ/複製ジョブに割り当てる

NetBackup よりストレージとメディア管理に多くのオプションが提供されているため、SLP 要件を適切に評価するためには、Veritas または自社の NetBackup アーキテクトにご相談ください。

1.  初期のディスク プールが定義されたら、3 つのストレージ ライフサイクル ポリシーの定義に進みます。

    1.  ローカル RAID ボリューム

    2.  StorSimple 第 2 ～ 4 週

    3.  StorSimple 月単位 - 完全

    4.  StorSimple 年単位- 完全

    管理コンソールの [ストレージ] で [ストレージ ライフサイクル ポリシー] を選択し [新しいストレージ ライフサイクル ポリシー] を選択します

    [NetBackup 管理コンソール、ストレージ ライフサイクル ポリシー](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

1.  名前を選択し [追加] をクリックします

    ![NetBackup 管理コンソール、ストレージ ライフサイクル ポリシー](./media/storsimple-configure-backup-target-using-netbackup/nbimage21.png)

1.  [プロパティ] タブで [バックアップ]、[Destination Storage] (宛先ストレージ)、適切なリテンション期間、[OK] を選択します。 今回の場合、LocalRAIDVolume (ローカル RAID ボリューム) と 1 週間のリテンション期間を選択します。

    ![NetBackup 管理コンソール、ストレージ ライフサイクル ポリシー](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

1.  これにより最初のバックアップ オペレーション/リポジトリが定義されます

2.  以前のオペレーションを強調表示し、[追加] をクリックし、[宛先ストレージ] と適切なリテンション期間を選択します。 今回の場合、LocalRAIDVolume (ローカル RAID ボリューム) で StorSimple 第 2 ～ 4 週と、1 か月のリテンション期間を選択します

    ![NetBackup 管理コンソール、新しいストレージ ライフサイクル ポリシー](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

1.  以前のオペレーションを強調表示し、[追加] を選択します。これで 1 年間の月単位のバックアップが追加されます。

    ![NetBackup 管理コンソール、ストレージ ライフサイクル ポリシーの変更](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

1.  適切な SLP - リテンション期間ポリシーになるまで手順 5 ～ 6 を繰り返します。

    ![NetBackup 管理コンソール、ストレージ ライフサイクル ポリシー](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

1.  適切な SLP - リテンション期間ポリシーの定義が完了したら、[ポリシー] で「プライマリ バックアップ ターゲットとしての StorSimple」のセクションで説明されているようにバックアップ ポリシーを定義します。

1.  [スケジュール] で [完全] を選択し、右クリックして [変更] を選択します。

    ![NetBackup 管理コンソール、スケジュールの変更](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

1.  [Overwrite policy storage selection] (ポリシー ストレージの選択を上書きする) を選択し、手順 1 ～ 8 で作成した SLP を選択します

    ![NetBackup 管理コンソール、スケジュールの変更](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

1.  [OK] をクリックし、次に増分バックアップ スケジュールに対し同じ手順を繰り返します。

    ![NetBackup 管理コンソール、スケジュールの変更](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)

1.  [OK] をクリックし、次に増分バックアップ スケジュールに対し同じ手順を繰り返します。

| バックアップ タイプとリテンション | サイズ TiB | GFS 乗数\*                                       | 合計容量 TiB          |
|-----------------------|----------|--------------------------------------------------------|-----------------------------|
| 週単位 - 完全           | 1        | 4                                                      | 4                           |
| 毎日 - 増分     | 0.5      | 20 (サイクルは 1 か月の週数と同じ) | 12 (追加のクォータに対し 2) |
| 月単位 - 完全          | 1        | 12                                                     | 12                          |
| 年単位 - 完全           | 1        | 10                                                     | 10                          |
| GFS 要件       |          |                                                        | 38                          |
| 追加のクォータ      | 4        |                                                        | GFS 要件合計 42   |
\*GFS 乗数は、データの保護とバックアップ ポリシー遵守を維持するために必要なコピー数を指します。

### <a name="storsimple-cloud-snapshots"></a>StorSimple クラウド スナップショット

StorSimple クラウド スナップショットは、StorSimple デバイスに存在するデータを保護するものです。 オフサイト設備への転送テープと同じ機能を持ち、Azure の geo 冗長ストレージ (GRS) を使用する場合、複数のサイトへの転送テープと同じ機能を持ちます。 障害時にデバイスの復旧が必要な場合、別の StorSimple デバイスをオンラインにしフェールオーバーを実行できます。 次のフェールオーバーでは、最近のクラウド スナップショットからデータに (クラウドのスピードで) アクセスできます。

次のセクションでは、バックアップの後処理中にショート スクリプトを作成し、StorSimple クラウド スナップショットをトリガーし、削除する方法を説明します。

> [!NOTE]
> 手動またはプログラムによって作成されたスナップショットは、StorSimple スナップショットの有効期間ポリシーに準拠しません。 手動またはプログラムによって削除する必要があります。

### <a name="start-delete-cloud-snapshots-with-a-script"></a>スクリプトを使用したクラウド スナップショットの開始 - 削除

> [!NOTE]
> StorSimple スナップショットを削除する前に、コンプライアンスとデータ リテンション期間の影響を慎重に評価します。 バックアップ後スクリプトを実行する方法の詳細は、[NetBackup ドキュメント](https://www.veritas.com/support/article.000094423)をご覧ください。

#### <a name="backup-lifecycle"></a>バックアップ ライフサイクル

![バックアップ ライフサイクルの図](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

#### <a name="requirements"></a>要件:

-   スクリプトを実行するサーバーは、Azure クラウドにアクセスできる必要があります。

-   ユーザー アカウントは、必要な権限を持っていなければなりません。

-   関連する StorSimple ボリュームを持つ StorSimple バックアップ ポリシーは構成されており、無効になっている必要があります。

-   StorSimple リソース名、登録キー、デバイス名、バックアップ ポリシー ID が必要です。

#### <a name="steps"></a>手順:

1.  [Azure PowerShell をインストールします](/powershell-install-configure/)。

2.  [発行設定とサブスクリプション情報をダウンロード、インポートします。](https://msdn.microsoft.com/library/dn385850.aspx)

3.  Azure クラシック ポータルで、StorSimple Manager サービス用のリソース名と[登録キーを取得します](storsimple-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key)。

4.  スクリプトを実行するサーバーで Windows PowerShell を管理者として実行します。 次のコマンドを入力します。

    -   `Get-AzureStorSimpleDeviceBackupPolicy –DeviceName <device name>`

    バックアップ ポリシー ID をメモします。

5.  メモ帳で新しい Windows PowerShell スクリプトを作成し、Azure 発行設定を保存した場所と同じ場所に保存します。 たとえば、「 `C:\CloudSnapshot\StorSimpleCloudSnapshot.ps1`」のように入力します。

    次のコード スニペットをコピーして貼り付けます。

    ```powershell
    Import-AzurePublishSettingsFile "c:\\CloudSnapshot Snapshot\\myAzureSettings.publishsettings"
    Disable-AzureDataCollection
    $ApplianceName = <myStorSimpleApplianceName>
    $RetentionInDays = 20
    $RetentionInDays = -$RetentionInDays
    $Today = Get-Date
    $ExpirationDate = $Today.AddDays($RetentionInDays)
    Select-AzureStorSimpleResource -ResourceName "myResource" –RegistrationKey
    Start-AzureStorSimpleDeviceBackupJob –DeviceName $ApplianceName -BackupType CloudSnapshot -BackupPolicyId <BackupId> -Verbose
    $CompletedSnapshots =@()
    $CompletedSnapshots = Get-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName
    Write-Host "The Expiration date is " $ExpirationDate
    Write-Host

    ForEach ($SnapShot in $CompletedSnapshots)
    {
        $SnapshotStartTimeStamp = $Snapshot.CreatedOn
        if ($SnapshotStartTimeStamp -lt $ExpirationDate)

        {
            $SnapShotInstanceID = $SnapShot.InstanceId
            Write-Host "This snpashotdate was created on " $SnapshotStartTimeStamp.Date.ToShortDateString()
            Write-Host "Instance ID " $SnapShotInstanceID
            Write-Host "This snpashotdate is older and needs to be deleted"
            Write-host "\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#"
            Remove-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName -BackupId $SnapShotInstanceID -Force -Verbose
        }
    }
    ```

6.  NetBackup ジョブ オプションのプリ/ポスト コマンドを編集し、NetBackup のバックアップ ジョブにスクリプトを追加します。

> [!NOTE]
> StorSimple クラウド スナップショット バックアップ ポリシーは、毎日のバックアップ ジョブの最後に、後処理スクリプトとして実行することをお勧めします。 RPO/RTO に適合するようバックアップ アプリケーション環境をバックアップ、復元する方法に関する詳細は、自社のバックアップ アーキテクトにご相談ください。

## <a name="storsimple-as-a-restore-source"></a>復元元としての StorSimple


StorSimple からの復元動作は、すべてのブロック ストレージ デバイスからの復元と類似しています。 クラウドに階層化されたデータを復元する場合、復元はクラウドのスピードで実行されます。 ローカル データについては、復元はデバイスのローカル ディスクのスピードで実行されます。 復元を実行する方法に関する詳細は、[NetBackup ドキュメント](https://www.veritas.com/support/article.000094423)をご覧になり、NetBackup の復元のベスト プラクティスをご確認ください。

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple のフェールオーバーと障害復旧

> [!NOTE]
> バックアップ ターゲットのシナリオについて、StorSimple Cloud Appliance は復元先としてサポートされていません。

障害はさまざまな要因により発生します。 次の表は、よくある障害復旧 (DR) シナリオをまとめたものです。

| シナリオ                                                                    | 影響                                             | 復旧方法                                                                                                                                                                               | メモ                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|-----------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| StorSimple デバイスの不具合                                                | バックアップと復元のオペレーションが中断されます。 | 不具合のあるデバイスを交換し、[StorSimple フェールオーバーと障害復旧](storsimple-device-failover-disaster-recovery.md)を実行します | デバイスの復旧後に復元を実行する必要がある場合、完全なデータのワーキング セットはクラウドから新しいデバイスに送られます。これにより、すべてのオペレーションがクラウドのスピードで実行されます。 このインデックスとカタログの再スキャン プロセスにより、すべてのバックアップ セットがスキャンされ、クラウドの階層からローカル デバイスの階層に戻されます。これには時間がかかる場合があります。                                                 |
| NetBackup サーバーの不具合                                              | バックアップと復元のオペレーションが中断されます。 | バックアップ サーバーを再構築し、データ ベースの復元を実行します                                                                                                                                       | NetBackup サーバーは、DR サイトで再構築または復旧する必要があります。 データベースは、最新の時点に復元する必要があります。 復元された NetBackup データベースが最新のバックアップ ジョブと同期しない場合、インデックスとカタログの作成が必要です。 このインデックスとカタログの再スキャン プロセスにより、すべてのバックアップ セットはスキャンされ、クラウドの階層からローカル デバイスの階層に戻されます。 これにはさらに時間がかかります。 |
| バックアップ サーバーと StorSimple 両方の損失を招くサイトの不具合 | バックアップと復元のオペレーションが中断されます。 | StorSimple を最初に復旧し、次に NetBackup を復旧します。                                                                                                                                          | StorSimple を最初に復旧し、次に NetBackup を復旧します。                                                                 デバイスの復旧後に復元を実行する必要がある場合、完全なデータのワーキング セットがクラウドから新しいデバイスに送られます。 これにより、すべてのオペレーションはクラウドのスピードで実行されます。|

## <a name="references"></a>参照

この記事では次のドキュメントを参照しています。

- [StorSimple デバイスの MPIO の構成](storsimple-configure-mpio-windows-server.md)

- [ストレージ シナリオ: 仮想プロビジョニング (Storage scenarios: Thin provisioning)](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)

- [GPT ドライブを使用する (Using GPT drives)](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)

- [共有フォルダーのシャドウ コピーを有効にして構成する](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>次のステップ

詳細情報:

- [バックアップ セットからの StorSimple ボリュームの復元](storsimple-restore-from-backup-set-u2.md)方法
- [StorSimple デバイスのフェールオーバーと障害復旧](storsimple-device-failover-disaster-recovery.md)の実行方法



<!--HONumber=Dec16_HO2-->


