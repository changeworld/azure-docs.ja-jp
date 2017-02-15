---
title: "Veritas Backup Exec を使用して StorSimple を構成する | Microsoft Docs"
description: "Veritas Backup Exec を使用して StorSimple バックアップ ターゲットを構成する方法を説明します。"
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
ms.sourcegitcommit: b7c6a9ffb2c598048bf0547420b1e7afac2c3e74
ms.openlocfilehash: 7a2fa4ccac6b2eda935477034c5f58a0ed90da37

---

# <a name="configure-storsimple-with-veritas-backup-exec8482"></a>Veritas Backup Exec&#8482; を使用して StorSimple を構成する

## <a name="overview"></a>概要

Microsoft Azure StorSimple は、複雑なデータの急増に対応できるハイブリッド クラウド ストレージ ソリューションです。 このソリューションでは Azure クラウド ストレージ アカウントがオンプレミス ソリューションの拡張機能として使用され、オンプレミス ストレージとクラウド ストレージをまたがってデータが自動的に階層化されます。

この記事では、Veritas Backup Exec と StorSimple の統合について取り上げ、両方のソリューションを統合するためのベスト プラクティスについて説明します。 StorSimple と最適に統合するために Veritas Backup Exec を構成する方法に関する推奨事項もご紹介します。 Microsoft は、お客様のバックアップ要件と SLA に合わせて Veritas Backup Exec を最適に構成する方法に関し、Veritas のベスト プラクティス、バックアップ アーキテクト、管理者に従います。

この記事は、構成手順の概要と主要な概念を説明するものであり、構成またはインストールの方法をステップ バイ ステップで説明するガイドではありません。 基本的なコンポーネントとインフラストラクチャが正常に動作しており、ここで説明する概念をサポートする準備が整っていることを前提としています。

## <a name="why-storsimple-as-a-backup-target"></a>StorSimple をバックアップ ターゲットとして使用する理由

StorSimple がバックアップ ターゲットとしてふさわしい理由は、次の通りです。

-   何も変更することなく、高速バックアップ先となるバックアップ アプリケーション用の標準的なローカル ストレージを提供します。 最新のバックアップからのすばやい復元にも使用できます。

-   StorSimple のクラウド階層化は費用効果の高い Microsoft Azure クラウド ストレージとシームレスに統合されます。

-   障害復旧のためのオフサイト ストレージを自動的に提供します。


## <a name="target-audience"></a>対象読者

この記事の対象読者は、ストレージ、Windows Server 2012 R2、イーサネット、クラウド サービス、Veritas Backup Exec に関する知識を持つ、バックアップ管理者、ストレージ管理者、ストレージ アーキテクトです。

## <a name="supported-versions"></a>サポートされているバージョン

サポートされているバージョンについては、次をご覧ください。

