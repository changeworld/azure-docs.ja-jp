---
title: "Azure Container Service クラスターに接続する | Microsoft Docs"
description: "リモート コンピューターから Azure Container Service の Kubernetes、DC/OS、または Docker Swarm クラスターに接続します"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, DC/OS, Azure"
ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2017
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: ea59ff3f527d051e01baf12f596ff44af8a0dfc1
ms.openlocfilehash: 7fe3bc6a5eab1d1b9a8b73ab3c88f9808817369a


---
# <a name="connect-to-an-azure-container-service-cluster"></a>Azure コンテナー サービス クラスターに接続する
Azure Container Service クラスターを作成したら、クラスターに接続してワークロードのデプロイと管理を行う必要があります。 この記事では、リモート コンピューターからクラスターのマスター VM に接続する方法について説明します。 Kubernetes クラスター、DC/OS クラスター、Docker Swarm クラスターはすべて REST エンドポイントを公開します。 Kubernetes では、このエンドポイントがセキュリティ保護されてインターネット上に公開されているため、インターネットに接続されている任意のコンピューターから `kubectl` コマンド ライン ツールを実行することによってアクセスできます。 DC/OS と Docker Swarm では、REST エンドポイントに安全に接続するために Secure Shell (SSH) トンネルを作成する必要があります。 

> [!NOTE]
> Azure Container Service での Kubernetes のサポートは、現在はプレビューの段階です。
>

## <a name="prerequisites"></a>前提条件

* [Azure Container Service にデプロイされている](container-service-deployment.md) Kubernetes、DC/OS、または Swarm クラスター。
* SSH 秘密キー ファイル (デプロイ時にクラスターに追加された公開キーに対応)。 これらのコマンドは、SSH 秘密キーがコンピューター上の `$HOME/.ssh/id_rsa` に存在することを想定しています。 詳細については、[OS X と Linux 用](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)または [Windows 用](../virtual-machines/virtual-machines-linux-ssh-from-windows.md)の手順を参照してください。 SSH 接続が動作していない場合は、[SSH キーのリセット](../virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md)が必要になることがあります。

## <a name="connect-to-a-kubernetes-cluster"></a>Kubernetes クラスターへの接続

お使いのコンピューターに `kubectl` をインストールして構成するには、次の手順に従います。

> [!NOTE] 
> Linux または OS X では、`sudo` を使用してこのセクションのコマンドを実行することが必要になる場合があります。
> 

### <a name="install-kubectl"></a>kubectl のインストール
このツールをインストールする 1 つの方法は、`az acs kubernetes install cli` Azure CLI 2.0 (プレビュー) コマンドを使用することです。 このコマンドを実行するには、Azure CLI 2.0 (プレビュー) の最新版が[インストール済み](/cli/azure/install-az-cli2)であることと、Azure アカウントにログインしていること (`az login`) を確認してください。

```azurecli
# Linux or OS X
az acs kubernetes install-cli [--install-location=/some/directory/kubectl]

# Windows
az acs kubernetes install-cli [--install-location=C:\some\directory\kubectl.exe]
```

