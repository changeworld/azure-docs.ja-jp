---
title: "Veeam を使用して Microsoft Azure StorSimple を構成する | Microsoft Docs"
description: "Veeam を使用して StorSimple バックアップ ターゲットを構成する方法を説明します。"
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
ms.date: 12/06/2016
ms.author: hkanna
translationtype: Human Translation
ms.sourcegitcommit: dfecc291c441b862499d7eaaab421c7b72f929b2
ms.openlocfilehash: 2f71afc6c60cc22ef73a902ee67466313221def2

---

# <a name="configure-storsimple-with-veeam"></a>Veeam を使用して StorSimple を構成する

## <a name="overview"></a>概要

Microsoft Azure StorSimple は、複雑なデータの急増に対応できるハイブリッド クラウド ストレージ ソリューションです。 このソリューションでは Azure クラウド ストレージ アカウントがオンプレミス ソリューションの拡張機能として使用され、オンプレミス ストレージとクラウド ストレージをまたがってデータが自動的に階層化されます。

この記事では、Veeam と StorSimple の統合について取り上げ、両方のソリューションを統合するためのベスト プラクティスについて説明します。 StorSimple と最適に統合するために Veeam を構成する方法に関する推奨事項もご紹介します。 Microsoft は、お客様のバックアップ要件と SLA に合わせて Veeam を最適に構成する方法に関し、Veeam のベスト プラクティス、バックアップ アーキテクト、管理者に従います。

この記事は、構成手順の概要と主要な概念を説明するものであり、構成またはインストールの方法をステップ バイ ステップで説明するガイドではありません。 基本的なコンポーネントとインフラストラクチャが正常に動作しており、ここで説明する概念をサポートする準備が整っていることを前提としています。

## <a name="why-storsimple-as-a-backup-target"></a>StorSimple をバックアップ ターゲットとして使用する理由

StorSimple がバックアップ ターゲットとしてふさわしい理由は、次の通りです。

-   何も変更することなく、高速バックアップ先となるバックアップ アプリケーション用の標準的なローカル ストレージを提供します。 最新のバックアップからのすばやい復元にも使用できます。

-   StorSimple のクラウド階層化はクラウド ストレージ アカウントとシームレスに統合され、費用効果の高い Microsoft Azure ストレージを使用できます。

-   障害復旧のためのオフサイト ストレージを自動的に提供します。


## <a name="target-audience"></a>対象読者

この記事の対象読者は、ストレージ、Windows Server 2012 R2、イーサネット、クラウド サービス、Veeam に関する知識を持つ、バックアップ管理者、ストレージ管理者、ストレージ アーキテクトです。

## <a name="supported-versions"></a>サポートされているバージョン

-   Veeam 9 以降。

