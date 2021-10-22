---
title: チュートリアル:Linux 仮想マシン スケール セットを作成する
description: 仮想マシン スケール セットを使って、Linux VM に高可用性アプリを作成およびデプロイする方法を説明します。
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machines
ms.collection: linux
ms.date: 10/15/2021
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 10f7202e2525920edd4c65b2e35cea51b9751abb
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165074"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux"></a>チュートリアル: 仮想マシン スケール セットを作成して Linux に高可用性アプリをデプロイする 

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: ユニフォーム スケール セット

仮想マシン スケール セットでは、[フレキシブル オーケストレーション](../flexible-virtual-machine-scale-sets.md)を使用して、負荷分散が行われる VM のグループを作成して管理できます。 需要または定義されたスケジュールに応じて、VM インスタンスの数を自動的に増減させることができます。

このチュートリアルでは、仮想マシン スケール セットを Azure にデプロイします。次の方法について説明します。

> [!div class="checklist"]
> * リソース グループを作成する。
> * ロード バランサーを使用してフレキシブル スケール セットを作成する。
> * Nginx をスケール セット インスタンスに追加する。
> * HTTP トラフィックに対してポート 80 を開く。
> * スケール セットをテストする。


## <a name="scale-set-overview"></a>スケール セットの概要

スケール セットには、次のような主な利点があります。
- 複数の VM の作成と管理が容易である
- 複数の障害ドメインに VM を分散することにより、高可用性とアプリケーションの回復性が提供される
- リソースの需要の変化に応じた、アプリケーションの自動スケーリングを可能にする
- 大規模に動作する

Azure ではフレキシブル オーケストレーションを使用することで、Azure VM エコシステム全体で統合されたエクスペリエンスを実現できます。 フレキシブル オーケストレーションでは、リージョン内の障害ドメインまたは可用性ゾーン内で VM を分散することで、(最大 1,000 個の VM まで) 高可用性を保証します。 これにより、以下のようなクォーラムベースまたはステートフル ワークロードを実行するのに欠かせない障害ドメインを分離したまま、アプリケーションをスケールアウトできます。
- クォーラムベースのワークロード
- オープンソース データベース
- ステートフル アプリケーション
- 高可用性と大きいスケールが必要なサービス
- 仮想マシンの種類の混在、またはスポットとオンデマンドの VM の併用が求められるサービス
- 既存の可用性セット アプリケーション

[オーケストレーション モード](../../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md)での均一スケール セットとフレキシブル スケール セットの違いについて確認してください。



## <a name="create-a-scale-set"></a>スケール セットを作成する

Azure portal を使用してフレキシブル スケール セットを作成します。

