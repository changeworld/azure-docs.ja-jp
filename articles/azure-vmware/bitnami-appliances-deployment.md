---
title: Bitnami 仮想アプライアンスをデプロイする
description: Azure VMware Solution プライベート クラウドにデプロイできるよう Bitnami でパッケージ化された仮想アプライアンスについて説明します。
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 41201eddaa2ba14110f36c18350860efd4d3a93e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699237"
---
# <a name="bitnami-appliance-deployment"></a>Bitnami アプライアンスのデプロイ

VMware の Bitnami には、すぐに使用できる仮想アプライアンスが多数そろっています。 [VMware Marketplace](https://marketplace.cloud.vmware.com/) にある Bitnami の vSphere 互換アプライアンスをデプロイでき、頻繁に利用される多くのオープンソース ソフトウェア プロジェクトも用意されています。

この記事では、Bitnami でパッケージ化された次の仮想アプライアンスを、Azure VMware Solution プライベート クラウドにインストールし、設定する方法を説明します。

- LAMP

- Jenkins

- PostgreSQL

- NGINX

- RabbitMQ



## <a name="prerequisites"></a>前提条件

- [少なくとも 3 つのノードを持つ、デプロイ済みの](deploy-azure-vmware-solution.md) Azure VMware Solution プライベート クラウド。

- [ネットワーク計画のチェックリスト](tutorial-network-checklist.md)に関する記事の説明どおりに設定したネットワーク。



## <a name="step-1-download-the-bitnami-virtual-appliance-ovaovf-file"></a>手順 1. Bitnami 仮想アプライアンス OVA/OVF ファイルをダウンロードする


1. [VMware Marketplace](https://marketplace.cloud.vmware.com/) に移動し、Azure VMware Solution プライベート クラウドにインストールする仮想アプライアンスをダウンロードします:

   - [Bitnami でパッケージ化された LAMP 仮想アプライアンス](https://marketplace.cloud.vmware.com/services/details/lampstack?slug=true)

   - [Jenkins](https://marketplace.cloud.vmware.com/services/details/jenkins?slug=true)

   - [PostgreSQL](https://marketplace.cloud.vmware.com/services/details/postgresql?slug=true)

   - [NGINX](https://marketplace.cloud.vmware.com/services/details/nginxstack?slug=true)

   - [RabbitMQ](https://marketplace.cloud.vmware.com/services/details/rabbitmq?slug=true)

1. バージョンを選択し、 **[Download]\(ダウンロード\)** をクリックし、EULA ライセンスに同意します。 

   >[!NOTE]
   >仮想マシンでファイルにアクセスできることを確認します。



## <a name="step-2-access-the-local-vcenter-of-your-private-cloud"></a>手順 2. プライベート クラウドのローカル vCenter にアクセスする

1. [Azure portal](https://portal.azure.com) にサインインし、目的のプライベート クラウド、 **[Manage** > **Idendity]\(ID の管理\)** を順にクリックします。

1. vCenter の URL、ユーザー名、パスワードをコピーします。 これらは、仮想マシン (VM) へのアクセスに使用します。 

1. **[Overview]\(概要\)** をクリックし、VM を選択し、RDP でその VM に接続します。 接続に関してサポートが必要な場合は、[仮想マシンへの接続](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)に関する記事で詳しい情報をご覧ください。

1. VM でブラウザーを開いて vCenter の URL に移動します。 

1. 先ほどコピーした `cloudadmin@vsphere.local` ユーザー サインイン情報でサインインします。

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="VMware vSphere サインイン ページを示すスクリーンショット。" border="true":::




## <a name="step-3-install-the-bitnami-ovaovf-file-in-vcenter"></a>手順 3. vCenter で Bitnami OVA/OVF ファイルをインストールする

1. LAMP 仮想アプライアンスをインストールするクラスターを右クリックして、 **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** を選択します。

1. **[Local file]\(ローカル ファイル\)** を選択し、先ほどダウンロードした OVF ファイルを選択します。 **[次へ]** を選択します。

1. データ センターを選択し、仮想アプライアンス VM の名前を入力します (例: **bitnami-lampstack**)。 **[次へ]** を選択します。

1. VM を実行するコンピューティング リソースに ESXi ホストを選択し、 **[Next]\(次へ\)** をクリックします。

1. 内容を確認して **[Next]\(次へ\)** を選択します。

1. 使用許諾契約書に同意し、**[次へ]** を選択します。

1. VM のストレージを選択して **[Next]\(次へ\)** をクリックします。

1. VM の接続先ネットワークを選択して **[Next]\(次へ\)** をクリックします。

1. VM とネットワークのプロパティなど、必要な情報を入力してテンプレートをカスタマイズします。 **[次へ]** を選択します。  

1. 構成の設定を確認して **[Finish]\(終了\)** をクリックします。

1. **[Task Console]\(タスク コンソール\)** で、OVF テンプレートのデプロイの状態が完了になっていることを確認します。

1. インストールが終わったら、 **[Actions]\(アクション\)** の **[Power on]\(パワー オン\)** をクリックしてアプライアンスを起動します。 

1. vCenter のコンソールで **[Launch Web Console]\(Web コンソールの起動\)** をクリックし、Bitnami 仮想アプライアンスにサインインします。 デフォルトのユーザー名とパスワードは、[Bitnami 仮想アプライアンスのサポート ドキュメント](https://docs.bitnami.com/vmware-marketplace/faq/get-started/find-credentials/) をご確認ください。

   >[!NOTE]
   >デフォルトのパスワードは、より安全なものに変更できます。 詳しくは...をご覧ください。



## <a name="step-4-assign-a-static-ip-to-the-virtual-appliance"></a>手順 4. 静的 IP を仮想アプライアンスに割り当てる

このステップでは、*bootproto* と *onboot* パラメーターを変更して、Bitnami 仮想アプライアンスに静的 IP アドレスを割り当てます。 

1. ネットワーク構成ファイルを検索します。 

   ```bash
   sudo find /etc -name \*ens160\*
   ```

1. *\/etc\/sysconfig\/network-scripts\/ifcfg-ens160* ファイルを編集してブート パラメーターを変更します。 静的 IP、ネットマスク、ゲートウェイ アドレスを追加します。

   - bootproto=static

   - onboot=yes


1. **ifcfg-ens160** ファイルに対する変更を確認します。

   ```bash
   cat ifcfg-ens160  
   ```

1. ネットワーク サービスを再起動します。 このステップでは、最初にネットワーク サービスを停止し、それから IP の構成を適用します。 

   ```bash
   sudo systemctl restart network
   ```

1. ゲートウェイ IP アドレスに ping を打ち、ネットワークの構成と VM への接続が有効であることを確認します。

1. デフォルト ルート 0.0.0.0 が表示されることを確認します。

   ```bash
   sudo route -n
   ```



## <a name="step-5-enable-ssh-access-to-the-virtual-appliance"></a>手順 5. 仮想アプライアンスへの SSH アクセスを有効にする

このステップでは、仮想アプライアンスで SSH を有効にして、リモート アクセス制御ができるようにします。 SSH サービスは既定では無効になっています。 ホスト コンソールへの接続には PuTTY も使用します。

1. SSH サービスを有効にし、起動します。

   ```bash
   sudo rm /etc/ssh/sshd_not_to_be_run
   sudo systemctl enable sshd
   sudo systemctl start sshd
   ```

1. *\/etc\/ssh\/sshd_config* ファイルを編集してパスワード認証を変更します。

   ```bash
   PasswordAuthentication yes
   ```

1. **sshd_config** ファイルへの変更を確認します。

   ```bash
   sudo cat sshd_config
   ```

1. ファイルへの変更をリロードします。 

   ```bash
   sudo /etc/init.d/ssh force-reload
   ```

1. PuTTY を開き、 **[SSH]** オプションを選択して、ホスト名とポート番号の **22* を入力します。 その後、 **[開く]** を選択します。 

1. 仮想アプライアンス コンソールのプロンプトで Bitnami のユーザー名とパスワードを入力してホストに接続します。 



