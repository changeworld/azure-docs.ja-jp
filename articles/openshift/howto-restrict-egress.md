---
title: Azure Red Hat OpenShift (ARO) クラスターでのエグレス トラフィックを制限する
description: Azure Red Hat OpenShift (ARO) でエグレス トラフィックを制御するために必要なポートとアドレスについて説明します
author: sakthi-vetrivel
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 04/09/2021
ms.openlocfilehash: 24c4686306aff9d84fe7bf74ddfdccff987244d9
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368514"
---
# <a name="control-egress-traffic-for-your-azure-red-hat-openshift-aro-cluster-preview"></a>Azure Red Hat OpenShift (ARO) クラスターのエグレス トラフィックを制御する (プレビュー)

この記事では、Azure Red Hat OpenShift cluster (ARO) からの送信トラフィックをセキュリティで保護するために必要な詳細情報について説明します。 これには、基本的な ARO デプロイのクラスター要件と、オプションの Red Hat およびサードパーティ製コンポーネントの要件が含まれています。 Azure Firewall でこれらの要件を構成する方法の終わりには、[例](#private-aro-cluster-setup)を示してあります。 この情報は Azure Firewall や任意の送信制限の方法またはアプライアンスに適用できることにご注意ください。

## <a name="before-you-begin"></a>始める前に

この記事では、新しいクラスターを作成することを想定しています。 基本的な ARO クラスターが必要な場合は、[ARO クイックスタート](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster)に関する記事を参照してください。

> [!IMPORTANT]
> ARO のプレビュー機能は、セルフサービスのオプトイン単位で利用できます。 プレビューは、"現状有姿のまま" および "利用可能な限度" で提供され、サービス レベル アグリーメントおよび限定保証から除外されるものとします。 ARO プレビューは、ベストエフォート ベースでカスタマー サポートによって部分的にカバーされます。

## <a name="minimum-required-fqdn--application-rules"></a>最低限必要な FQDN とアプリケーションの規則

この一覧は、 https://docs.openshift.com/container-platform/4.6/installing/install_config/configuring-firewall.html の OpenShift ドキュメントに記載されている FQDN の一覧に基づいています。

次の FQDN/アプリケーション規則が必要です。

| 送信先 FQDN | Port | 用途 |
| ----------- | ----------- | ------------- |
| **`quay.io`** | **HTTPS: 443** | インストールに必須で、クラスターによって使用されます。 これは、プラットフォームのコンテナー イメージをダウンロードするためにクラスターによって使用されます。 |
| **`registry.redhat.io`** | **HTTPS: 443** | 主要なアドオンには必須です。 これは、開発ツール、オペレーター ベースのアドオン、Red Hat 提供のコンテナー イメージなどの主要なコンポーネントをダウンロードするためにクラスターによって使用されます。
| **`mirror.openshift.com`** | **HTTPS: 443** | これは、VDI 環境またはノート PC で、ミラー化されたインストール コンテンツおよびイメージにアクセスするために必要です。 クラスターで、Quay.io からプルするイメージを知るためのプラットフォーム リリース署名のダウンロードに必要となります。 |
| **`api.openshift.com`** | **HTTPS: 443** | イメージ署名をダウンロードする前に、使用可能な更新プログラムがあるかどうかを確認するためにクラスターで必要です。 |
| **`arosvc.azurecr.io`** | **HTTPS: 443** | ARO 演算子の内部プライベート レジストリです。  サブネット上でサービス エンドポイントの Microsoft.ContainerRegistry を許可しない場合は必須です。 |
| **`management.azure.com`** | **HTTPS: 443** | これは、クラスターが Azure API にアクセスするために使用されます。 |
| **`login.microsoftonline.com`** | **HTTPS: 443** | これは、クラスターによる Azure に対する認証のために使用されます。 |
| **`gcs.prod.monitoring.core.windows.net`** | **HTTPS: 443** | これは、ARO チームが顧客のクラスターを監視できるように、Microsoft Geneva Monitoring に使用されます。 |
| **`*.blob.core.windows.net`** | **HTTPS: 443** | これは、ARO チームが顧客のクラスターを監視できるように、Microsoft Geneva Monitoring に使用されます。 |
| **`*.servicebus.windows.net`** | **HTTPS: 443** | これは、ARO チームが顧客のクラスターを監視できるように、Microsoft Geneva Monitoring に使用されます。 |
| **`*.table.core.windows.net`** | **HTTPS: 443** | これは、ARO チームが顧客のクラスターを監視できるように、Microsoft Geneva Monitoring に使用されます。 |

> [!NOTE] 
> *.blob を公開している多くの顧客にとって、*.table やその他の大きなアドレス空間は、潜在的なデータ流出の懸念を生じさせるものです。 クラスターにデプロイされているアプリケーションがこれらの宛先に到達するのを防ぎ、特定のアプリケーションのニーズに合わせて Azure Private Link を使用するために、[Openshift エグレス ファイアウォール](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/configuring-egress-firewall.html)の使用を検討することができます。

---

## <a name="complete-list-of-required-and-optional-fqdns"></a>必須およびオプションの FQDN の完全な一覧

### <a name="first-group-installing-and-downloading-packages-and-tools"></a>最初のグループ: パッケージとツールのインストールとダウンロード

- **`quay.io`** : インストールに必須で、クラスターによって使用されます。 これは、プラットフォームのコンテナー イメージをダウンロードするためにクラスターによって使用されます。
- **`registry.redhat.io`** : 主要なアドオンには必須です。 これは、開発ツール、オペレーター ベースのアドオン、ミドルウェアである Universal Base Image などの Red Hat 提供のコンテナー イメージなど、主要なコンポーネントをダウンロードするためにクラスターによって使用されます。
- **`sso.redhat.com`** : これは、VDI 環境またはノート PC で cloud.redhat.com に接続するために必要です。 これは、プル シークレットをダウンロードし、Red Hat で提供する SaaS ソリューションの一部を使用して、サブスクリプション、クラスター インベントリ、チャージバック レポートなどの監視を容易にすることができるサイトです。
- **`openshift.org`** : これは、VDI 環境またはノート PC で、接続して RH CoreOS イメージをダウンロードするために必要ですが、Azure では、マーケットプレースから選択されるため、OS イメージをダウンロードする必要はありません。

---

### <a name="second-group-telemetry"></a>2 つ目のグループ: テレメトリ

このセクションはすべてオプト アウトできますが、その方法の前に、それが何かを確認してください。 https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/about-remote-health-monitoring.html
- **`cert-api.access.redhat.com`** : VDI またはノート PC 環境で使用します。
- **`api.access.redhat.com`** : VDI またはノート PC 環境で使用します。
- **`infogw.api.openshift.com`** : VDI またはノート PC 環境で使用します。
- **`https://cloud.redhat.com/api/ingress`** : aaS Red Hat Insights と統合される Insights Operator のクラスターで使用します。
OpenShift Container Platform では、顧客はレポートの正常性と使用状況の情報をオプト アウトできます。 ただし、接続されたクラスターを使用すると、Red Hat で問題に迅速に対応し、顧客のサポートを向上し、製品がクラスターをアップグレードする方法について理解を深めることができます。 詳細はこちら (https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/opting-out-of-remote-health-reporting.html ) で確認してください。

---

### <a name="third-group-cloud-apis"></a>3 つ目のグループ: クラウド API

- **`management.azure.com`** : これは、クラスターが Azure API にアクセスするために使用されます。

---

### <a name="fourth-group-other-openshift-requirements"></a>4 つ目のグループ: OpenShift のその他の要件

- **`mirror.openshift.com`** : これは、VDI 環境またはノート PC で、ミラー化されたインストール コンテンツおよびイメージにアクセスするために必要であり、クラスターで、Quay.io からプルするイメージを知るために使用されるプラットフォーム リリース署名のダウンロードに必要となります。
- **`storage.googleapis.com/openshift-release`** : クラスターで、Quay.io からプルするイメージを知るために使用されるプラットフォーム リリース署名をダウンロードするための代替サイトです。
- **`*.apps.<cluster_name>.<base_domain>`** (または同等の ARO URL): ドメインを許可リストに載せる場合に、企業ネットワークで OpenShift でデプロイされたアプリケーションにアクセスしたり、OpenShift コンソールにアクセスしたりするために使用します。
- **`api.openshift.com`** : イメージ署名をダウンロードする前に、使用可能な更新プログラムがあるかどうかを確認するためにクラスターで必要です。
- **`registry.access.redhat.com`** : ODO CLI ツールを使用するときに、開発イメージをダウンロードするために、VDI またはノート PC 環境でレジストリ アクセスが必要です。 (この CLI ツールは、Kubernetes に慣れていない開発者向けの代替 CLI ツールです)。 https://docs.openshift.com/container-platform/4.6/cli_reference/developer_cli_odo/understanding-odo.html

---

### <a name="fifth-group-microsoft--red-hat-aro-monitoring-service"></a>5 つ目のグループ: Microsoft と Red Hat の ARO 監視サービス

- **`login.microsoftonline.com`** : これは、クラスターによる Azure に対する認証のために使用されます。
- **`gcs.prod.monitoring.core.windows.net`** : これは、ARO チームが顧客のクラスターを監視できるように、Microsoft Geneva Monitoring に使用されます。
- **`*.blob.core.windows.net`** : これは、ARO チームが顧客のクラスターを監視できるように、Microsoft Geneva Monitoring に使用されます。
- **`*.servicebus.windows.net`** : これは、ARO チームが顧客のクラスターを監視できるように、Microsoft Geneva Monitoring に使用されます。
- **`*.table.core.windows.net`** : これは、ARO チームが顧客のクラスターを監視できるように、Microsoft Geneva Monitoring に使用されます。

## <a name="aro-integrations"></a>ARO 統合

### <a name="azure-monitor-for-containers"></a>コンテナーに対する Azure Monitor

Azure Monitor for containers へのアクセスを提供するには 2 つのオプションがあります。Azure Monitor の [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) を許可するか、**または**、必要な FQDN とアプリケーションの規則へのアクセスを提供することができます。  既存の ARO クラスターに Azure Monitor を追加する方法については、こちらの[手順](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-azure-redhat4-setup) 参照してください。

#### <a name="required-network-rules"></a>必要なネットワーク規則

次の FQDN/アプリケーション規則が必要です。

| 送信先エンドポイント                                                             | Protocol | Port    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureMonitor:443`**  | TCP           | 443      | このエンドポイントは、メトリック データとログを Azure Monitor および Log Analytics に送信するために使用されます。 |

#### <a name="required-fqdn--application-rules"></a>必要な FQDN とアプリケーションの規則

Azure Monitor for Containers が有効になっている ARO クラスターでは、次の FQDN とアプリケーションの規則が必要です。

| FQDN                                    | Port      | 用途      |
|-----------------------------------------|-----------|----------|
| **`dc.services.visualstudio.com`** | **`HTTPS:443`**    | このエンドポイントは、Azure Monitor を使用するメトリックと監視テレメトリに使用されます。 |
| **`*.ods.opinsights.azure.com`**    | **`HTTPS:443`**    | このエンドポイントは、ログ分析データを取り込むために Azure Monitor によって使用されます。 |
| **`*.oms.opinsights.azure.com`** | **`HTTPS:443`** | このエンドポイントは、ログ分析サービスの認証に使用される omsagent によって使用されます。 |
| **`*.monitoring.azure.com`** | **`HTTPS:443`** | このエンドポイントは、メトリック データを Azure Monitor に送信するために使用されます。 |


## <a name="private-aro-cluster-setup"></a>プライベート ARO クラスターのセットアップ
目標は、Azure Firewall 経由でエグレス トラフィックをルーティングすることで ARO クラスターをセキュリティで保護することです
### <a name="before"></a>前:
![以前](media/concepts-networking/aro-private.jpg)
### <a name="after"></a>次の処理の後
![After](media/concepts-networking/aro-fw.jpg)

## <a name="create-a-private-aro-cluster"></a>プライベート ARO クラスターを作成する

### <a name="set-up-vars-for-your-environment"></a>環境の VARS を設定する
```bash

CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster
AROVNET=aro-vnet # The name of your vnet from your created ARO cluster
JUMPSUBNET=jump-subnet
LOCATION=eastus # The location where ARO cluster is deployed

```

### <a name="create-a-resource-group"></a>リソース グループを作成する
```bash
az group create -g "$RESOURCEGROUP" -l $LOCATION
```

### <a name="create-the-virtual-network"></a>仮想ネットワークの作成
```bash
az network vnet create \
  -g $RESOURCEGROUP \
  -n $AROVNET \
  --address-prefixes 10.0.0.0/8
```

### <a name="add-two-empty-subnets-to-your-virtual-network"></a>2 つの空のサブネットを自分の仮想ネットワークに追加する
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "$CLUSTER-master" \
    --address-prefixes 10.10.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry

  az network vnet subnet create \
    -g $RESOURCEGROUP \
    --vnet-name $AROVNET \
    -n "$CLUSTER-worker" \
    --address-prefixes 10.20.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```

### <a name="disable-network-policies-for-private-link-service-on-your-virtual-network-and-subnets-this-is-a-requirement-for-the-aro-service-to-access-and-manage-the-cluster"></a>仮想ネットワークとサブネット上の Private Link サービスのネットワーク ポリシーを無効にします。 これは、ARO サービスがクラスターにアクセスして管理するための要件です。
```bash
az network vnet subnet update \
  -g "$RESOURCEGROUP" \
  --vnet-name $AROVNET \
  -n "$CLUSTER-master" \
  --disable-private-link-service-network-policies true
```
### <a name="create-a-firewall-subnet"></a>ファイアウォール サブネットを作成する
```bash
az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "AzureFirewallSubnet" \
    --address-prefixes 10.100.1.0/26
```

## <a name="create-a-jump-host-vm"></a>ジャンプ ホスト VM を作成する
### <a name="create-a-jump-subnet"></a>ジャンプ サブネットを作成する
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n $JUMPSUBNET \
    --address-prefixes 10.30.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```
### <a name="create-a-jump-host-vm"></a>ジャンプ ホスト VM を作成する
```bash
VMUSERNAME=aroadmin

az vm create --name ubuntu-jump \
             --resource-group $RESOURCEGROUP \
             --ssh-key-values ~/.ssh/id_rsa.pub \
             --admin-username $VMUSERNAME \
             --image UbuntuLTS \
             --subnet $JUMPSUBNET \
             --public-ip-address jumphost-ip \
             --vnet-name $AROVNET 
```

## <a name="create-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift クラスターを作成する
### <a name="get-a-red-hat-pull-secret-optional"></a>Red Hat プル シークレットを取得する (省略可能)

Red Hat プル シークレットを使用すると、クラスターは他のコンテンツと共に Red Hat コンテナー レジストリにアクセスできます。 この手順は省略可能ですが、実施することをお勧めします。

1. **[Red Hat OpenShift クラスター マネージャー ポータルに移動](https://cloud.redhat.com/openshift/install/azure/aro-provisioned)し、ログインします。**

   Red Hat アカウントにログインするか、お使いのビジネス メール アドレスを使用して新しい Red Hat アカウントを作成し、使用条件に同意する必要があります。

2. **[Download pull secret]\(プル シークレットのダウンロード\) を選択します。**

保存されている `pull-secret.txt` ファイルは安全な場所に保管してください。このファイルは、クラスターを作成するたびに使用します。

`az aro create` コマンドを実行する場合は、`--pull-secret @pull-secret.txt` パラメーターを使用してプル シークレットを参照できます。 `pull-secret.txt` ファイルを格納したディレクトリから `az aro create` を実行します。 それ以外の場合は、`@pull-secret.txt` を `@<path-to-my-pull-secret-file` で置き換えます。

プル シークレットをコピーする場合や他のスクリプトで参照する場合は、プル シークレットを有効な JSON 文字列として書式設定する必要があります。

```bash
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet $AROVNET \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --apiserver-visibility Private \
  --ingress-visibility Private \
  --pull-secret @pull-secret.txt
```

## <a name="create-an-azure-firewall"></a>Azure Firewall を作成する

### <a name="create-a-public-ip-address"></a>パブリック IP アドレスを作成する
```bash
az network public-ip create -g $RESOURCEGROUP -n fw-ip --sku "Standard" --location $LOCATION
```
### <a name="update-install-azure-firewall-extension"></a>Azure Firewall 拡張機能を更新インストールする
```bash
az extension add -n azure-firewall
az extension update -n azure-firewall
```

### <a name="create-azure-firewall-and-configure-ip-config"></a>Azure Firewall を作成して IP 構成を構成する
```bash
az network firewall create -g $RESOURCEGROUP -n aro-private -l $LOCATION
az network firewall ip-config create -g $RESOURCEGROUP -f aro-private -n fw-config --public-ip-address fw-ip --vnet-name $AROVNET

```

### <a name="capture-azure-firewall-ips-for-a-later-use"></a>後で使用するために Azure Firewall IP をキャプチャする
```bash
FWPUBLIC_IP=$(az network public-ip show -g $RESOURCEGROUP -n fw-ip --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RESOURCEGROUP -n aro-private --query "ipConfigurations[0].privateIpAddress" -o tsv)

echo $FWPUBLIC_IP
echo $FWPRIVATE_IP
```

### <a name="create-a-udr-and-routing-table-for-azure-firewall"></a>Azure Firewall 用の UDR とルーティング テーブルを作成する
```bash
az network route-table create -g $RESOURCEGROUP --name aro-udr

az network route-table route create -g $RESOURCEGROUP --name aro-udr --route-table-name aro-udr --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP
```

### <a name="add-application-rules-for-azure-firewall"></a>Azure Firewall のアプリケーション ルールを追加する
OpenShift がこの[一覧](https://docs.openshift.com/container-platform/4.3/installing/install_config/configuring-firewall.html#configuring-firewall_configuring-firewall)に基づいて機能するための規則:
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'ARO' \
 --action allow \
 --priority 100 \
 -n 'required' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns 'registry.redhat.io' '*.quay.io' 'sso.redhat.com' 'management.azure.com' 'mirror.openshift.com' 'api.openshift.com' 'quay.io' '*.blob.core.windows.net' 'gcs.prod.monitoring.core.windows.net' 'registry.access.redhat.com' 'login.microsoftonline.com' '*.servicebus.windows.net' '*.table.core.windows.net' 'grafana.com'
```
Docker イメージのオプションの規則:
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'Docker' \
 --action allow \
 --priority 200 \
 -n 'docker' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns '*cloudflare.docker.com' '*registry-1.docker.io' 'apt.dockerproject.org' 'auth.docker.io'
```

### <a name="associate-aro-subnets-to-fw"></a>ARO サブネットを FW に関連付ける
```bash
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-master" --route-table aro-udr
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-worker" --route-table aro-udr
```

## <a name="test-the-configuration-from-the-jumpbox"></a>Jumpbox から構成をテストする
これらの手順は、Docker イメージのルールを追加した場合にのみ有効です。 
### <a name="configure-the-jumpbox"></a>Jumpbox を構成する
Jumpbox VM にログインし、`azure-cli`、`oc-cli`、`jq` のユーティリティをインストールします。 openshift-cli のインストールについては、Red Hat カスタマー ポータルを確認してください。
```bash
#Install Azure-cli
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
#Install jq
sudo apt install jq -y
```
### <a name="log-into-the-aro-cluster"></a>ARO クラスターにログインする
クラスターの資格情報を一覧表示する:
```bash

# Login to Azure
az login
# Set Vars in Jumpbox
CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster

#Get the cluster credentials
ARO_PASSWORD=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminPassword')
ARO_USERNAME=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminUsername')
```
API サーバー エンドポイントを取得する:
```bash
ARO_URL=$(az aro show -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.apiserverProfile.url')
```

### <a name="download-the-oc-cli-to-the-jumpbox"></a>Jumpbox に oc CLI をダウンロードする
```bash
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

`oc login` を使用してログインする:
```bash
oc login $ARO_URL -u $ARO_USERNAME -p $ARO_PASSWORD
```

### <a name="run-centos-to-test-outside-connectivity"></a>CentOS を実行して外部接続をテストする
ポッドを作成する
```bash
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: centos
spec:
  containers:
  - name: centos
    image: centos
    ports:
    - containerPort: 80
    command:
    - sleep
    - "3600"
EOF
```
ポッドが実行されたら、それを実行し、接続外部をテストします。

```bash
oc exec -it centos -- /bin/bash
curl microsoft.com
```

## <a name="access-the-web-console-of-the-private-cluster"></a>プライベート クラスターの Web コンソールにアクセスする

### <a name="set-up-ssh-forwards-commands"></a>SSH 転送コマンドを設定する

```bash
sudo ssh -i $SSH_PATH -L 443:$CONSOLE_URL:443 aroadmin@$JUMPHOST

example:
sudo ssh -i /Users/jimzim/.ssh/id_rsa -L 443:console-openshift-console.apps.d5xm5iut.eastus.aroapp.io:443 aroadmin@104.211.18.56
```

### <a name="modify-the-etc-hosts-file-on-your-local-machine"></a>ローカル マシン上の etc. hosts ファイルを変更する
```bash
##
# Host Database
#
127.0.0.1 console-openshift-console.apps.d5xm5iut.eastus.aroapp.io
127.0.0.1 oauth-openshift.apps.d5xm5iut.eastus.aroapp.io
```

### <a name="use-sshuttle-as-another-option"></a>sshuttle を別のオプションとして使用する

[SSHuttle](https://github.com/sshuttle/sshuttle)


## <a name="clean-up-resources"></a>リソースをクリーンアップする

```bash

# Clean up the ARO cluster, vnet, firewall and jumpbox

# Remove udr from master and worker subnets first or will get error when deleting ARO cluster
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-master -g $RESOURCEGROUP --route-table aro-udr --remove routeTable
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-worker -g $RESOURCEGROUP --route-table aro-udr --remove routeTable

# Remove ARO Cluster
az aro delete -n $CLUSTER -g $RESOURCEGROUP

# Remove the resource group that contains the firewall, jumpbox and vnet
az group delete -n $RESOURCEGROUP
```