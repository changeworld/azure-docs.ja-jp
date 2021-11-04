---
title: Azure portal でプライベート アクセスを使用して Azure Database for MySQL フレキシブル サーバーに接続する
description: この記事では、Azure portal を使用して、Azure Database for MySQL フレキシブル サーバーを作成し、プライベート アクセスで接続する手順について説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 04/18/2021
ms.openlocfilehash: f8d32c97014e9e38f2ad5a3c6d6a1b96d4e190f5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425476"
---
# <a name="connect-azure-database-for-mysql-flexible-server-with-private-access-connectivity-method"></a>プライベート アクセスの接続方法を使用して Azure Database for MySQL フレキシブル サーバーに接続する

Azure Database for MySQL フレキシブル サーバーは、高可用性 MySQL サーバーをクラウドで実行、管理、スケーリングするために使用できる管理サービスです。 このクイックスタートでは、Azure portal を使用して仮想ネットワークにフレキシブル サーバーを作成する方法について説明します。



[!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]


## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする
[Azure ポータル](https://portal.azure.com/)にアクセスします。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL フレキシブル サーバーを作成する

フレキシブル サーバーは、定義済みの一連の[コンピューティング リソースとストレージ リソース](./concepts-compute-storage.md)を使って作成します。 サーバーは、[Azure リソース グループ](../../azure-resource-manager/management/overview.md)内に作成します。

フレキシブル サーバーを作成するには、これらの手順を実行します。

1. ポータルで **[Azure Database for MySQL サーバー]** を検索して選択します。

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png" alt-text="[Azure Database for MySQL サーバー] の検索を示すスクリーンショット。" lightbox="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png":::

2. **[追加]** を選択します。

3. **[Select Azure Database for MySQL deployment option]\(Azure Database for MySQL のデプロイ オプションの選択\)** ページで、デプロイ オプションとして **[フレキシブル サーバー]** を選択します。

    > :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-option.png" alt-text="[フレキシブル サーバー] オプションを示すスクリーンショット。" lightbox="./media/quickstart-create-connect-server-vnet/deployment-option.png":::

4. **[基本]** タブで、**サブスクリプション**、**リソース グループ**、**リージョン**、**管理者のユーザー名**、**管理者のパスワード** を入力します。  既定値を使用すると、1 つの仮想コア、2GiB のメモリ、32GiB のストレージを使用するバースト可能 SKU を備えたバージョン 5.7 の MySQL サーバーがプロビジョニングされます。 バックアップの保持期間は 7 日間です。 この構成は変更できます。

    > :::image type="content" source="./media/quickstart-create-connect-server-vnet/mysql-flexible-server-create-portal.png" alt-text="フレキシブル サーバー ページの [基本] タブを示すスクリーンショット。" lightbox="./media/quickstart-create-connect-server-vnet/mysql-flexible-server-create-portal.png":::

   > [!TIP]
   > 移行中のデータ読み込みを高速化するために、IOPS をコンピューティング サイズでサポートされている最大サイズまで増やし、後でスケールダウンしてコストを節約することをお勧めします。

5.  **[ネットワーク]** タブを開き、 **[プライベート アクセス]** を選択します。サーバーを作成した後に接続方法を変更することはできません。 **[仮想ネットワークの作成]** を選択し、新しい仮想ネットワーク **vnetenvironment1** を作成します。

    > :::image type="content" source="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-mysql-server.png" alt-text="[ネットワーク] タブと新しい VNET を示すスクリーンショット。" lightbox="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-mysql-server.png":::

6. 仮想ネットワーク名とサブネットの情報を入力したら **[OK]** を選択します。
    > :::image type="content" source="./media/quickstart-create-connect-server-vnet/show-server-vnet-information.png" alt-text="VNET の情報を確認する":::

7. **[確認と作成]** を選択して、フレキシブル サーバーの構成を確認します。

8. **[作成]** を選択して、サーバーをプロビジョニングします。 プロビジョニングには数分かかる場合があります。

9. デプロイが完了し、成功するまで待ちます。

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-success.png" alt-text="[ネットワーク] 設定と新しい VNET を示すスクリーンショット。" lightbox="./media/quickstart-create-connect-server-vnet/deployment-success.png":::

9.  **[リソースに移動]** を選択して、サーバーの **[概要]** ページを確認します。

## <a name="create-azure-linux-virtual-machine"></a>Azure Linux 仮想マシンを作成する

サーバーは仮想ネットワーク内にあるので、サーバーと同じ仮想ネットワーク内にある他の Azure サービスからしかサーバーに接続できません。 サーバーに接続して管理するために、Linux 仮想マシンを作成してみましょう。 仮想マシンは、**同じリージョン** と **同じサブスクリプション** に作成する必要があります。 この Linux 仮想マシンは、データベース サーバーを管理するための SSH トンネルとして使用できます。

1. サーバーが作成されたリソース グループに移動します。 **[追加]** を選択します。
2. **[Ubuntu Server 18.04 LTS]** を選択します
3. **[基本]** タブの **[Project details] (プロジェクトの詳細)** で、正しいサブスクリプションが選択されていることを確認し、リソース グループの **[新規作成]** を選択します。 名前として「*myResourceGroup*」と入力します。

   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/project-details.png" alt-text="仮想マシンの Azure サブスクリプションとリソース グループを選択する場所が示されている [プロジェクトの詳細] セクションのスクリーンショット" lightbox="../../virtual-machines/linux/media/quick-create-portal/project-details.png":::

2. **[インスタンスの詳細]** で、 **[仮想マシン名]** に「*myVM*」と入力し、データベース サーバーと同じ **[リージョン]** を選択します。

   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/instance-details.png" alt-text="仮想マシンの名前を指定し、そのリージョン、イメージ、サイズを選択する、[インスタンスの詳細] セクションのスクリーンショット" lightbox="../../virtual-machines/linux/media/quick-create-portal/instance-details.png":::

3. **[管理者アカウント]** で **[SSH 公開キー]** を選択します。

4. **[ユーザー名]** に「*azureuser*」と入力します。

5. **[SSH public key source]\(SSH 公開キー ソース\)** では、 **[新しいキー ペアの生成]** を既定値のまま残し、 **[Key pair name]\(キー ペアの名前\)** に「*myKey*」と入力します。

   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png" alt-text="認証の種類を選択し、管理者の資格情報を入力する、[管理者アカウント] セクションのスクリーンショット" lightbox="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png":::

6. **[受信ポートの規則]**  >  **[Public inbound ports] (パブリック受信ポート)** で、 **[Allow selected ports] (選択されたポートを許可する)** を選択してから、ドロップダウンから **[SSH (22)]** と **[HTTP (80)]** を選択します。

   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png" alt-text="受信接続が許可されるポートを選択する [受信ポートの規則] セクションのスクリーンショット" lightbox="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png":::

7. **[ネットワーク]** ページを選択して仮想ネットワークを構成します。 仮想ネットワークには、データベース サーバー用に作成した **vnetenvironment1** を選択します。

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png" alt-text="データベース サーバーの既存の仮想ネットワークを選択しているスクリーンショット" lightbox="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png":::

8. **[サブネット構成の管理]** を選択して、サーバーの新しいサブネットを作成します。

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png" alt-text="サブネットの管理のスクリーンショット" lightbox="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png":::

9. 仮想マシンの新しいサブネットを追加します。

   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png" alt-text="仮想マシンの新しいサブネットの追加のスクリーンショット" lightbox="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png":::

10. サブネットが正常に作成されたら、ページを閉じます。
   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/subnetcreate-success.png" alt-text="仮想マシンの新しいサブネットの追加に成功したスクリーンショット" lightbox="./media/quickstart-create-connect-server-vnet/subnetcreate-success.png":::

11. **[確認および作成]** を選択します。
12. **［作成］** を選択します **[新しいキー ペアの生成]** ウィンドウが開いたら、 **[Download private key and create resource]\(秘密キーをダウンロードし、リソースを作成する\)** を選択します。 キー ファイルは **myKey.pem** としてダウンロードされます。

   >[!IMPORTANT]
   > `.pem` ファイルがダウンロードされた場所を必ず把握してください。そのパスが次の手順で必要になります。

13. デプロイが完了したら、 **[リソースに移動]** を選択します。
   > :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-create-success.png" alt-text="デプロイの成功のスクリーンショット" lightbox="./media/quickstart-create-connect-server-vnet/vm-create-success.png":::

11. 新しい VM のページで、パブリック IP アドレスを選択し、それをクリップボードにコピーします。
   > :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/ip-address.png" alt-text="仮想マシンの IP アドレスをコピーする方法を示すスクリーンショット" lightbox="../../virtual-machines/linux/media/quick-create-portal/ip-address.png":::

## <a name="install-mysql-client-tools"></a>MySQL クライアント ツールをインストールする

Bash または PowerShell を使用して、VM との SSH 接続を作成します。 プロンプトで、仮想マシンへの SSH 接続を開きます。 IP アドレスをご自分の VM のものに置換し、`.pem` のパスを、キー ファイルがダウンロードされた場所に置換します。

```console
ssh -i .\Downloads\myKey1.pem azureuser@10.111.12.123
```

> [!TIP]
> 作成した SSH キーは、次回 Azure で VM を作成するときに使用できます。 次回 VM を作成するときは、 **[SSH public key source]\(SSH 公開キー ソース\)** には **[Use a key stored in Azure]\(Azure に保存されているキーを使用する\)** を選択するだけです。 お使いのコンピューターには既に秘密キーがあります。そのため、何もダウンロードする必要はありません。

サーバーに接続できるようにするには、mysql-client ツールをインストールする必要があります。

```bash
sudo apt-getupdate
sudo apt-get install mysql-client
```

データベースへの接続には SSL が使用されるため、パブリック SSL 証明書をダウンロードする必要があります。

```bash
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
```

## <a name="connect-to-the-server-from-azure-linux-virtual-machine"></a>Azure Linux 仮想マシンからサーバーに接続する
[mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) クライアント ツールがインストールされたので、ローカル環境からサーバーに接続できるようになりました。

```bash
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする
これで、リソース グループに Azure Database for MySQL フレキシブル サーバーが作成されました。 これらのリソースが今後不要である思われる場合は、リソース グループを削除してリソースを削除することも、単にこの MySQL サーバーを削除することもできます。 リソース グループを削除するには、次の手順のようにします。

1. Azure portal で、「**リソース グループ**」を検索して選択します。
1. リソース グループの一覧で、リソース グループの名前を選択します。
1. ご自分のリソース グループの **[概要]** ページで、 **[リソース グループの削除]** を選択します。
1. 確認のダイアログ ボックスでリソース グループの名前を入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [MySQL を使用した PHP (Laravel) Web アプリを作成する](tutorial-php-database-app.md)