-   [Backup Exec バージョン 16 以降](http://backupexec.com/compatibility)

-   [StorSimple Update 3 以降](storsimple-overview.md#storsimple-workload-summary)。

## <a name="key-concepts"></a>主要な概念

その他のストレージ ソリューションと同様、ソリューションのストレージ性能、SLA、変更率、容量増加の必要性について慎重に評価することが不可欠です。 クラウドの階層を導入することで、お客様のクラウドへのアクセス時間と処理能力が、StorSimple のジョブを実行する能力に根本的な影響を与えます。

StorSimple は、適切に定義されたデータのワーキング セット (ホットデータ) 上で動作するアプリケーションにストレージを提供するよう設計されています。 このモデルでは、データのワーキング セットはローカルの階層に格納され、残りの非ワーキング/クラウド/アーカイブ セットはクラウドに格納されます。 このモデルを示したのが次のグラフです。 ほぼ平らな緑の線は、StorSimple デバイスのローカル階層に格納されているデータを表しています。 赤い線は、すべての階層で StorSimple ソリューションに格納されているデータの総量を表しています。 平らな緑の線と右上がりの曲線を描く赤い線の間の空間は、クラウドに格納されているデータの総量を表しています。

**StorSimple の階層化**
![Storsimple の階層化の図](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

このグラフを見ると、StorSimple をバックアップ ターゲットとして稼働させることが適切であることがわかります。 StorSimple では次のことができます。

-   ローカルのデータのワーキングセットから最も頻繁に復元を実行します。

-   オフサイトの障害復旧と復元の頻度が低いデータにクラウドを使用します。

## <a name="storsimple-benefits"></a>StorSimple の利点

StorSimple は、オンプレミスとクラウドのストレージへのシームレスなアクセスを生かして、Microsoft Azure とシームレスに統合するオンプレミス ソリューションを提供します。

StorSimple は、ソリッド ステート デバイス (SSD) ストレージ、シリアル接続 SCSI (SAS) ストレージと Azure Storage を含むオンプレミス デバイス間で自動的に階層化します。 自動的に階層化されることで、頻繁にアクセスされるデータは SSD 階層と SAS 階層でローカルに保持され、アクセス頻度の低いデータは Azure Storage に移動されます。

StorSimple には次の利点があります。

-   クラウドを使用してこれまでにない重複除去レベルを実現するための一意の重複除去と圧縮アルゴリズム

-   高可用性

-   Azure の geo レプリケーションを使用した Geo レプリケーション

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
| プライマリ バックアップ   | 高速復旧 (RPO) 用のローカル ストレージに格納される最近のバックアップ | バックアップ履歴 (RPO) はクラウドの容量に収まります |
| セカンダリ バックアップ | クラウドの容量では、バックアップ データのセカンダリ コピーを格納できます  |

## <a name="storsimple-as-a-primary-backup-target"></a>プライマリ バックアップ ターゲットとしての StorSimple

このシナリオでは、StorSimple ボリュームが、バックアップの唯一のリポジトリとしてバックアップ アプリケーションに提供されます。 次の図は、すべてのバックアップが、バックアップと復元の両方に StorSimple 階層化ボリュームを使用するソリューション アーキテクチャを表しています。

![プライマリ バックアップ ターゲットとしての StorSimple の論理図](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

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

![セカンダリ バックアップ ターゲットとしての StorSimple の論理図](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

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

このソリューションのデプロイメントは、3 つの手順で構成されています。ネットワーク インフラストラクチャの準備、StorSimple デバイスのバックアップ ターゲットとしてのデプロイ、Veritas Backup Exec のデプロイです。 これらの各手順を以下のセクションで詳しく説明します。

### <a name="configure-the-network"></a>ネットワークの構成方法

Azure クラウドとの統合ソリューションである StorSimple は、Azure クラウドへのアクティブで有効な接続を必要とします。 この接続は、クラウド スナップショット、管理、メタデータの転送、Azure のクラウド ストレージへの古いデータやアクセスの少ないデータの階層化などのオペレーションに使用されます。

ソリューションを最適に実行するには、次のネットワークのベスト プラクティスに従うことをお勧めします。

-   StorSimple の階層と Azure 間の接続は、適切なクオリティ オブ サービス (QoS) をインフラストラクチャ スイッチに適用して RPO/RTO の SLA に合致した帯域幅要件を満たす必要があります。

-   Azure Blob Storage のアクセスの最大待機時間は、80 ミリ秒の範囲内で指定する必要があります。

### <a name="deploy-storsimple"></a>StorSimple のデプロイ方法

StorSimple のデプロイメントのステップ バイ ステップ ガイダンスは、「[オンプレミスの StorSimple デバイスのデプロイ](storsimple-deployment-walkthrough-u2.md)」をご覧ください。

### <a name="deploy-veritas-backup-exec"></a>Veritas Backup Exec のデプロイ方法

Veritas Backup Exec インストール時のベスト プラクティスについては、「[Backup Exec のインストールのベスト プラクティス](https://www.veritas.com/support/en_US/article.000068207)」をご覧ください。

## <a name="configure-the-solution"></a>ソリューションの構成方法

このセクションでは、構成の例をいくつかご紹介します。 次の例と推奨事項は、最も基本的な実装を示しています。 この実装例は特定のバックアップ要件に直接適用されない場合があります。

### <a name="configure-storsimple"></a>StorSimple の構成方法

| StorSimple のデプロイメント タスク                                                                                                                 | その他のコメント                                                                                                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| オンプレミスの StorSimple デバイスのデプロイ。                                                                                                | サポートされているバージョン: Update 3 以降                                                                                                                                                                                                                                                                 |
| バックアップ ターゲット モードを有効にする。                                                                                                                   | 次のコマンドを使用して有効化/無効化し、ステータスを取得します。 詳細は、「[StorSimple デバイスにリモート接続する](storsimple-remote-connect.md)」をご覧ください。</br> バックアップ モードの有効化: `Set-HCSBackupApplianceMode -enable`</br>  バックアップ モードの無効化: `Set-HCSBackupApplianceMode -disable`</br> バックアップ モード設定の現在のステータス: `Get-HCSBackupApplianceMode` |
| バックアップ データを格納するボリュームの共通ボリューム コンテナーを作成する。   ボリューム コンテナー内のすべてのデータは重複除去される。 | StorSimple のボリューム コンテナーでは、重複除去のドメインを定義します。                                                                                                                                                                                                                                             |
| StorSimple ボリュームを作成する                                                                                                                 | ボリューム サイズがクラウド スナップショットの継続時間に影響を与えるため、予想される使用量に可能な限り近いサイズのボリュームを作成します。 ボリュームのサイズを変更する方法の詳細については「[リテンション期間ポリシー](#retention-policies)」をご覧ください。</br> </br> StorSimple 階層化ボリュームを使用し、[**Use this volume for less frequently accessed archival data] (アクセス頻度の低いアーカイブ データにこのボリュームを使用します**) チェックボックスをオンにします。 </br> ローカル固定ボリュームのみの作成はサポートされていません。|
| すべてのバックアップ ターゲット ボリュームに対し、一意の StorSimple バックアップ ポリシーを作成する。                                                               | ボリュームの整合性グループは、StorSimple のバックアップ ポリシーで定義されます。                                                                                                                                                                                                                                       |
| スナップショットとしてスケジュールを無効にする。                                                                                                    | スナップショットは後処理オペレーションとしてトリガーされます。                                                                                                                                                                                                                                                         |
|                                                                                                     |                                             |




### <a name="configure-host-backup-server-storage"></a>ホスト バックアップ サーバー ストレージの構成方法

次のガイドラインに沿ってホスト バックアップ サーバー ストレージが構成されていることを確認します。  

- (Windows Disk マネージャーで作成された) スパン ボリュームはサポートされていません。
- アロケーション サイズ 64 KB の NTFS を使用してボリュームをフォーマットします。
- StorSimple ボリュームを 「Backup Exec」 サーバーに直接マッピングします。
    - 物理サーバーの場合 iSCSI を使用します。
    - 仮想サーバーにパススルー ディスクを使用します。


## <a name="best-practices-for-storsimple-and-veritas-backup-exec"></a>StorSimple と Veritas Backup Exec のベスト プラクティス

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

### <a name="veritas-backup-exec"></a>Veritas Backup Exec

-   Veritas Backup Exec は、サーバーのローカル ドライブにインストールする必要があります。StorSimple ボリュームにはインストールできません。

-   Veritas Backup Exec ストレージの**同時書き込み操作**を最大値に設定します。

    -   Veritas Backup Exec ストレージの**ブロックとバッファー サイズ**は、512 KB に設定する必要があります。

    -   Veritas Backup Exec ストレージの**バッファー読み取りと書き込み**を有効にする必要があります。

-   Veritas Backup Exec の完全バックアップと増分バックアップはサポートされていますが、合成バックアップと差分バックアップは推奨されません。

-   バックアップ データ ファイルには、特定のジョブのデータのみ含める必要があります。 たとえば、複数のジョブにまたがってメディアを追加することはできません。

-   ジョブの検証を無効にします。 必要に応じて、最近のバックアップ ジョブの後に検証をスケジュールします。 このジョブがバックアップの時間帯に影響を与えることを理解しておくことが重要です。

-   **[ストレージ] > [ディスク] > [詳細] > [プロパティ]** の順に進み、**[Pre-allocate disk space]** (ディスクの領域を事前割り当てする) を無効にします。

最新の Veritas Backup Exec 設定と要件を実装する方法のベスト プラクティスについては、[www.veritas.com](https://www.veritas.com) をご覧ください。

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

## <a name="configure-veritas-backup-exec-storage"></a>Veritas Backup Exec ストレージの構成方法

1.  Veritas Backup Exec 管理コンソールで、**[ストレージ] &gt; [ストレージの構成] &gt; [Disk-Based Storage]** (ディスク ベースのストレージ) の順に選択します。 **[次へ]&gt; **をクリックします。

    ![Veritas Backup Exec 管理コンソール、ストレージの構成画面](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

1.  **[ディスク ストレージ]** をクリックし、次のページに進みます。

    ![Veritas Backup Exec 管理コンソール、ストレージの選択画面](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

1.  「Saturday Full」(土曜日・完全) など、内容がわかるような名前と説明を入力します。 次のページに進みます。

    ![Veritas Backup Exec 管理コンソール、名前と説明の画面](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

1.  該当するディスクを選択し、**[次へ]&gt; **をクリックします。 今回の場合「D:」を選択しています。

    ![Veritas Backup Exec 管理コンソール、ストレージ ディスクの選択画面](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

1.  書き込み操作の数字を 16 に増やし、**[次へ]&gt; ** をクリックします。

    ![Veritas Backup Exec 管理コンソール、同時書き込み操作の設定画面 ](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

1.  設定を確認し、**[完了]**をクリックします。

    ![Veritas Backup Exec 管理コンソール、ストレージの構成のまとめ画面](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

1.  各割り当ての最後には、前述のベスト プラクティスのセクションで紹介した推奨設定に合うようストレージ デバイスの設定を変更します。

    ![Veritas Backup Exec 管理コンソール、ストレージ デバイスの設定画面](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

1.  StorSimple ボリュームの Veritas Backup Exec への割り当てが完了するまで、すべての手順を繰り返します。

## <a name="storsimple-as-a-primary-backup-target"></a>プライマリ バックアップ ターゲットとしての StorSimple

> [!NOTE]
> クラウドに階層化されたバックアップからデータの復元が必要な場合、クラウドのスピードで復元されることにご注意ください。

次の図は、バックアップ ジョブに対する一般的なボリュームのマッピングを示しています。 この場合、週単位のバックアップはすべて「土曜日 - 完全」のディスクにマッピングされ、増分バックアップは月曜日～金曜日の増分ディスクにマッピングされています。 すべてのバックアップと復元は、StorSimple 階層化ボリュームから行われます。

![プライマリ バックアップ ターゲット構成の論理図 ](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

#### <a name="storsimple-as-a-primary-backup-target-grandfather-father-and-son-gfs-schedule-example"></a>プライマリ バックアップ ターゲットとしての StorSimple の Grandfather-Father-Son (GFS) スケジュールの例

| 4 週間、月単位、年単位の GFS ローテーション スケジュール |               |             |
|--------------------------------------------------------------------------|---------------|-------------|
| 頻度/バックアップ タイプ   | 完全          | 増分 (1 ～ 5 日目)  |
| 週単位 (第 1 ～ 4 週)    | 土曜日 | 月曜日～金曜日 | 
| 月単位     | 土曜日  |             |
| 年単位      | 土曜日  |             |


### <a name="assigning-storsimple-volumes-to-a-veritas-backup-exec-backup-job"></a>StorSimple ボリュームを Veritas Backup Exec バックアップ ジョブに割り当てる。

次の手順は、Backup Exec とターゲット ホストが Backup Exec エージェントのガイドラインに従って構成されていることを前提としています。

1.  Veritas Backup Exec 管理コンソールで、**[ホスト] &gt; [バックアップ] &gt; [ディスクへのバックアップ]&gt;** の順に移動します。

    ![Veritas Backup Exec 管理コンソールで、[ホスト] > [バックアップ] >[ディスクへのバックアップ] の順に選択します。](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

1.  [Backup Definition Properties] (バックアップ定義のプロパティ) ウィンドウで、**[編集]** (バックアップの下) を選択します。

    ![[バックアップ定義のプロパティ] ウィンドウで [編集] を選択します。](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

1.  完全バックアップと増分バックアップを、RPO/RTO 要件と Veritas のベスト プラクティスに合うよう構成します。

2.  [バックアップ オプション] ウィンドウで、**[ストレージ]** を選択します。

    ![[バックアップ オプション] の [ストレージ] タブ](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

1.  該当する StorSimple ボリュームをバックアップ スケジュールに割り当てます。

    > [!NOTE]
    > **[圧縮]** と **[暗号化の種類]** は、**[なし]** に設定します。

2.  **[検証]** で、**[Do not verify data for this job]** (このジョブのデータを検証しない) を選択します。検証することで StorSimple 階層化に影響を与える場合があります。

    > [!NOTE]
    > 最適化、インデックス作成とバックグラウンドの検証は、StorSimple 階層化に悪影響を与えます。

    ![バックアップ オプションの検証設定](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

1.  要件を満たすよう残りのバックアップ オプションを構成したら、**[OK]** を選択し完了します。

## <a name="storsimple-as-a-secondary-backup-target"></a>セカンダリ バックアップ ターゲットとしての StorSimple

> [!NOTE]
> クラウドに階層化されたバックアップからデータの復元が必要な場合、クラウドのスピードで復元されることにご注意ください。

このモデルでは、一時キャッシュとして機能する (StorSimple 以外の) ストレージ メディアが必要です。 たとえば、領域、I/O、帯域幅に合わせて、RAID ボリュームを使用できます。 RAID 5、50、10 を使用することをお勧めします。

次の図は、一般的な短期リテンションの ローカル ボリューム (サーバーにローカル) と長期リテンションのアーカイブ ボリュームを表しています。 この場合、すべてのバックアップはローカル RAID ボリューム (サーバーにローカル) で実行されます。 これらのバックアップは、アーカイブ ボリュームに定期的に複製、アーカイブされます。 ローカル RAID ボリューム (サーバーにローカル) に、短期リテンション容量と性能要件に対応できる容量があることが重要です。

#### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>セカンダリ バックアップ ターゲットとしての StorSimple の GFS の例

![セカンダリ バックアップ ターゲットとしての StorSimple の論理図 ](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

次の表は、ローカル ディスクと StorSimple ディスク上でバックアップを実行するためのバックアップ構成方法、個別の容量と合計容量の要件をまとめたものです。

#### <a name="backup-configuration-and-capacity-requirements"></a>バックアップの構成と容量要件

| バックアップ タイプとリテンション                    |構成済みストレージ| サイズ (TiB) | GFS 乗数 | 合計容量 (TiB)        |
|----------------------------------------------|-----|----------|----------------|------------------------|
| 第 1 週 (完全と増分) |ローカル ディスク (短期)| 1        | 1              | 1           |
| StorSimple 第 2 ～ 4 週           |StorSimple ディスク (長期) | 1        | 4              | 4                   |
| 月単位 - 完全                                 |StorSimple ディスク (長期) | 1        | 12             | 12                   |
| 年単位 - 完全                               |StorSimple ディスク (長期) | 1        | 1              | 1                   |
|GFS ボリュームのサイズ要件 | |          |                | 18*|

\* 合計容量は、StorSimple ディスクの 17 TiB とローカル RAID ボリュームの 1 TiB の合計です。


#### <a name="gfs-example-schedule"></a>GFS スケジュールの例

|週、月、年単位の GFS ローテーション スケジュール|                    |                   |                   |                   |                   |                   |
|--------------------------------------------------------------------------|--------------------|-------------------|-------------------|-------------------|-------------------|-------------------|
| 頻度                                                                     | 完全               | 増分 (1 日目)        | 増分 (2 日目)        | 増分 (3 日目)        | 増分 (4 日目)        | 増分 (5 日目)        |
| 第 1 週                                                                   | ローカル RAID ボリューム  | ローカル RAID ボリューム | ローカル RAID ボリューム | ローカル RAID ボリューム | ローカル RAID ボリューム | ローカル RAID ボリューム |
| 第 2 週                                                                   | StorSimple 第 2 ～ 4 週 |                   |                   |                   |                   |                   |
| 第 3 週                                                                   | StorSimple 第 2 ～ 4 週 |                   |                   |                   |                   |                   |
| 第 4 週                                                                   | StorSimple 第 2 ～ 4 週 |                   |                   |                   |                   |                   |
| 月単位                                                                  | StorSimple 月単位 |                   |                   |                   |                   |                   |
| 年単位                                                                   | StorSimple 年単位  |                   |                   |                   |                   |                   |


### <a name="assign-storsimple-volumes-to-backup-exec-archivededuplication-job"></a>StorSimple ボリュームを Backup Exec アーカイブ/複製ジョブに割り当てる

1.  RAID ボリュームをプライマリ バックアップ ターゲットとして使用するよう初回バックアップ ジョブを構成したら、Veritas Backup Exec 管理コンソールに移動します。 StorSimple ボリュームにアーカイブするジョブを選択し、右クリックして**[バックアップ定義のプロパティ]** を選びます。 **[編集]**をクリックします。

    ![Backup Exec コンソール、バックアップ定義のプロパティ タブ](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

1.  **[Add Stage]** (ステージを追加) を選択し、ドロップダウン リストから**[Duplicate to Disk]** (ディスクに複製) を選択します。 **[編集]**をクリックします。

    ![Backup Exec コンソール、バックアップ定義の追加 タブ](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

2.  **[Duplicate Options]** (複製オプション) で、該当する**ソース**と**スケジュール**を選びます。

    ![Backup Exec コンソール、バックアップ定義のプロパティ、複製オプション](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

1.  **[ストレージ]** のドロップダウン リストからアーカイブ ジョブがデータを格納する StorSimple ボリュームを選択します。 今回の場合、ソースを「**完全**」に、ターゲットを「StorSimple 完全アーカイブ ボリューム」に設定しています。

    ![Backup Exec コンソール、バックアップ定義のプロパティ、複製オプション](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

1.  **[検証]** に移動し、**[Do not verify data for this job]** (このジョブのデータを検証しない) を選択します。

    ![Backup Exec コンソール、バックアップ定義のプロパティ、複製オプション](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

1.  **[OK]**をクリックします。

    ![Backup Exec コンソール、バックアップ定義のプロパティ、複製オプション](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

1.  **[バックアップ]** の欄で新しいステージを追加します。 ソースに「増分」を、ターゲットに増分バックアップ ジョブがアーカイブされる StorSimple ボリュームを選択します。 以上の手順を繰り返します。

## <a name="storsimple-cloud-snapshots"></a>StorSimple クラウド スナップショット

StorSimple クラウド スナップショットは、StorSimple デバイスに存在するデータを保護するものです。 オフサイト設備への転送テープと同じ機能を持ち、Azure の geo 冗長ストレージ (GRS) を使用する場合、複数のサイトへの転送テープと同じ機能を持ちます。 障害時にデバイスの復旧が必要な場合、別の StorSimple デバイスをオンラインにしフェールオーバーを実行できます。 次のフェールオーバーでは、最近のクラウド スナップショットからデータに (クラウドのスピードで) アクセスできます。

次のセクションでは、バックアップの後処理中にショート スクリプトを作成し、StorSimple クラウド スナップショットをトリガーし、削除する方法を説明します。

> [!NOTE]
> 手動またはプログラムによって作成されたスナップショットは、StorSimple スナップショットの有効期間ポリシーに準拠しません。 手動またはプログラムによって削除する必要があります。

### <a name="start-and-delete-cloud-snapshots-with-a-script"></a>スクリプトを使用したクラウド スナップショットの開始と削除

> [!NOTE]
> StorSimple スナップショットを削除する前に、コンプライアンスとデータ リテンション期間の影響を慎重に評価します。 バックアップ後スクリプトを実行する方法の詳細は、Veritas Backup Exec ドキュメントをご覧ください。

#### <a name="backup-lifecycle"></a>バックアップ ライフサイクル

![バックアップ ライフサイクルの図](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

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

5.  メモ帳で新しい Windows PowerShell スクリプトを作成し、Azure 発行設定を保存した場所と同じ場所に保存します。 たとえば、「 `C:\\CloudSnapshot\\StorSimpleCloudSnapshot.ps1`」のように入力します。

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
        Write-Host "This snapshotdate was created on " $SnapshotStartTimeStamp.Date.ToShortDateString()
        Write-Host "Instance ID " $SnapShotInstanceID
        Write-Host "This snpashotdate is older and needs to be deleted"
        Write-host "\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#"
        Remove-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName -BackupId $SnapShotInstanceID -Force -Verbose
        }
    }   
    ```

1.  Veritas Backup Exec ジョブ オプションのプリ/ポスト コマンドを編集し、Veritas Backup Exec のバックアップ ジョブにスクリプトを追加します。

    ![Backup Exec コンソール、バックアップ オプション、プレ/ポスト コマンド タブ](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> StorSimple クラウド スナップショット バックアップ ポリシーは、毎日のバックアップ ジョブの最後に、後処理スクリプトとして実行することをお勧めします。 RPO/RTO に適合するようバックアップ アプリケーション環境をバックアップ、復元する方法に関する詳細は、自社のバックアップ アーキテクトにご相談ください。

## <a name="storsimple-as-a-restore-source"></a>復元元としての StorSimple

StorSimple からの復元動作は、すべてのブロック ストレージ デバイスからの復元と類似しています。 クラウドに階層化されたデータを復元する場合、復元はクラウドのスピードで実行されます。 ローカル データについては、復元はデバイスのローカル ディスクのスピードで実行されます。 復元を実行する方法に関する詳細は、Veritas Backup Exec ドキュメントをご覧になり、Veritas Backup Exec の復元のベスト プラクティスに従ってください。

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple のフェールオーバーと障害復旧

> [!NOTE]
> バックアップ ターゲットのシナリオについて、StorSimple Cloud Appliance は復元先としてサポートされていません。

障害はさまざまな要因により発生します。 次の表は、よくある障害復旧 (DR) シナリオをまとめたものです。

| シナリオ                                                                    | 影響                                             | 復旧方法                                                                                                                                                                               | メモ                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|-----------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| StorSimple デバイスの不具合                                                | バックアップと復元のオペレーションが中断されます。 | 不具合のあるデバイスを交換し、[StorSimple フェールオーバーと障害復旧](storsimple-device-failover-disaster-recovery.md)を実行します。 | デバイスの復旧直後に復元が実行された場合、完全なワーキング セットがクラウドから新しいデバイスに送られます。 これにより、すべてのオペレーションはクラウドのスピードで実行されます。 また、このインデックスとカタログの再スキャンにより、すべてのバックアップ セットはスキャンされ、クラウドの階層からデバイスのローカル階層に戻されます。 これにはさらに時間がかかります。                                                               |
| Veritas Backup Exec サーバーの不具合                                       | バックアップと復元のオペレーションが中断されます。 | 「[Backup Exec (BEDB) データベースの手動バックアップと復元方法](http://www.veritas.com/docs/000041083)」に従ってバックアップ サーバーを再構築し、データベースの復元を実行します。            | Veritas Backup Exec サーバーは、DR サイトで再構築または復旧する必要があります。 データベースは、最新の時点に復元する必要があります。 復元された Veritas Backup Exec データベースが最新のバックアップ ジョブと同期しない場合、インデックスとカタログの作成が必要です。 このインデックスとカタログの再スキャンにより、すべてのバックアップ セットはスキャンされ、クラウドの階層からローカル デバイスの階層に戻されます。 これにはさらに時間がかかります。 |
| バックアップ サーバーと StorSimple 両方の損失を招くサイトの不具合 | バックアップと復元のオペレーションが中断されます。 | StorSimple を最初に復旧し、次に Veritas Backup Exec を復旧します。                                                                                                                                   | StorSimple を最初に復旧し、次に Veritas Backup Exec を復旧します。                                                                デバイスの復旧後に復元を実行する必要がある場合、完全なデータのワーキング セットがクラウドから新しいデバイスに送られます。 これにより、すべてのオペレーションはクラウドのスピードで実行されます。|


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


