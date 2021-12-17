---
title: Azure から Oracle BareMetal サーバーにトラフィックをルーティングするように SnapCenter を設定する
description: Azure から Oracle BareMetal インフラストラクチャ サーバーにトラフィックをルーティングするように NetApp SnapCenter を設定する方法について説明します。
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 05/05/2021
ms.openlocfilehash: 9d8f83712822dcf556efe1098eadd1cfdb4867e6
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110576232"
---
# <a name="set-up-netapp-snapcenter-to-route-traffic-from-azure-to-oracle-baremetal-servers"></a>Azure から Oracle BareMetal サーバーにトラフィックをルーティングするように NetApp SnapCenter を設定する

この記事では、Azure から Oracle BareMetal インフラストラクチャ サーバーにトラフィックをルーティングするように NetApp SnapCenter を設定する手順について説明します。 

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> - **SnapCenter Server のシステム要件:** Azure Windows 2019 以降、4 vCPU、16 GB RAM、500 GB 以上のマネージド Premium SSD ストレージ。
> - **ExpressRoute ネットワークの要件:** Oracle BareMetal の SnapCenter ユーザーは、Microsoft Operations と協力して、個人用ストレージ仮想マシン (VM) と Azure の SnapCenter VM の間の通信を可能にするネットワーク アカウントを作成する必要があります。
> - **BareMetal インスタンス上の Java 1.8:** SnapCenter プラグインを使用するには、BareMetal インスタンスに Java 1.8 がインストールされている必要があります。

## <a name="steps-to-integrate-snapcenter"></a>SnapCenter を統合する手順

Azure から Oracle BareMetal インフラストラクチャ サーバーにトラフィックをルーティングするように NetApp SnapCenter を設定するために必要な手順を次に示します。 

1. サポート チケット リクエストを提出して、ユーザーが生成した公開キーを Microsoft Ops チームに伝えます。 SnapCenter ユーザーがストレージ システムにアクセスできるように設定するには、サポートが必要です。 

2. BareMetal インスタンスにアクセスできる Azure Virtual Network (VNet) に VM を作成します。この VM は SnapCenter に使用されます。 

3. SnapCenter をダウンロードしてインストールします。 

4. バックアップと回復の操作。 

>[!NOTE]
> これらの手順では、Azure のサブスクリプションと Oracle HaaS のテナントの間に ExpressRoute 接続が既に作成されているものとします。

## <a name="create-a-support-ticket-for-user-role-storage-setup"></a>ユーザー ロール ストレージ設定のサポート チケットを作成する

1. Azure portal を開き、[サブスクリプション] ページに移動します。 BareMetal のサブスクリプションを選択します。
2. BareMetal のサブスクリプション ページで、 **[リソース グループ]** を選択します。
3. リージョン内の適切なリソース グループを選択します。
    
    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/select-resource-group.png" alt-text="サブスクリプション ページのリソース グループを示すスクリーンショット。":::

4. Azure ストレージ上の SAP HANA に対応する SKU エントリを選択します。 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/select-sku.png" alt-text="Azure 上の SAP HANA の種類が強調されている SKU を示すリソース グループのスクリーンショット。":::

5. **[新しいサポート リクエスト]** を選択します。

6. **[基本]** タブで、チケットの次の情報を指定します。
    - **[問題の種類]** : 技術
    -   **[サブスクリプション]** : 自分のサブスクリプション
    -   **[サービス]** : BareMetal インフラストラクチャ
    -   **[リソース]:** 自分のリソース グループ
    -   **[概要]** : SnapCenter アクセスのセットアップ
    -   **[問題の種類]** : 構成とセットアップ
    -   **[問題のサブタイプ]:** SnapCenter のセットアップ