または、[リリース ページ](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md#downloads-for-v146)から直接クライアントをダウンロードすることもできます。

### <a name="download-cluster-credentials"></a>クラスターの資格情報のダウンロード
`kubectl` をインストールしたら、クラスターの資格情報をコンピューターにコピーする必要があります。 資格情報を取得する 1 つの方法は、`az acs kubernetes get-credentials` コマンドを使用することです。 リソース グループの名前とコンテナー サービス リソースの名前を渡します。


```azurecli
az acs kubernetes get-credentials --resource-group=<cluster-resource-group> --name=<cluster-name>
```

このコマンドにより、`kubectl` が求める配置場所である `$HOME/.kube/config` にクラスターの資格情報がダウンロードされます。

または、`scp` を使用して、マスター VM の `$HOME/.kube/config` からローカル コンピューターにファイルを安全にコピーすることもできます。 次に例を示します。

```console
mkdir $HOME/.kube/config
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

Windows を使用している場合は、Bash on Ubuntu on Windows、PuTTy セキュア ファイル コピー クライアント、または同様のツールを使用する必要があります。



### <a name="use-kubectl"></a>kubectl の使用

`kubectl` を構成したら、クラスター内のノードを一覧表示することで接続をテストできます。

```console
kubectl get nodes
```

他の `kubectl` コマンドを試すことができます。 たとえば、Kubernetes ダッシュボードを表示できます。 まず、Kubernetes API サーバーに対してプロキシを実行します。

```console
kubectl proxy
```

Kubernetes UI は現在、`http://localhost:8001/ui` で利用できます。

詳細については、[Kubernetes のクイック スタート](http://kubernetes.io/docs/user-guide/quick-start/)に関するページを参照してください。

## <a name="connect-to-a-dcos-or-swarm-cluster"></a>DC/OS または Swarm クラスターへの接続

Azure コンテナー サービスによってデプロイされている DC/OS クラスターと Docker Swarm クラスターは、REST エンドポイントを公開しています。 ただし、これらのエンドポイントは外部には開放されていません。 これらのエンドポイントを管理するには、Secure Shell (SSH) トンネルを作成する必要があります。 SSH トンネルが確立されると、クラスター エンドポイントに対してコマンドを実行し、独自のシステムでブラウザー経由でクラスター UI を表示できます。 以降のセクションでは、Linux、OS X、Windows の各オペレーティング システムを実行するコンピューターから SSH トンネルを作成する手順について説明します。

> [!NOTE]
> SSH セッションは、クラスター管理システムとの間で作成することができます。 ただし、これは推奨されません。 管理システムで直接作業すると、構成を意図せず変更してしまうおそれがあります。
> 

### <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>Linux または OS X での SSH トンネルの作成
Linux または OS X で SSH トンネルを作成するにはまず、負荷分散されたマスターのパブリック DNS 名を特定する必要があります。 次の手順に従います。


1. [Azure Portal](https://portal.azure.com) で、コンテナー サービス クラスターを含むリソース グループに移動します。 各リソースが表示されるようにリソース グループを展開します。 

2. マスターの仮想マシンを探して選択します。 DC/OS クラスターでは、このリソースに、**dcos-master-** で始まる名前が付いています。 

    **[仮想マシン]** ブレードには、パブリック IP アドレスに関する情報が表示されます。これには、DNS 名が含まれています。 後で使用するためにこの名前を保存します。 

    ![Public DNS name](media/pubdns.png)

3. ここでシェルを開き、次の値を指定して `ssh` コマンドを実行します。 

    **PORT** は、公開するエンドポイントのポートです。 Swarm の場合、ポート 2375 を使用します。 DC/OS の場合、ポート 80 を使用します。  
    **USERNAME** は、クラスターのデプロイ時に指定したユーザー名です。  
    **DNSPREFIX** は、クラスターのデプロイ時に指定した DNS 接頭辞です。  
    **REGION** は、リソース グループが置かれているリージョンです。  
    **PATH_TO_PRIVATE_KEY** (省略可能) は、クラスターの作成時に指定した公開キーに対応する秘密キーへのパスです。 このオプションは、`-i` フラグと共に使用します。

    ```bash
    ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
    ```
    > [!NOTE]
    > SSH 接続ポートは、標準の 22 ではなく 2200 です。 複数のマスター VM を含むクラスターでは、これが最初のマスター VM への接続ポートです。
    > 

DC/OS と Swarm の例については、以降のセクションを参照してください。    

### <a name="dcos-tunnel"></a>DC/OS トンネル
DC/OS 関連のエンドポイントへのトンネルを開くには、次のようなコマンドを実行します。

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

これで DC/OS 関連のエンドポイントに次の場所でアクセスできます。

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

同様に、各アプリケーションの REST API にはこのトンネルを経由して到達できます。

### <a name="swarm-tunnel"></a>Swarm トンネル
Swarm エンドポイントへのトンネルを開くには、次のようなコマンドを実行します。

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

これで、DOCKER_HOST 環境変数を次のように設定できます。 通常どおり、Docker コマンド ライン インターフェイス (CLI) を使用し続けることができます。

```bash
export DOCKER_HOST=:2375
```

### <a name="create-an-ssh-tunnel-on-windows"></a>Windows の SSH トンネルの作成
Windows では、さまざまな方法で SSH トンネルを作成できます。 このセクションでは、PuTTY を使用してトンネルを作成する方法について説明します。

1. Windows システムに [PuTTY をダウンロード](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)します。

2. アプリケーションを実行します。

3. ホスト名を入力します。ホスト名は、クラスター管理者のユーザー名とクラスターの第 1 マスターのパブリック DNS 名で構成されます。 **[ホスト名]** は `adminuser@PublicDNSName` のようになります。 **[ポート]** に「2200」と入力します。

    ![PuTTY configuration 1](media/putty1.png)

4. **[SSH]、[認証]** の順に選択します。 認証用の秘密キー ファイル (.ppk 形式) のパスを追加します。 [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) などのツールを使用すると、クラスターの作成に使用された SSH キーからこのファイルを生成できます。

    ![PuTTY configuration 2](media/putty2.png)

5. **[SSH]、[トンネル]** の順に選択し、次のように転送ポートを構成します。

    * **[源ポート]** には、DC/OS の場合は 80、Swarm の場合は 2375 を指定します。
    * **[ターゲット]** には、DC/OS の場合は localhost:80、Swarm の場合は localhost:2375 を指定します。

    次の例は DC/OS で構成されていますが、Docker Swarm でも同様です。

    > [!NOTE]
    > このトンネルの作成時にはポート 80 を使用しないでください。
    > 

    ![PuTTY configuration 3](media/putty3.png)

6. 完了したら、**[セッション]、[保存]** の順にクリックして接続の構成を保存します。

7. PuTTY セッションに接続するには、**[開く]** をクリックします。 接続すると、PuTTY のイベント ログでポート構成を確認できます。

    ![PuTTY event log](media/putty4.png)

DC/OS のトンネルを構成したら、関連するエンドポイントに次のアドレスでアクセスできます。

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

Docker Swarm のトンネルを構成したら、Windows の設定を開き、`DOCKER_HOST` という名前のシステム環境変数を `:2375` という値で構成します。 これで、Docker CLI を使用して Swarm クラスターにアクセスできます。

## <a name="next-steps"></a>次のステップ
クラスターにコンテナーをデプロイして管理します。

* [Azure Container Service と Kubernetes の使用](container-service-kubernetes-ui.md)
* [Azure コンテナー サービスと DC/OS の使用](container-service-mesos-marathon-rest.md)
* [Azure コンテナー サービスと Docker Swarm の使用](container-service-docker-swarm.md)




<!--HONumber=Jan17_HO3-->


