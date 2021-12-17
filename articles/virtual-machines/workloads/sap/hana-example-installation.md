---
title: SAP HANA on Azure (Large Instances) に HANA をインストールする | Microsoft Docs
description: HANA を SAP HANA on Azure (Large Instances) にイントールする方法を説明します。
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/4/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e4e7aec0422d3dfe5772748215c350073155a30
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2021
ms.locfileid: "111559790"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (Large Instances) に HANA をインストールする

この記事では、HANA を SAP HANA on Azure Large Instances (別名 BareMetal Infrastructure) にインストールする方法を説明します。

## <a name="prerequisites"></a>前提条件

HANA を SAP HANA on Azure (Large Instances) にインストールするには、最初に次のことを行います。

- SAP HANA Large Instances にデプロイするすべてのデータを Microsoft に渡します。
- Microsoft から SAP HANA Large Instances を受け取ります。
- オンプレミスのネットワークに接続している Azure 仮想ネットワークを作成します。
- HANA Large Instances 用の ExpressRoute 回線を同じ Azure 仮想ネットワークに接続します。
- HANA Large Instances のための踏み台に使用する Azure 仮想マシンをインストールします。
- 踏み台から HANA Large Instances に、またはその逆に接続できることを確認します。
- 必要なパッケージとパッチがすべてインストールしてあるかどうかを確認します。
- 使用しているオペレーティング システムへの HANA のインストールに関する SAP のノートとドキュメントを読みます。 そのオペレーティング システム リリースで、選択した HANA リリースがサポートされていることを確認する。

## <a name="download-the-sap-hana-installation-bits"></a>SAP HANA インストール ビットのダウンロード

HANA のインストール パッケージを踏み台仮想マシンにダウンロードしましょう。 この例では、オペレーティング システムに Windows を使用します。

HANA L インスタンス ユニットは、インターネットに直接接続されません。 SAP から HANA L インスタンス仮想マシンにインストール パッケージを直接ダウンロードすることはできません。 その代わり、ジャンプ ボックス仮想マシンにパッケージをダウンロードします。

SAP S ユーザーなどのユーザーに、SAP Marketplace にアクセスできるようにしてもらう必要があります。

