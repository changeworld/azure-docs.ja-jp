---
title: チュートリアル - VM に LAMP と WordPress をデプロイする
description: このチュートリアルでは、Azure 内の Linux 仮想マシンに LAMP スタックと WordPress をインストールする方法について説明します。
author: cynthn
ms.collection: linux
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: cynthn
ms.openlocfilehash: ec73bc6c6f7563f1513f4575b5ee3c9b4a244e7c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122699001"
---
# <a name="tutorial-install-a-lamp-stack-on-an-azure-linux-vm"></a>チュートリアル: Azure Linux VM に LAMP スタックをインストールする

**適用対象:** :heavy_check_mark: Linux VM 

この記事では、Apache Web サーバー、MySQL、PHP (LAMP スタック) を Azure　上の Ubuntu VM にデプロイする方法について説明します。 LAMP サーバーの動作を確認するために、WordPress サイトをインストールし、構成することもできます。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Ubuntu VM を作成する 
> * Web トラフィック用にポート 80 を開く
> * Apache、MySQL、および PHP をインストールする
> * インストールと構成を確認する
> * WordPress のインストール 

このセットアップは、簡単なテストまたは概念実証のためのものです。 運用環境の推奨事項を含め、LAMP スタックの詳細については、[Ubuntu ドキュメント](https://help.ubuntu.com/community/ApacheMySQLPHP)を参照してください。

このチュートリアルでは、[Azure Cloud Shell](../../cloud-shell/overview.md) で CLI を使用します。このバージョンは常に更新され最新になっています。 Cloud Shell を開くには、コード ブロックの上部にある **[使ってみる]** を選択します。

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.30 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](/cli/azure/group) コマンドを使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm) コマンドで VM を作成します。 

次の例では、*myVM* という名前の VM を作成し、既定のキーの場所にまだ SSH キーが存在しない場合は SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。 このコマンドは、管理者ユーザー名として *azureuser* も設定します。 後でこの名前を使って VM に接続します。 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM が作成されると、Azure CLI によって次の例のような情報が表示されます。 `publicIpAddress` を書き留めておきます。 このアドレスは、後の手順で VM にアクセスするために使われます。

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>Web トラフィック用にポート 80 を開く 

Azure にデプロイされている Linux VM に対しては、既定で SSH 接続のみが許可されます。 この VM は Web サーバーとして使用することになるため、インターネットからのポート 80 を開放する必要があります。 [az vm open-port](/cli/azure/vm) コマンドを使用して、目的のポートを開きます。  
 
```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

VM へのポートを開く方法の詳細については、[ポートの開放](nsg-quickstart.md)に関するページを参照してください。

## <a name="ssh-into-your-vm"></a>VM への SSH 接続

VM のパブリック IP アドレスが未確認である場合は、[az network public-ip list](/cli/azure/network/public-ip) コマンドを実行します。 後の複数の手順で、この IP アドレスが必要になります。

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

次のコマンドを使用して、仮想マシンとの SSH セッションを作成します。 ご使用の仮想マシンの正しいパブリック IP アドレスに置き換えてください。 この例の IP アドレスは *40.68.254.142* です。 *azureuser* は、VM を作成するときに設定された管理者ユーザー名です。

```bash
ssh azureuser@40.68.254.142
```


## <a name="install-apache-mysql-and-php"></a>Apache、MySQL、および PHP をインストールする

次のコマンドを実行して、Ubuntu パッケージ ソースを更新し Apache、MySQL、および PHP をインストールします。 コマンドの最後にあるキャレット (^) に注意してください。これは、`lamp-server^` パッケージ名の一部です。 


```bash
sudo apt update && sudo apt install lamp-server^
```

パッケージやその他の依存関係をインストールすることが求められます。 このプロセスにより、MySQL で PHP を使用するための必要最小限の PHP 拡張機能がインストールされます。  

## <a name="verify-apache"></a>Apache を確認する

次のコマンドで Apache のバージョンを確認します。
```bash
apache2 -v
```

Apache がインストールされ、VM に対しポート 80 が開かれると、Web サーバーにインターネットからアクセスできるようになります。 Apache2 Ubuntu の既定ページを表示するには、Web ブラウザーを開いて、VM のパブリック IP アドレスを入力します。 VM に対する SSH で使用したパブリック IP アドレスを使用します。

![Apache の既定のページ][3]


## <a name="verify-and-secure-mysql"></a>MySQL を確認してセキュリティで保護する

次のコマンドを使用して MySQL のバージョンを確認します (大文字の `V` パラメーターに注意)。

```bash
mysql -V
```

MySQL のインストールのセキュリティ保護を強化 (ルートのパスワードを設定するなど) するには、`mysql_secure_installation` スクリプトを実行します。 

```bash
sudo mysql_secure_installation
```

必要に応じてValidate Password プラグインを設定することもできます (推奨)。 次に、MySQL のルート ユーザーに使用するパスワードを設定し、その他、環境のセキュリティ設定を構成します。 質問にはすべて "Y" (はい) で答えることをお勧めします。

MySQL の機能 (MySQL データベースの作成、ユーザーの追加、構成設定の変更) を試したい場合は、MySQL にログインします。 このチュートリアルを実行するには、このステップは必要ありません。

```bash
sudo mysql -u root -p
```

完了したら「`\q`」と入力して、mysql プロンプトを終了します。

## <a name="verify-php"></a>PHP を確認する

次のコマンドで PHP のバージョンを確認します。

```bash
php -v
```

さらに詳しくテストする場合は、簡単な PHP 情報ページを作成して、ブラウザーで表示します。 次のコマンドは、PHP 情報ページを作成します。

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

ここで作成した PHP 情報ページを確認できます。 ブラウザーを開き、`http://yourPublicIPAddress/info.php` に移動します。 IP アドレスは、実際の VM のパブリック IP アドレスに置き換えてください。 以下の画像のようなページが表示されます。

![PHP 情報ページ][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure に LAMP サーバーを展開しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Ubuntu VM を作成する
> * Web トラフィック用にポート 80 を開く
> * Apache、MySQL、および PHP をインストールする
> * インストールと構成を確認する
> * LAMP サーバーに WordPress をインストールする

TLS/SSL 証明書を使用して Web サーバーをセキュリティ保護する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [TLS による Web サーバーのセキュリティ保護](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