7. サポート チケットの  **[説明]** の  **[詳細]**  タブで、詳細用のテキスト ボックスに *.pem ファイルの内容を貼り付けます。 *.pem ファイルを zip にしてアップロードすることもできます。 SnapCenter ユーザー用の公開キーは snapcenter.pem です。 次の例を使用して、BareMetal インスタンスのいずれかから *.pem ファイルを作成します。 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/pem.png" alt-text=".pem ファイルのサンプル コンテンツを示すスクリーンショット。":::

    >[!NOTE]
    >"snapcenter" というファイルの名前は、REST API 呼び出しを行うために必要なユーザー名です。 そのため、ファイル名をわかりやすくすることをお勧めします。

8.  **[確認と作成]** を選択し、サポート チケットを確認します。

9.  公開キー証明書が送信されると、Microsoft によってテナントの SnapCenter ユーザー名とストレージ仮想マシン (SVM) の IP アドレスが設定されます。 Microsoft から SVM IP が提供されます。

10. SVM IP を受け取ったら、制御する SVM にアクセスするためのパスワードを設定します。

    HANA Large Instance 環境へのアクセス権を持ち、パスワードの設定に使用される、仮想ネットワーク内の HANA Large Instance または VM からの REST 呼び出しの例を次に示します。
    
    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/sample-rest-call.png" alt-text="REST 呼び出しのサンプルを示すスクリーンショット。":::

11. *.pem ファイルの作成で使用される BareMetal インスタンスでアクティブになっているプロキシ変数がないことを確認します。

     :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/ensure-no-proxy.png" alt-text="*.pem ファイルの作成で BareMetal インスタンスにアクティブなプロキシ変数がないようにするために設定されていない http プロキシを示すスクリーンショット。":::

12. クライアント コンピューターから、有効な REST コマンドのユーザー名とパスワードなしでコマンドを実行できるようになります。 接続をテストします。 

    プロキシなし:

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/connection-test.png" alt-text="接続のテストと、ユーザー名とパスワードなしで有効になる REST コマンドを示すスクリーンショット。":::


       >[!NOTE]
       > コマンドが失敗した理由の詳細を提供するには、curl コマンドに "--verbose" を追加します。

## <a name="install-snapcenter"></a>SnapCenter をインストールする

1. プロビジョニングされた Windows VM で、[NetApp の Web サイト](https://mysupport.netapp.com/site/products/all/details/snapcenter/downloads-tab)を参照します。

2. サインインして SnapCenter をダウンロードします。 [ダウンロード] > [SnapCenter] > **[バージョン 4.4]** を選択します。

3. SnapCenter 4.4 をインストールします。 **[次へ]** を選択します。

    インストーラーによって、VM の前提条件が確認されます。 特に大規模な環境では、VM のサイズに注意してください。 再起動が保留中になっているかもしれませんが、インストールを続けてかまいません。

4. SnapCenter のユーザー資格情報を構成します。 既定では、インストールのためにアプリケーションを起動した Windows ユーザーが設定されます。 ポートの競合がない限り、既定のポートを使用することをお勧めします。

    インストール ウィザードが完了するには少し時間がかかり、進行状況が表示されます。
 
5. インストールが終わったら、 **[完了]** を選択します。  SnapCenter Web ポータルの Web アドレスを記録しておきます。  インストールの完了後にデスクトップに表示される SnapCenter アイコンをダブルクリックしてアクセスすることもできます。
 
## <a name="disable-enhanced-messaging-service-ems-messages-to-netapp-auto-support"></a>NetApp 自動サポートへの拡張メッセージング サービス (EMS) メッセージを無効にする

EMS データ収集が既定で有効になり、インストール日から 7 日ごとに実行されます。 いつでもデータ収集を無効にすることができます。

1. PowerShell のコマンド ラインから、SnapCenter とのセッションを確立します。

   ```powershell-interactive
   Open-SmConnection
   ```

2. 自分の資格情報でサインインします。

3. EMS のデータ収集を無効にします。 

   ```powershell-interactive
   Disable-SmDataCollectionEms
   ```
   
## <a name="next-steps"></a>次のステップ

SnapCenter の構成方法を確認します。

> [!div class="nextstepaction"]
> [SnapCenter を構成する](configure-snapcenter-oracle-baremetal.md)