1. [SAP Service Marketplace](https://support.sap.com/en/index.html) にサインインしてアクセスします。 **[Download Software]\(ソフトウェアのダウンロード\)**  >  **[Installations and Upgrade]\(インストールとアップグレード\)**  >  **[By Alphabetical Index]\(アルファベット インデックス順\)** を選択します。 **[Under H - SAP HANA Platform Edition]\(H の SAP HANA Platform Edition\)**  >  **[SAP HANA Platform Edition 2.0]**  >  **[Installation]\(インストール\)** の順に選択します。 次のスクリーンショットに示されているファイルをダウンロードします。

   ![HANA のインストール ファイルをダウンロードする操作のスクリーンショット。](./media/hana-installation/image16_download_hana.PNG)

2. この例では、SAP HANA 2.0 インストール パッケージをダウンロードしました。 Azure ジャンプ ボックス仮想マシンで、以下に示すとおり自己解凍形式のアーカイブをディレクトリに展開します。

   ![アーカイブの自己抽出操作のスクリーンショット。](./media/hana-installation/image17_extract_hana.PNG)

3. アーカイブを抽出したら、抽出によって作成されたディレクトリ (この例では 51052030) をコピーします。 作成したディレクトリに、HANA L インスタンス ユニットの /hana/shared ボリュームのディレクトリをコピーします。

   > [!Important]
   > インストール パッケージは、ルートおよびブート LUN にはコピーしないでください。 スペースに限りがあり、このスペースは他の処理に使用する必要があります。


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>HANA L インスタンス ユニットへの SAP HANA のインストール

1. SAP HANA をインストールするには、ルート ユーザーとしてサインインします。 SAP HANA をインストールための十分なアクセス許可を持つのはルートだけです。 /hana/shared にコピーしたディレクトリに対するアクセス許可を設定します。

    ```
    chmod –R 744 <Installation bits folder>
    ```
    
    GUI でセットアップを行って SAP HANA をインストールするには、HANA Large Instances に gtk2 のパッケージをインストールする必要があります。 これがインストールされているかどうかは、次のコマンドで確認できます。
    
    ```
    rpm –qa | grep gtk2
    ```

    (以降の手順では、グラフィカル ユーザー インターフェイスを使用して SAP HANA のセットアップについて説明します。)         

2. インストール ディレクトリに移動し、サブディレクトリ HDB_LCM_LINUX_X86_64 に移動します。 

    そのディレクトリから開始します。
    
    ```
    ./hdblcmgui 
    ```
3. 次に、いくつかの画面を移動しながら、インストールに必要なデータを指定していきます。 この例では、SAP HANA データベース サーバーと SAP HANA クライアント コンポーネントをインストールします。 そのため **SAP HANA Database** を選んでいます。

    ![SAP HANA Lifecycle Management の画面のスクリーンショット。SAP HANA Database が選択されている。](./media/hana-installation/image18_hana_selection.PNG)

4. **[Install New System]\(新しいシステムのインストール\)** を選択します。

    ![SAP HANA Lifecycle Management の画面のスクリーンショット。[Install New System]\(新しいシステムのインストール\) が選択されている。](./media/hana-installation/image19_select_new.PNG)

5. インストールできる他のコンポーネントから必要なものを選択します。

    ![SAP HANA ライフサイクル管理画面のスクリーンショット (追加コンポーネントの一覧)](./media/hana-installation/image20_select_components.PNG)

6. SAP HANA Client と SAP HANA Studio を選択します。 スケールアップ インスタンスもインストールします。 **[Single-Host System]\(シングルホストのシステム\)** を選択します。 

    ![SAP HANA Lifecycle Management の画面のスクリーンショット。[Single-Host System]\(シングルホストのシステム\) が選択されている。](./media/hana-installation/image21_single_host.PNG)

7. いくつかのデータを指定します。 インストール パスには、/hana/shared ディレクトリを使用します。

    ![SAP HANA Lifecycle Management の画面のスクリーンショット。入力が必要なシステムのプロパティ欄が表示されている。](./media/hana-installation/image22_provide_sid.PNG)

    > [!Important]
    > HANA システム ID (SID) として、HANA L インスタンスのデプロイを注文したときに Microsoft に提供したものと同じ SID を入力する必要があります。 別の SID を選択すると、さまざまなボリュームでのアクセス許可の問題が原因でインストールが失敗します。

8. HANA のデータ ファイルと HANA のログ ファイルの場所を指定します。

    ![SAP HANA ライフサイクル管理画面のスクリーンショット (データ エリアとログ エリアのフィールド)](./media/hana-installation/image23_provide_log.PNG)

    > [!Note]
    > システム プロパティを定義するとき (2 つ前の画面) に指定した SID は、マウント ポイントの SID と一致している必要があります。 一致していない場合は、前に戻って、マウント ポイントの SID 値に調整します。

9. ホスト名を確認し、必要に応じて修正します。 

    ![SAP HANA Lifecycle Management の画面のスクリーンショット。ホスト名が表示されている。](./media/hana-installation/image24_review_host_name.PNG)

10. HANA Large Instances のデプロイを依頼したときに Microsoft に渡したデータを取得します。 

    ![SAP HANA ライフサイクル管理画面のスクリーンショット (定義するシステム管理者フィールド)](./media/hana-installation/image25_provide_guid.PNG)

    > [!Important]
    > ユニットのデプロイを依頼したときに Microsoft に渡した **[System Administrator User ID]\(システム管理者ユーザー ID\)** と **[ID of User Group]\(ユーザー グループのID\)** を入力します。 これを行わないと、HANA Large Instances ユニットへの SAP HANA のインストールは失敗します。

11. 次の 2 つの画面は、ここに掲載しません。 それらでは、SAP HANA データベースの SYSTEM ユーザーのパスワードと、sapadm ユーザーのパスワードを入力することができます。 後者は、SAP HANA データベース インスタンスの一部としてインストールされる SAP Host Agent で使用されます。

    パスワードを定義すると確認画面が表示されます。 表示されているすべてのデータを確認し、インストールを続行します。 次のような、インストールの進み具合を表示する進行状況画面に移動します。

    ![SAP HANA Lifecycle Management の画面のスクリーンショット。インストールの進み具合が表示されている。](./media/hana-installation/image27_show_progress.PNG)

12. インストールが終了すると、次のような画面が表示されます。

    ![SAP HANA Lifecycle Management の画面のスクリーンショット。インストールの終了が表示されている。](./media/hana-installation/image28_install_finished.PNG)

    SAP HANA インスタンスが起動して稼働状態になり、使用する準備が整いました。 SAP HANA Studio から接続できます。 最新の更新プログラムを確認し、適用します。


## <a name="next-steps"></a>次のステップ

Azure での SAP HANA Large Instances の高可用性とディザスター リカバリーについて学ぶ。

> [!div class="nextstepaction"]
> [Azure での SAP HANA Large Instances の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)
