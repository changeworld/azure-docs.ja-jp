---
title: Fortanix Confidential Computing Manager によってアプリを実行する
description: Fortanix Confidential Computing Manager を使用して、コンテナー化されたイメージを変換する方法について説明します。
services: virtual-machines
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 03/24/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: fcf3e3a1aab9114ceccc9eb32f96347ce3ed0fc2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306418"
---
# <a name="run-an-application-by-using-fortanix-confidential-computing-manager"></a>Fortanix Confidential Computing Manager を使用してアプリケーションを実行する

[Fortanix](https://www.fortanix.com/) の [Fortanix Confidential Computing Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.em_managed?tab=Overview) および [Node Agen](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) を使用して Azure Confidential Computing でアプリケーションを実行する方法について説明します。


Fortanix は、Azure インフラストラクチャで動作する製品とサービスを提供するサード パーティのソフトウェア ベンダーです。 Azure 用の類似したコンフィデンシャル コンピューティング サービスを提供するサード パーティ プロバイダーは他にもあります。

> [!Note]
> このドキュメントで参照されている製品には、Microsoft から提供されていないものもあります。 Microsoft では、この情報を便宜上提供しているだけです。 これらの Microsoft 以外の製品について言及していても、Microsoft が公認しているわけではありません。

このチュートリアルでは、アプリケーション イメージを、コンフィデンシャル コンピューティングで保護されたイメージに変換する方法について説明します。 この環境では、Azure DCsv2 シリーズの Intel SGX 対応仮想マシンで稼働する [Fortanix](https://www.fortanix.com/) ソフトウェアを使用します。 このソリューションでは、本人確認やデータ アクセス制御のような重要なセキュリティ ポリシーを調整します。

Fortanix のサポートについては、[Fortanix Slack コミュニティ](https://fortanix.com/community/)に参加してください。 `#enclavemanager` チャネルを使用します。

## <a name="prerequisites"></a>[前提条件]

- Fortanix Confidential Computing Manager アカウントを持っていない場合は、開始する前に[サインアップ](https://ccm.fortanix.com/auth/sign-up)します。
- 変換されたアプリケーション イメージを適用するためのプライベート [Docker](https://docs.docker.com/) レジストリが必要です。
- Azure サブスクリプションを持っていない場合は、開始する前に[アカウントを作成](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)します。

> [!NOTE]
> 無料試用版アカウントでは、このチュートリアルで使用されている仮想マシンにアクセスできません。 このチュートリアルを完了するには、従量課金制サブスクリプションが必要です。

## <a name="add-an-application-to-fortanix-confidential-computing-manager"></a>Fortanix Confidential Computing Manager にアプリケーションを追加する

1. [Fortanix Confidential Computing Manager (Fortanix CCM)](https://ccm.fortanix.com) にサインインします。
1. **[Accounts]** ページにアクセスし、 **[ADD ACCOUNT]** を選択して新しいアカウントを作成します。

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-account-new.png" alt-text="アカウントの作成方法を示すスクリーンショット。":::

1. アカウントが作成されたら、 **[SELECT ACCOUNT]** をクリックして、新しく作成したアカウントを選択します。 これで、コンピューティング ノードの登録とアプリケーションの作成を開始できます。
1. **[Applications]** タブで、 **[+ APPLICATION]** を選択してアプリケーションを追加します。 この例では、Python Flask サーバーを実行する Enclave OS アプリケーションを追加します。

1. **Enclave OS アプリケーション** の **[ADD]** ボタンを選択します。

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/add-enclave-application.png" alt-text="アプリケーションの追加方法を示すスクリーンショット。":::

   > [!NOTE]
   > このチュートリアルでは、Enclave OS アプリケーションのみの追加について説明します。 EDP Rust アプリケーションの追加の詳細については、[EDP Rust アプリの Confidential Computing Manager への取り込み](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Confidential-Computing-Manager)に関するページを参照してください。

1. このチュートリアルでは、サンプル アプリケーションに Fortanix Docker レジストリを使用します。 次の設定の指定された値を入力します。 プライベート Docker レジストリを使用して、出力イメージを格納します。

    - **アプリケーション名**:Python アプリケーション サーバー
    - **説明**:Python Flask Server
    - **入力イメージ名**: fortanix/python-flask
    - **出力イメージ名**: fortanix-private/python-flask-sgx (独自のレジストリに置き換えます)。
    - **ISVPRODID**:1
    - **ISVSVM**:1
    - **メモリ サイズ**:1 GB
    - **スレッド数**:128

      *省略可能*: 変換されていないアプリケーションを実行します。
    - **Docker Hub**: [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **アプリ**: fortanix/python-flask

      次のコマンドを実行します。

      ```bash
         sudo docker run fortanix/python-flask
      ```
      > [!NOTE]
      > 出力イメージの格納に、プライベート Docker レジストリを使用することはお勧めしません。

1. 証明書を追加します。 次の値を入力してから、 **[次へ]** を選択します。
    - **[Domain]** : myapp.domain.com
    - **[種類]** :Confidential Computing Manager によって発行された証明書
    - **[Key path]** : /run/key.pem
    - **[Key type]** :RSA
    - **[Certificate path]** : /run/cert.pem
    - **[RSA Key Size]** :2048 ビット

## <a name="create-an-image"></a>イメージを作成する

Fortanix CCM イメージは、アプリケーションのソフトウェア リリースまたはバージョンです。 各イメージは、1 つのエンクレーブ ハッシュ (MRENCLAVE) に関連付けられています。

1. **[Add image]** ページで、 **[Output image name]** にレジストリ資格情報を入力します。 これらの資格情報は、イメージがプッシュされるプライベート Docker レジストリにアクセスするために使用されます。 入力イメージはパブリック レジストリに格納されるため、入力イメージの資格情報を提供する必要はありません。
1. イメージ タグを入力し、 **[CREATE]** を選択します。

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-image.png" alt-text="イメージの作成方法を示すスクリーンショット。":::


## <a name="domain-and-image-allowlist"></a>ドメインとイメージの許可リスト

ドメインが許可リストに追加されたアプリケーションは、Fortanix Confidential Computing Manager から TLS 証明書を取得します。 Enclave OS アプリケーションが起動すると、その TLS 証明書を受信するために Fortanix Confidential Computing Manager に接続されます。

画面の左側の **[Tasks]** タブで、保留中の要求を承認して、ドメインとイメージを許可します。

## <a name="enroll-the-compute-node-agent-in-azure"></a>Azure にコンピューティング ノード エージェントを登録する

### <a name="create-and-copy-a-join-token"></a>参加トークンを作成してコピーする

次に Fortanix Confidential Computing Manager で、トークンを作成します。 このトークンにより、Azure のコンピューティング ノードが自身を認証できるようになります。 Azure 仮想マシンには、このトークンが必要です。

1. **[Compute Nodes]** タブで、 **[ENROLL NODE]** を選択します。
1. **[COPY]** ボタンを選択して、参加トークンをコピーします。 コンピューティング ノードではこの参加トークンを使用して自らを認証します。

### <a name="enroll-nodes-into-fortanix-node-agent"></a>Fortanix Node Agent にノードを登録する

Fortanix ノード エージェントを作成すると、仮想マシン、ネットワーク インターフェイス、仮想ネットワーク、ネットワーク セキュリティ グループ、およびパブリック IP アドレスが Azure リソース グループにデプロイされます。 Azure サブスクリプションは、仮想マシンに対して時間単位で課金されます。 Fortanix ノード エージェントを作成する前に、DCsv2 シリーズについての Azure [仮想マシンの価格に関するページ](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)を参照してください。 使用していない Azure リソースを削除します。

1. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) に移動し、Azure 資格情報を使用してサインインします。
1. 検索ボックスに「**Fortanix Confidential Computing Node Agent**」と入力します。 検索結果で **[Fortanix Confidential Computing Node Agent]** を選択して、[アプリのホーム ページ](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent?tab=OverviewFortanix)に移動します。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/search-fortanix-marketplace.png" alt-text="アプリのホーム ページにアクセスする方法を示すスクリーンショット。":::
1. **[今すぐ入手]** を選択し、必要に応じて情報を入力して、続いて **[続行]** を選択します。 Azure portal にリダイレクトされます。
1. **[作成]** を選択して、Fortanix Confidential Computing Node Agent のデプロイ ページに移動します。
1. このページでは、仮想マシンをデプロイするための情報を入力します。 この VM は、Fortanix Node Agent ソフトウェアがインストールされた、Azure からの DCsv2 シリーズの Intel SGX 対応の仮想マシンです。 ノード エージェントにより、変換されたイメージは Azure の Intel SGX ノードでさらに安全に実行できます。 仮想マシンおよび関連付けられているリソースをデプロイするサブスクリプションとリソース グループを選択します。

   > [!NOTE]
   > 制約は、DCsv2 シリーズの仮想マシンを Azure にデプロイするときに適用されます。 追加のコアのクォータを要求することが必要な場合もあります。 詳細については、[Azure VM でのコンフィデンシャル コンピューティング ソリューション](./virtual-machine-solutions-sgx.md)に関するページを参照してください。

1. 使用可能なリージョンを選択します。
1. **[ノード名]** ボックスで、仮想マシンの名前を入力します。
1. 仮想マシンへの認証に使用するユーザー名とパスワード (または SSH キー) を入力します。
1. 既定の **OS ディスク サイズ** である **200** のままにします。 **VM サイズ** を選択します。 (このチュートリアルでは **Standard_DC4s_v2** が適切です)。
1. **参加トークン** ボックスに、このチュートリアルで前に作成したトークンを貼り付けます。

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/deploy-fortanix-node-agent-protocol.png" alt-text="リソースのデプロイ方法を示すスクリーンショット。":::

1. **[Review + create]\(レビュー + 作成\)** を選択します。 検証に合格したことを確認し、 **[作成]** を選択します。 すべてのリソースをデプロイすると、コンピューティング ノードが Fortanix Confidential Computing Manager に登録されます。

## <a name="run-the-application-image-on-the-compute-node"></a>コンピューティング ノードでアプリケーション イメージを実行する

次のコマンドを実行して、アプリケーションを実行します。 ノードの IP、ポート、変換されたイメージ名は必ず、アプリケーションの値に変更してください。

このチュートリアルでは、次のコマンドを実行します。

```bash
    sudo docker run \
        --device /dev/isgx:/dev/isgx \
        --device /dev/gsgx:/dev/gsgx \
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket \
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

このコマンドの説明:

- `52.152.206.164` は、ノード エージェントのホスト IP です。
- `9092` は、Node Agent でリッスンする既定のポートです。
- `fortanix-private/python-flask-sgx` は、変換されたアプリです。 これは、Fortanix Confidential Computing Manager の Web ポータルで見つかります。 **[イメージ]** タブの **[イメージ]** テーブルの **[イメージ名]** 列にあります。

## <a name="verify-and-monitor-the-running-application"></a>実行中のアプリケーションを確認して監視する

1. [Fortanix Confidential Computing Manager](https://ccm.fortanix.com/console) に戻ります。
1. ノードを登録した **アカウント** で作業していることを確認します。
1. **[アプリケーション]** タブで、コンピューティング ノードが関連付けられている実行中のアプリケーションがあることを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、リソース グループ、仮想マシン、関連リソースを削除できます。 リソース グループを削除すると、変換されたイメージに関連付けられているノードの登録が解除されます。

仮想マシンのリソース グループを選択し、 **[削除]** を選択します。 リソース グループの名前を確認してリソースの削除を終了します。

作成した Fortanix Confidential Computing Manager アカウントを削除するには、Fortanix Confidential Computing Manager の[アカウント ページ](https://ccm.fortanix.com/accounts)に移動します。 削除するアカウントにマウス ポインターを移動します。 右上隅の垂直方向の黒いドットを選択し、 **[DELETE ACCOUNT]** を選択します。

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/delete-account.png" alt-text="アカウントを削除する方法を示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Fortanix ツールを使用して、コンフィデンシャル コンピューティング仮想マシン上で実行されるようにアプリケーション イメージを変換しました。 Azure におけるコンフィデンシャル コンピューティング仮想マシンの詳細については、「[仮想マシン上のソリューション](virtual-machine-solutions-sgx.md)」を参照してください。

Azure のコンフィデンシャル コンピューティング オファリングの詳細については、「[Azure におけるコンフィデンシャル コンピューティング](overview.md)」を参照してください。

また、Azure 上で ([Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) や [Scone](https://sconedocs.github.io)などの) 他のサードパーティ オファリングを使用して同様のタスクを実行する方法についても確認できます。