1. [Azure Portal](https://portal.azure.com)を開きます。
1. **[仮想マシン スケール セット]** を検索して選択します。
1. **[仮想マシン スケール セット]** ページで **[作成]** を選択します。 **[仮想マシン スケール セットを作成する]** が開きます。
1. **[サブスクリプション]** で、使用するサブスクリプションを選択します。
1. **[リソース グループ]** で、**[新規作成]** を選択し、名前に「*myVMSSRG*」と入力して **[OK]** を選択します。
    :::image type="content" source="media/tutorial-create-vmss/flex-project-details.png" alt-text="プロジェクトの詳細。":::
1. **[仮想マシン スケール セットの名前]** に、「*myVMSS*」と入力します。
1. **[地域]** で、*[米国東部]* のように、自分の地域に近いリージョンを選択します。
    :::image type="content" source="media/tutorial-create-vmss/flex-details.png" alt-text="名前と地域。":::
1. この例では、**[可用性ゾーン]** は空白のままにします。
1. **[オーケストレーション モード]** で、**[フレキシブル]** を選択します。
1. [Fault domain count]\(障害ドメイン数\) は、既定値の *[1]* のままにするか、ドロップダウンから別の値を選択します。
   :::image type="content" source="media/tutorial-create-vmss/flex-orchestration.png" alt-text="[フレキシブル オーケストレーション モード]"::: を選択します。
1. **[イメージ]** に、*[Ubuntu 18.04 LTS]* を選択します。
1. **[サイズ]** は、既定値のままにするか、*[Standard_E2s_V3]* のようにサイズを選択します。
1. **[ユーザー名]** に「*azureuser*」と入力します。
1. **[SSH public key source]\(SSH 公開キー ソース\)** では、 **[新しいキー ペアの生成]** を既定値のまま残し、 **[Key pair name]\(キー ペアの名前\)** に「*myKey*」と入力します。
    :::image type="content" source="media/tutorial-create-vmss/flex-admin.png" alt-text="認証の種類を選択し、管理者の資格情報を入力する、[管理者アカウント] セクションのスクリーンショット。":::
1. **[ネットワーク]** タブの **[負荷分散]** で、**[ロード バランサーを使用する]** を選択します。
1. **[負荷分散のオプション]** は、既定の **[Azure load balancer]\(Azure ロード バランサー\)** のままにします。
1. **[ロード バランサーを選択します]** で、**[Create new]\(新規作成\)** を選択します。
    :::image type="content" source="media/tutorial-create-vmss/load-balancer-settings.png" alt-text="ロード バランサーの設定。":::
1. **[Create a load balancer]\(ロードバランサーの作成\)** ページで、ロード バランサーの名前と **パブリック IP アドレス名** を入力します。
1. **[ドメイン名ラベル]** には、ドメイン名のプレフィックスとして使用する名前を入力します。 この名前は一意である必要があります。
1. 完了したら、 **[作成]** を選択します。
    :::image type="content" source="media/tutorial-create-vmss/flex-load-balancer.png" alt-text="ロード バランサーの作成。":::
1. **[ネットワーク]** タブに戻り、バックエンド プールの既定の名前をそのまま使用します。
1. **[スケーリング]** タブで、インスタンス数を既定の *[2]* のままにするか、独自の値を追加します。 これは、作成される VM の数です。この値を変更した場合は、サブスクリプションのコストと制限に注意してください。
1. **[スケーリング ポリシー]** は、*[手動]* に選択されたままにします。
    :::image type="content" source="media/tutorial-create-vmss/flex-scaling.png" alt-text="スケーリング ポリシーの設定。":::
1. **[詳細]** タブを選択します。
1. **[Custom data and cloud init]\(カスタム データと cloud-init\)** で以下をコピーして **[Custom data]\(カスタム データ\)** テキスト ボックスに貼り付けます。
    ```yml
    #cloud-config
    package_upgrade: true
    packages:
      - nginx
      - nodejs
      - npm
    write_files:
      - owner: www-data:www-data
      - path: /etc/nginx/sites-available/default
        content: |
          server {
            listen 80;
            location / {
              proxy_pass http://localhost:3000;
              proxy_http_version 1.1;
              proxy_set_header Upgrade $http_upgrade;
              proxy_set_header Connection keep-alive;
              proxy_set_header Host $host;
              proxy_cache_bypass $http_upgrade;
            }
          }
      - owner: azureuser:azureuser
      - path: /home/azureuser/myapp/index.js
        content: |
          var express = require('express')
          var app = express()
          var os = require('os');
          app.get('/', function (req, res) {
            res.send('Hello World from host ' + os.hostname() + '!')
          })
          app.listen(3000, function () {
            console.log('Hello world app listening on port 3000!')
          })
    runcmd:
      - service nginx restart
      - cd "/home/azureuser/myapp"
      - npm init
      - npm install express -y
      - nodejs index.js
    ```
1. 完了したら、 **[確認および作成]** を選択します。
1. 検証が成功したことを確認したら、ページの下部にある **[作成]** を選択してスケール セットをデプロイできます。
1. **[新しいキー ペアの生成]** ウィンドウが開いたら、 **[Download private key and create resource]\(秘密キーをダウンロードし、リソースを作成する\)** を選択します。 キー ファイルは **myKey.pem** としてダウンロードされます。 `.pem` ファイルがダウンロードされた場所を必ず把握してください。そのパスが次の手順で必要になります。
1. デプロイが完了したら、**[リソースに移動]** を選択し、スケール セットを表示します。


## <a name="view-the-vms-in-your-scale-set"></a>スケール セット内の VM を表示する

スケール セットのページで、左側のメニューから **[インスタンス]** を選択します。 

スケール セットの一部である VM の一覧が表示されます。 この一覧の内容:

- VM の名前
- VM によって使用されるコンピューター名。
- VM の現在の状態 (例: *[実行中]*)。
- VM の *[プロビジョニングの状態]* (例: *[Succeeded]\(成功\)*)。

:::image type="content" source="media/tutorial-create-vmss/instances.png" alt-text="スケール セット インスタンスに関する情報の表。":::


## <a name="open-port-80"></a>ポート 80 を開く 

ネットワーク セキュリティ グループ (NSG) に受信規則を追加して、スケール セットのポート 80 を開きます。

1. スケール セットのページで、左側のメニューから **[ネットワーク]** を選択します。 **[ネットワーク]** ページが開きます。
1. **[受信ポートの規則を追加する]** を選択します。 **[受信セキュリティ規則の追加]** ページが開きます。
1. **[サービス]** で *[HTTP]* を選択し、ページの下部にある **[追加]** を選択します。

## <a name="test-your-scale-set"></a>スケール セットのテスト

ブラウザーからスケール セットに接続してテストします。

1. スケール セットの **[概要]** ページで、パブリック IP アドレスをコピーします。
1. ブラウザーで別のタブを開いて、アドレス バーにその IP アドレスを貼り付けます。
1. ページが読み込まれたら、表示されているコンピューター名をメモしておきます。 
1. コンピューター名の変更が反映されるまでページを更新します。 

## <a name="delete-your-scale-set"></a>スケール セットの削除

完了したら、リソース グループを削除する必要があります。これにより、スケール セットにデプロイしたすべてのものが削除されます。

1. スケール セットのページで、**[リソース グループ]** を選択します。 自分のリソース グループのページが開きます。
1. ページの上部で **[リソース グループの削除]** を選択します。
1. **[削除しますか]** ページで、リソース グループの名前を入力し、**[削除]** を選択します。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、仮想マシン スケール セットを作成しました。 以下の方法を学習しました。

> [!div class="checklist"]
> * リソース グループを作成する。
> * ロード バランサーを使用してフレキシブル スケール セットを作成する。
> * Nginx をスケール セット インスタンスに追加する。
> * HTTP トラフィックに対してポート 80 を開く。
> * スケール セットをテストする。

次のチュートリアルでは、仮想マシンでの負荷分散の概念について詳しく説明します。

> [!div class="nextstepaction"]
> [仮想マシンを負荷分散する](tutorial-load-balancer.md)