-   [StorSimple Update 3 以降](/storsimple-overview#storsimple-workload-summary)。



## <a name="key-concepts"></a>主要な概念

その他のストレージ ソリューションと同様、ソリューションのストレージ性能、SLA、変更率、容量増加の必要性について慎重に評価することが不可欠です。 クラウドの階層を導入することで、お客様のクラウドへのアクセス時間と処理能力が、StorSimple のジョブを実行する能力に根本的な影響を与えます。

StorSimple は、適切に定義されたデータのワーキング セット (ホットデータ) 上で動作するアプリケーションにストレージを提供するよう設計されています。 このモデルでは、データのワーキング セットはローカルの階層に格納され、残りの非ワーキング/クラウド/アーカイブ セットはクラウドに格納されます。 このモデルを示したのが次のグラフです。 ほぼ平らな緑の線は、StorSimple デバイスのローカル階層に格納されているデータを表しています。 赤い線は、すべての階層で StorSimple ソリューションに格納されているデータの総量を表しています。 平らな緑の線と右上がりの曲線を描く赤い線の間の空間は、クラウドに格納されているデータの総量を表しています。

**StorSimple の階層化**
![Storsimple の階層化の図](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

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


| ストレージの容量       | 8100       | 8600       |
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

![プライマリ バックアップ ターゲットとしての StorSimple の論理図](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>プライマリ ターゲット バックアップの論理的ステップ

1.  バックアップ サーバーが対象のバックアップ エージェントに接続され、バックアップ エージェントからデータがバックアップ サーバーに転送されます。

2.  バックアップ サーバーから StorSimple 階層化ボリュームにデータが書き込まれます。

3.  バックアップ サーバーがカタログ データベースを更新し、バックアップ ジョブを完了します。

4.  スナップショット スクリプトは StorSimple のクラウド スナップショット管理をトリガーします。

5.  リテンション期間ポリシーに基づき、バックアップ サーバーから期限切れのバックアップが削除されます。

### <a name="primary-target-restore-logical-steps"></a>プライマリ ターゲットの復元の論理的ステップ

1.  バックアップ サーバーが、ストレージ リポジトリから適切なデータの復元を開始します。

2.  バックアップ エージェントがバックアップ サーバーからデータを受信します。

3.  バックアップ サーバーが復元ジョブを完了します。

## <a name="storsimple-as-a-secondary-backup-target"></a>セカンダリ バックアップ ターゲットとしての StorSimple

このシナリオでは、StorSimple ボリュームを主に使用して主に長期リテンションまたはアーカイブを行います。

次の図は、初回のバックアップと復元が高性能ボリュームをターゲットとしているアーキテクチャを表しています。 これらのバックアップは、指定のスケジュールで StorSimple 階層化ボリュームにコピー、アーカイブされます。

高性能ボリュームに、リテンション期間ポリシー、容量、性能の要件に対応できる十分な領域と性能があることが重要です。

![セカンダリ バックアップ ターゲットとしての StorSimple の論理図](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>セカンダリ ターゲット バックアップの論理的ステップ

1.  バックアップ サーバーが対象のバックアップ エージェントに接続され、バックアップ エージェントからデータがバックアップ サーバーに転送されます。

2.  バックアップ サーバーが高性能ストレージにデータを書き込みます。

3.  バックアップ サーバーがカタログ データベースを更新し、バックアップ ジョブを完了します。

4.  リテンション期間ポリシーに基づき、バックアップ サーバーが StorSimple にバックアップをコピーします。

5.  スナップショット スクリプトは StorSimple のクラウド スナップショット管理をトリガーします。

6.  リテンション期間ポリシーに基づき、バックアップ サーバーから期限切れのバックアップが削除されます。

### <a name="secondary-target-restore-logical-steps"></a>セカンダリ ターゲットの復元の論理的ステップ

1.  バックアップ サーバーが、ストレージ リポジトリから適切なデータの復元を開始します。

2.  バックアップ エージェントがバックアップ サーバーからデータを受信します。

3.  バックアップ サーバーが復元ジョブを完了します。

## <a name="deploy-the-solution"></a>ソリューションのデプロイ方法

このソリューションのデプロイメントは、3 つの手順で構成されています。ネットワーク インフラストラクチャの準備、StorSimple デバイスのバックアップ ターゲットとしてのデプロイ、Veeam ソフトウェアのデプロイです。 これらの各手順を以下のセクションで詳しく説明します。

### <a name="configure-the-network"></a>ネットワークの構成方法

Azure クラウドとの統合ソリューションである StorSimple は、Azure クラウドへのアクティブで有効な接続を必要とします。 この接続は、クラウド スナップショット、管理、メタデータの転送、Azure のクラウド ストレージへの古いデータやアクセスの少ないデータの階層化などのオペレーションに使用されます。

ソリューションを最適に実行するには、次のネットワークのベスト プラクティスに従うことをお勧めします。

-   StorSimple の階層と Azure 間の接続は、適切なクオリティ オブ サービス (QoS) をインフラストラクチャ スイッチに適用して RPO/RTO の SLA に合致した帯域幅要件を満たす必要があります。

-   Azure Blob Storage のアクセスの最大待機時間は、80 ミリ秒の範囲内で指定する必要があります。

### <a name="deploy-storsimple"></a>StorSimple のデプロイ方法

StorSimple のデプロイメントのステップ バイ ステップ ガイダンスは、「[オンプレミスの StorSimple デバイスのデプロイ](storsimple-deployment-walkthrough-u2.md)」をご覧ください。

### <a name="deploy-veeam"></a>Veeam の展開方法

Veeam のインストールのベスト プラクティスについては、「[Veeam9 のベスト プラクティス](https://bp.veeam.expert/)」と[ Veeam のヘルプ センター (テクニカル ドキュメント) ](https://www.veeam.com/documentation-guides-datasheets.html)のユーザーガイドをご覧ください。

## <a name="configure-the-solution"></a>ソリューションの構成方法

このセクションでは、構成の例をいくつかご紹介します。 次の例と推奨事項は、最も基本的な実装を示しています。 この実装例は特定のバックアップ要件に直接適用されない場合があります。

### <a name="configure-storsimple"></a>StorSimple の構成方法

| StorSimple のデプロイメント タスク                                                                                                                 | その他のコメント                                                                                                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| オンプレミスの StorSimple デバイスのデプロイ。                                                                                    | サポートされているバージョン: Update 3 以降。                                                                                                                                                                                                                                                                 |
| バックアップ ターゲット モードを有効にする。                                                                                                                | 次のコマンドを使用して有効化/無効化し、ステータスを取得します。 詳細は、「[StorSimple デバイスにリモート接続する](storsimple-remote-connect.md)」をご覧ください。</br> バックアップ モードの有効化: `Set-HCSBackupApplianceMode -enable`</br>  バックアップ モードの無効化: `Set-HCSBackupApplianceMode -disable`</br> バックアップ モード設定の現在のステータス: `Get-HCSBackupApplianceMode` |
| バックアップ データを格納するボリュームの共通ボリューム コンテナーを作成する。 ボリューム コンテナー内のすべてのデータは重複除去される。 | StorSimple のボリューム コンテナーでは、重複除去のドメインを定義します。                                                                                                                                                                                                                                             |
| StorSimple ボリュームを作成する。                                                                                                                 | ボリューム サイズがクラウド スナップショットの継続時間に影響を与えるため、予想される使用量に可能な限り近いサイズのボリュームを作成します。 ボリュームのサイズを変更する方法の詳細については「[リテンション期間ポリシー](#retention-policies)」をご覧ください。</br> </br> StorSimple 階層化ボリュームを使用し、[**Use this volume for less frequently accessed archival data] (アクセス頻度の低いアーカイブ データにこのボリュームを使用します**) チェックボックスをオンにします。 </br> ローカル固定ボリュームのみの作成はサポートされていません。        |
| すべてのバックアップ ターゲット ボリュームに対し、一意の StorSimple バックアップ ポリシーを作成する。                                                               | ボリュームの整合性グループは、StorSimple のバックアップ ポリシーで定義されます。                                                                                                                                                                                                                                       |
| スナップショットとしてスケジュールを無効にする。                                                                                                    | スナップショットは後処理オペレーションとしてトリガーされます。                                                                                                                                                                                                                                                         |
|                                                                                                     |                                             |


### <a name="configure-host-backup-server-storage"></a>ホスト バックアップ サーバー ストレージの構成方法

次のガイドラインに沿ってホスト バックアップ サーバー ストレージが構成されていることを確認します。  

- (Windows Disk マネージャーで作成された) スパン ボリュームはサポートされていないため、使用しないでください。
- アロケーション ユニット サイズ 64 KB の NTFS を使用してボリュームをフォーマットします。
- StorSimple ボリュームを Veeam サーバーに直接マッピングします。 
    - 物理サーバーに iSCSI を使用します。


## <a name="best-practices-for-storsimple-and-veeam"></a>StorSimple と Veeam のベスト プラクティス

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

### <a name="veeam"></a>Veeam

-   Veeam データベースは、サーバーにローカルである必要があり、StorSimple ボリューム上に存在してはなりません。

-   DR 用に、Veeam データベースを StorSimple ボリュームにバックアップします。

-   このソリューションについて、Microsoft では Veeam の完全バックアップと増分バックアップをサポートしています。 合成バックアップや差分バックアップは使用しないことをお勧めします。

-   バックアップ データ ファイルには、特定のジョブのデータのみ含める必要があります。 たとえば、複数のジョブにまたがってメディアを追加することはできません。

-   ジョブの検証を無効にします。 必要に応じて、最近のバックアップ ジョブの後に検証をスケジュールします。 このジョブがバックアップの時間帯に影響を与えることを理解しておくことが重要です。

-   メディアの事前割り当てを無効にします。

-   並列処理が有効になっていることを確認します。

-   圧縮を無効にします。

-   バックアップ ジョブでの重複除去を無効にします。

-   **LAN ターゲット**の最適化を設定します。

-   **[Create active full backup] **(アクティブな完全バックアップの作成) (2 週間ごと) を有効にします。

-   バックアップ リポジトリで、**[Use per-VM backup files]** (VM ごとのバックアップ ファイルを使用) を構成します。

-   **[use multiple upload streams per job]** (ジョブごとの複数のアップロード ストリームを使用) を「8」 (最大 16) に設定します。 StorSimple デバイスの CPU 使用率に合わせて数字を設定します。

## <a name="retention-policies"></a>リテンション期間ポリシー

最も使用されているバックアップ リテンション期間ポリシーは、Grandfather-Father-Son (GFS) 方式です。 この方式では、増分バックアップは毎日実行されます。 完全バックアップは、週単位と月単位で実行されます。 この方式では 6 つの StorSimple 階層化ボリュームを使用します。

-   1 つのボリュームには、週、月、年単位の完全バックアップが格納されます。

-   残りの 5 つのボリュームには、毎日の増分バックアップが格納されます。

次の例では、GFS 方式を使用しています。 次の例では以下の条件を前提としています。

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

## <a name="configuring-veeam-storage"></a>Veeam ストレージの構成方法

1.  **[バックアップ インフラストラクチャ]** の設定に移動します。 **[バックアップ リポジトリ]** を選択し、右クリックして **[Add Backup Repository]** (バックアップ リポジトリを追加) を選択します。

    ![Veeam 管理コンソール、バックアップ リポジトリの画面](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

1.  リポジトリの**名前**と**説明**を指定します。 **[次へ >] **をクリックします。

    ![Veeam 管理コンソール、名前と説明の画面](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

1.  バックアップ リポジトリに **[Microsoft Windows Server]** を選択します。 Veeam サーバーを選択します。 **[次へ >] **をクリックします。

    ![Veeam 管理コンソール、バックアップ リポジトリのタイプを選択する](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

1.  **[Location]** (ロケーション) を指定するには、必要なボリュームを検索し、選択します。 **[Limit maximum concurrent tasks to:]** (制限する並行タスクの最大数:) を「4」に設定します。 これにより、各 VM と同時に処理される仮想ディスクの数が 4 つに制限されます。
**[詳細設定]**をクリックします。

    ![Veeam 管理コンソール、ボリュームを選択する](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


1.  **[Use per-VM backup files]** (VM ごとのバックアップ ファイルを使用) を選択します。

    ![Veeam 管理コンソール、ストレージの互換性設定](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

1.  **[vPower NFS service on the mount server (recommended)]** (マウントサーバーの vPower NFS サービス (推奨)) を有効にします。

    ![Veeam 管理コンソール、バックアップ リポジトリの画面](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

1.  設定を確認し、次のページに進みます。

    ![Veeam 管理コンソール、バックアップ リポジトリの画面](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    リポジトリが Veeam サーバーに追加されます。

## <a name="storsimple-as-a-primary-backup-target"></a>プライマリ バックアップ ターゲットとしての StorSimple

> [!IMPORTANT]
> クラウドに階層化されたバックアップからデータの復元が必要な場合、クラウドのスピードで復元されます。

次の図は、バックアップ ジョブに対する一般的なボリュームのマッピングを示しています。 この場合、週単位のバックアップはすべて「土曜日 - 完全」のディスクにマッピングされ、増分バックアップは月曜日～金曜日の増分ディスクにマッピングされています。 すべてのバックアップと復元は、StorSimple 階層化ボリュームから行われます。

![プライマリ バックアップ ターゲット構成の論理図 ](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

#### <a name="storsimple-as-a-primary-backup-target-grandfather-father-and-son-gfs-schedule-example"></a>プライマリ バックアップ ターゲットとしての StorSimple の Grandfather-Father-Son (GFS) スケジュールの例

| 4 週間、月単位、年単位の GFS ローテーション スケジュール |               |             |
|--------------------------------------------------------------------------|---------------|-------------|
| 頻度/バックアップ タイプ   | 完全          | 増分 (1 ～ 5 日目)  |
| 週単位 (第 1 ～ 4 週)    | 土曜日 | 月曜日～金曜日 |
| 月単位     | 土曜日  |             |
| 年単位      | 土曜日  |             |


### <a name="assigning-storsimple-volumes-to-a-veeam-backup-job"></a>StorSimple ボリュームを Veeam バックアップ ジョブに割り当てる

1.  バックアップ ターゲットとして、プライマリ バックアップ ターゲット シナリオの場合はプライマリ Veeam StorSimple を使用し、セカンダリ バックアップ ターゲットの場合は DAS/NAS/JBOD を使用して毎日のジョブを作成します。 **[Backup &#38; Replication &gt; Backup]** (バックアップ & レプリケーション バックアップ) に移動します。 右クリックし、ご使用の環境に合わせて [VMware] または [Hyper-V] を選択します。

    ![Veeam 管理コンソール、新しいバックアップ ジョブ](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

1.  毎日のバックアップ ジョブの**名前**と**説明**を指定します。

    ![Veeam 管理コンソール、新しいバックアップ ジョブの画面](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

1.  バックアップ先の仮想マシンを選択します。

    ![Veeam 管理コンソール、新しいバックアップ ジョブの画面](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

1.  **[バックアップ プロキシ]** と **[バックアップ リポジトリ]** を適切に設定します。 ローカルに接続されたストレージの環境の RPO/RTO 定義に合わせて **[Restore points to keep on disk]** (ディスクに維持する復旧時点) を設定します。 [詳細設定] をクリックします。

    ![Veeam 管理コンソール、新しいバックアップ ジョブの画面](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

    **[バックアップ]** タブで **[増分]** を選択します。 **[Create synthetic full backups periodically]** (合成完全バックアップを定期的に作成する) チェックボックスがオフになっていることを確認します。 **[Create active full backups periodically]** (アクティブな完全バックアップを定期的に作成する) を有効にし、[Weekly on selected days] (選択した曜日で毎週) を選択して、**[Active full backups]** (アクティブな完全バックアップ) が毎週土曜日に実行されるよう設定します。

    ![Veeam 管理コンソール、新しいバックアップ ジョブの詳細設定画面](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

    [ストレージ] タブで、重複除去と圧縮が無効になっていることと、ファイル ブロックのスワップと削除が有効になっていることを確認します。 **[ストレージの最適化]** を **[LAN Target]** (LAN ターゲット) に設定しパフォーマンスと重複除去のバランスを取ります。

    ![Veeam 管理コンソール、新しいバックアップ ジョブの詳細設定画面](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    重複除去と圧縮の設定方法の詳細は、「[Data Compression and Deduplication](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html)」 (データの圧縮と重複除去) をご覧ください。

1.  **[Enable Application Aware Processing]** (アプリケーション整合プロセス) を選択できます。

    ![Veeam 管理コンソール、新しいバックアップ ジョブのゲスト プロセス画面](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

1.  1 日に 1 度、任意の指定時間で実行するようスケジュールを設定します。

    ![Veeam 管理コンソール、新しいバックアップ ジョブのスケジューラ画面](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="storsimple-as-a-secondary-backup-target"></a>セカンダリ バックアップ ターゲットとしての StorSimple

> [!NOTE]
> クラウドに階層化されたバックアップからのデータの復元は、クラウドのスピードで実行されます。

このモデルでは、一時キャッシュとして機能する (StorSimple 以外の) ストレージ メディアが必要です。 たとえば、領域、I/O、帯域幅に合わせて、RAID ボリュームを使用できます。 RAID 5、50、10 を使用することをお勧めします。

次の図は、一般的な短期リテンションの ローカル ボリューム (サーバーにローカル) と長期リテンションのアーカイブ ボリュームを表しています。 この場合、すべてのバックアップはローカル RAID ボリューム (サーバーにローカル) で実行されます。 これらのバックアップは、アーカイブ ボリュームに定期的に複製、アーカイブされます。 ローカル RAID ボリューム (サーバーにローカル) に、短期リテンション容量と性能要件に対応できる容量があることが重要です。



| バックアップ タイプとリテンション                    |構成済みストレージ| サイズ (TiB) | GFS 乗数 | 合計容量 (TiB)        |
|----------------------------------------------|-----|----------|----------------|------------------------|
| 第 1 週 (完全と増分) |ローカル ディスク (短期)| 1        | 1              | 1           |
| StorSimple 第 2 ～ 4 週           |StorSimple ディスク (長期) | 1        | 4              | 4                   |
| 月単位 - 完全                                 |StorSimple ディスク (長期) | 1        | 12             | 12                   |
| 年単位 - 完全                               |StorSimple ディスク (長期) | 1        | 1              | 1                   |
|GFS ボリュームのサイズ要件 | |          |                | 18*|

\* 合計容量は、StorSimple ディスクの 17 TiB とローカル RAID ボリュームの 1 TiB の合計です。

![セカンダリ バックアップ ターゲットとしての StorSimple の論理図 ](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

#### <a name="gfs-example-schedule"></a>GFS スケジュールの例

| 週、月、年単位の GFS ローテーション スケジュール|                    |                   |                   |                   |                   |                   |
|--------------------------------------------------------------------------|--------------------|-------------------|-------------------|-------------------|-------------------|-------------------|
| 週                                                                     | 完全               | 増分 (1 日目)        | 増分 (2 日目)        | 増分 (3 日目)        | 増分 (4 日目)        | 増分 (5 日目)        |
| 第 1 週                                                                   | ローカル RAID ボリューム  | ローカル RAID ボリューム | ローカル RAID ボリューム | ローカル RAID ボリューム | ローカル RAID ボリューム | ローカル RAID ボリューム |
| 第 2 週                                                                   | StorSimple 第 2 ～ 4 週 |                   |                   |                   |                   |                   |
| 第 3 週                                                                   | StorSimple 第 2 ～ 4 週 |                   |                   |                   |                   |                   |
| 第 4 週                                                                   | StorSimple 第 2 ～ 4 週 |                   |                   |                   |                   |                   |
| 月単位                                                                  | StorSimple 月単位 |                   |                   |                   |                   |                   |
| 年単位                                                                   | StorSimple 年単位  |                   |                   |                   |                   |                   |


### <a name="assigning-storsimple-volumes-to-a-veeam-copy-job"></a>StorSimple ボリュームを Veeam コピー ジョブに割り当てる

1.  [New Backup Copy Job] (新しいバックアップ コピー ジョブ) ウィザードを立ち上げます。 **[ジョブ] > [バックアップ コピー]** の順に移動します。 ご使用の環境に合わせて [VMware] または [Hyper-V] を選択します。

    ![Veeam 管理コンソール、新しいバックアップ コピー ジョブの画面](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

1.  ジョブの名前と説明を指定します。

    ![Veeam 管理コンソール、新しいバックアップ コピー ジョブの画面](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

1.  処理する VM 、[from backups] (バックアップから)、前に作成した毎日のバックアップをそれぞれ選択します。

    ![Veeam 管理コンソール、新しいバックアップ コピー ジョブの画面](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

1.  必要に応じてバックアップ コピー ジョブからオブジェクトを除外します。

1.  **[バックアップ リポジトリ]** を選択し、**[Restore points to keep]** (維持する復旧時点) を指定し、**[keep the following restore points for archival purposes]** (アーカイブ目的のため以下の復旧時点を維持する) が有効になっていることを確認します。 バックアップの頻度を指定し **[詳細設定]** をクリックします。

    ![Veeam 管理コンソール、新しいバックアップ コピー ジョブの画面](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

1.  次の詳細設定を指定します。

    -   **[メンテナンス]** タブでストレージ レベルの破損保護を無効にします。

    -   **[ストレージ]** タブで、重複除去と圧縮が無効になっていることを確認します。

    ![Veeam 管理コンソール、新しいバックアップ コピー ジョブの詳細設定画面](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    ![Veeam 管理コンソール、新しいバックアップ コピー ジョブの詳細設定画面](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

1.  データを直接転送するよう指定します。

1.  必要に応じてバックアップ コピーの時間帯スケジュールを指定し、完了します。


詳細は、「[Create backup copy jobs](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html)」 (バックアップ コピー ジョブを作成する) をご覧ください。

## <a name="storsimple-cloud-snapshots"></a>StorSimple クラウド スナップショット

StorSimple クラウド スナップショットは、StorSimple デバイスに存在するデータを保護するものです。 これはオフサイト設備への転送テープと同じ機能を持ちます。 Azure の geo 冗長ストレージ (GRS) を使用する場合、複数のサイトへの転送テープと同じ機能を持ちます。 障害時にデバイスの復旧が必要な場合、別の StorSimple デバイスをオンラインにしフェールオーバーを実行できます。 次のフェールオーバーでは、最近のクラウド スナップショットからデータに (クラウドのスピードで) アクセスできます。


次のセクションでは、バックアップの後処理中にショート スクリプトを作成し、StorSimple クラウド スナップショットをトリガーし、削除する方法を説明します。 

> [!NOTE] 
> 手動またはプログラムによって作成されたスナップショットは、StorSimple スナップショットの有効期間ポリシーに準拠しません。 これらのスナップショットは、手動またはプログラムによって削除する必要があります。

### <a name="start-and-delete-cloud-snapshots-with-a-script"></a>スクリプトを使用したクラウド スナップショットの開始と削除

> [!NOTE] 
> StorSimple スナップショットを削除する前に、コンプライアンスとデータ リテンション期間の影響を慎重に評価します。 バックアップ後スクリプトを実行する方法の詳細は、Veeam ドキュメントをご覧ください。


#### <a name="backup-lifecycle"></a>バックアップ ライフサイクル


![バックアップ ライフサイクルの図](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

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

5.  メモ帳で Windows PowerShell スクリプトを作成し、Azure 発行設定を保存した場所と同じ場所に保存します。 たとえば、「 `C:\\CloudSnapshot\\StorSimpleCloudSnapshot.ps1`」のように入力します。

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

6.  スクリプトをバックアップ ジョブに追加するには、Veeam ジョブの詳細設定のオプションを編集します。 

![Veeam バックアップの詳細設定のスクリプト タブ](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

StorSimple クラウド スナップショット バックアップ ポリシーは、毎日のバックアップ ジョブの最後に、後処理スクリプトとして実行することをお勧めします。 RPO/RTO に適合するようバックアップ アプリケーション環境をバックアップ、復元する方法に関する詳細は、自社のバックアップ アーキテクトにご相談ください。

## <a name="storsimple-as-a-restore-source"></a>復元元としての StorSimple
==============================

StorSimple デバイスからの復元動作は、すべてのブロック ストレージ デバイスからの復元として機能します。 クラウドに階層化されたデータを復元する場合、復元はクラウドのスピードで実行されます。 ローカル データについては、復元はデバイスのローカル ディスクのスピードで実行されます。

Veeam は、Veeam コンソールに組み込まれたエクスプローラーを使用して、StorSimple 経由でファイル レベルでの詳細な高速復元を可能にします。 Veeam エクスプローラーを使用して、メールのメッセージ、アクティブ ディレクトリ オブジェクト、SharePoint のアイテムなどの個別のアイテムをバックアップから復元できます。 この復元は、オンプレミス VM を中断することなく実行できます。 Microsoft SQL と Oracle データベースに対する任意の時点への復元もできます。 Veeam と StorSimple により、Azure からのアイテム レベルでの復元プロセスが高速に、また簡単になります。 復元の方法については、Veeam ドキュメントをご覧ください。


- [https://www.veeam.com/microsoft-exchange-recovery.html](https://www.veeam.com/microsoft-exchange-recovery.html)

- [https://www.veeam.com/microsoft-active-directory-explorer.html](https://www.veeam.com/microsoft-active-directory-explorer.html)

- [https://www.veeam.com/microsoft-sql-server-explorer.html](https://www.veeam.com/microsoft-sql-server-explorer.html)

- [https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)

- [https://www.veeam.com/oracle-backup-recovery-explorer.html](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple のフェールオーバーと障害復旧

> [!NOTE]
> バックアップ ターゲットのシナリオについて、StorSimple Cloud Appliance は復元先としてサポートされていません。

障害はさまざまな要因により発生します。 次の表は、よくある障害復旧 (DR) シナリオをまとめたものです。


| シナリオ                                                                    | 影響                                             | 復旧方法                                                                                                                                                                               | メモ                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|-----------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| StorSimple デバイスの不具合                                               | バックアップと復元のオペレーションが中断されます。 | 不具合のあるデバイスを交換し、[StorSimple フェールオーバーと障害復旧](storsimple-device-failover-disaster-recovery.md)を実行します | デバイスの復旧直後に復元が実行された場合、完全なワーキング セットがクラウドから新しいデバイスに送られます。 これにより、すべてのオペレーションはクラウドのスピードで実行されます。 また、このインデックスとカタログの再スキャンにより、すべてのバックアップ セットはスキャンされ、クラウドの階層からデバイスのローカル階層に戻されます。 これにはさらに時間がかかります。                                 |
| Veeam サーバーの不具合。                                                     | バックアップと復元のオペレーションが中断されます。 | [Veeam のヘルプ センター (テクニカル ドキュメント)](https://www.veeam.com/documentation-guides-datasheets.html) に記載されているようにバックアップ サーバーを再構築し、データベースの復元を実行します     | Veeam サーバーは、DR サイトで再構築または復元する必要があります。 データベースを最新の時点に復元します。 復元された Veeam データベースが最新のバックアップ ジョブと同期しない場合、インデックスとカタログの作成が必要です。 このインデックスとカタログの再スキャンにより、すべてのバックアップ セットはスキャンされ、クラウドの階層からローカル デバイスの階層に戻されます。 これにはさらに時間がかかります。 |
| バックアップ サーバーと StorSimple 両方の損失を招くサイトの不具合 | バックアップと復元のオペレーションが中断されます。 | StorSimple を最初に復旧し、次に Veeam を復旧します。                                                                                                                                                  | StorSimple を最初に復旧し、次に Veeam を復旧します。 デバイスの復旧後に復元を実行する必要がある場合、完全なデータのワーキング セットがクラウドから新しいデバイスに送られます。 これにより、すべてのオペレーションはクラウドのスピードで実行されます。                                                                                                                                                                            |


## <a name="references"></a>参照

この記事では次のドキュメントを参照しています。

- [StorSimple デバイスの MPIO の構成](storsimple-configure-mpio-windows-server.md)

- [ストレージ シナリオ: 仮想プロビジョニング (Storage scenarios: Thin provisioning)](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)

- [GPT ドライブを使用する (Using GPT drives)](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)

- [共有フォルダーのシャドウ コピーを有効にして構成する](http://technet.microsoft.com/library/cc771893.aspx)



<!--HONumber=Dec16_HO2-->


