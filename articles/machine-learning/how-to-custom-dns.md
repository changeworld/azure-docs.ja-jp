---
title: カスタム DNS サーバーを使用する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning ワークスペースおよびプライベート エンドポイントを使用するようにカスタム DNS サーバーを構成する方法。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 06/04/2021
ms.topic: how-to
ms.custom: contperf-fy21q3, devx-track-azurepowershell
ms.openlocfilehash: 616354174f5eb4bdae8e4b76379106e309c0dd14
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111969092"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>カスタム DNS サーバーでワークスペースを使用する方法

プライベート エンドポイントで Azure Machine Learning ワークスペースを使用する場合、[DNS 名前解決を処理する方法はいくつかあります](../private-link/private-endpoint-dns.md)。 既定では、ワークスペースとプライベート エンドポイントの名前解決は、Azure によって自動的に処理されます。 代わりに __独自のカスタム DNS サーバーを使用する場合__ は、ワークスペース用に DNS エントリを手動で作成したり、条件付きフォワーダーを使用したりする必要があります。

> [!IMPORTANT]
> この記事では、DNS ソリューションで DNS レコードを手動登録する必要がある場合に、これらのエントリの完全修飾ドメイン名 (FQDN) と IP アドレスを検索する方法について説明します。 また、この記事では、FQDN が正しい IP アドレスに自動的に解決されるようにカスタム DNS ソリューションを構成する方法についての、アーキテクチャに関する推奨事項も提供します。 これらの項目に対する DNS レコードの構成に関する情報については、この記事では説明しません。 レコードを追加する方法については、DNS ソフトウェアのドキュメントを参照してください。

## <a name="prerequisites"></a>[前提条件]

- [独自の DNS サーバー](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)を使用する Azure Virtual Network。

- プライベート エンドポイントを持つ Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。

- [トレーニングおよび推論中にネットワーク分離](./how-to-network-security-overview.md)を使用することに精通していること。

- [Azure プライベート エンドポイントの DNS ゾーン構成](../private-link/private-endpoint-dns.md)に関する知識。

- [Azure プライベート DNS](../dns/private-dns-privatednszone.md) に関する知識

- 必要に応じて、[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="automated-dns-server-integration"></a>DNS サーバーの自動統合

### <a name="introduction"></a>はじめに

DNS サーバーと Azure Machine Learning の自動統合を使用するには、2 つの一般的なアーキテクチャがあります。

* [Azure 仮想ネットワークでホストされているカスタム DNS サーバー](#dns-vnet)。
* ExpressRoute 経由で Azure Machine Learning に接続された、[オンプレミスでホストされているカスタム DNS サーバー](#dns-on-premises)。

お使いのアーキテクチャはこれらの例と異なるかもしれませんが、これらを参照ポイントとして使用できます。 どちらのアーキテクチャの例でも、正しく構成されていない可能性があるコンポーネントを特定するのに役立つトラブルシューティングの手順が提供されます。

### <a name="workspace-dns-resolution-path"></a>ワークスペースの DNS 解決パス

Private Link 経由での特定の Azure Machine Learning ワークスペースへのアクセスは、次に示す完全修飾ドメイン (ワークスペース FQDN と呼ばれます) と通信することによって行われます。

**Azure パブリック リージョン**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.azureml.ms```
- ```<compute instance name>.<region the workspace was created in>.instances.azureml.ms```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.azure.net```

**Azure China 21Vianet リージョン**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.ml.azure.cn```
- ```<compute instance name>.<region the workspace was created in>.instances.ml.azure.cn```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.chinacloudapi.cn```

**Azure US Government リージョン**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.ml.azure.us```
- ```<compute instance name>.<region the workspace was created in>.instances.ml.azure.us```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.usgovcloudapi.net```

完全修飾ドメインは、ワークスペース プライベート リンク FQDN と呼ばれる次の正規名 (CNAME) に解決されます。

**Azure パブリック リージョン**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.azureml.ms```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.azure.net```

**Azure China リージョン**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.ml.azure.cn```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.chinacloudapi.cn```

**Azure US Government リージョン**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.ml.azure.us```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.usgovcloudapi.net```

FQDN は、そのリージョンの Azure Machine Learning ワークスペースの IP アドレスに解決されます。 ただし、ワークスペース プライベート リンク FQDN の解決は、上で説明したように作成されたプライベート DNS ゾーンにリンクされている仮想ネットワークの Azure DNS 仮想サーバーの IP アドレスを使用して解決するとオーバーライドされます。

## <a name="manual-dns-server-integration"></a>DNS サーバーの手動統合

ここでは、DNS サーバーで A レコードを作成する完全修飾ドメインと、A レコードの値を設定する IP アドレスについて説明します。

### <a name="retrieve-private-endpoint-fqdns"></a>プライベート エンドポイント FQDN を取得する

#### <a name="azure-public-region"></a>Azure パブリック リージョン

次のリストには、ワークスペースが Azure パブリック クラウドにある場合に使用される完全修飾ドメイン名 (FQDN) が含まれています。

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.azure.net`

    > [!NOTE]
    > この FQDN のワークスペース名は切り詰められている可能性があります。 切り詰めは `ml-<workspace-name, truncated>-<region>-<workspace-guid>` を 63 文字以下に維持するために行われます。
* `<instance-name>.<region>.instances.azureml.ms`

    > [!NOTE]
    > * コンピューティング インスタンスには、仮想ネットワーク内からのみアクセスできます。
    > * この FQDN の IP アドレスは、コンピューティング インスタンスの IP では **ありません**。 代わりに、ワークスペースのプライベート エンドポイントのプライベート IP アドレス (`*.api.azureml.ms` エントリの IP) を使用します。

#### <a name="azure-china-region"></a>Azure China リージョン

次の FQDN は、Azure China リージョン向けです。

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.cn`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.cn`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.chinacloudapi.cn`

    > [!NOTE]
    > この FQDN のワークスペース名は切り詰められている可能性があります。 切り詰めは `ml-<workspace-name, truncated>-<region>-<workspace-guid>` を 63 文字以下に維持するために行われます。
    
* `<instance-name>.<region>.instances.ml.azure.cn`

   * この FQDN の IP アドレスは、コンピューティング インスタンスの IP では **ありません**。 代わりに、ワークスペースのプライベート エンドポイントのプライベート IP アドレス (`*.api.azureml.ms` エントリの IP) を使用します。

#### <a name="azure-us-government"></a>Azure 米国政府機関

次の FQDN は、Azure US Government リージョン向けです。

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.us`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.us`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.usgovcloudapi.net`

    > [!NOTE]
    > この FQDN のワークスペース名は切り詰められている可能性があります。 切り詰めは `ml-<workspace-name, truncated>-<region>-<workspace-guid>` を 63 文字以下に維持するために行われます。
* `<instance-name>.<region>.instances.ml.azure.us`
    > * この FQDN の IP アドレスは、コンピューティング インスタンスの IP では **ありません**。 代わりに、ワークスペースのプライベート エンドポイントのプライベート IP アドレス (`*.api.azureml.ms` エントリの IP) を使用します。

### <a name="find-the-ip-addresses"></a>IP アドレスを検索する

VNet 内の FQDN の内部 IP アドレスを検索するには、次のいずれかの方法を使用します。

> [!NOTE]
> 完全修飾ドメイン名と IP アドレスは、構成によって異なります。 たとえば、ドメイン名の GUID 値は、ワークスペースに固有です。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com) で、Azure Machine Learning __ワークスペース__ を選択します。
1. __[設定]__ セクションから、 __[プライベート エンドポイント接続]__ を選択します。
1. 表示される __[プライベート エンドポイント]__ 列のリンクを選択します。
1. ワークスペースのプライベート エンドポイントの完全修飾ドメイン名 (FQDN) と IP アドレスのリストがページの下部にあります。

    :::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="ポータルの FQDN のリスト":::

    > [!TIP]
    > ページ下部に DNS 設定が表示されない場合は、ページ左側にある __DNS 構成__ リンクを使用して FQDN を表示します。

---

どの方法でも返される情報は同じで、リソースの FQDN とプライベート IP アドレスのリストです。 次の例は、Azure パブリック クラウドからのものです。

| FQDN | IP アドレス |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.cert.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

次の表は、Azure China リージョンの IP の例を示しています。

| FQDN | IP アドレス |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.cn` | `10.1.0.5` |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.cert.api.ml.azure.cn` | `10.1.0.5` |
| `ml-mype-pltest-chinaeast2-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.chinacloudapi.cn` | `10.1.0.6` |

次の表は、Azure US Government リージョンの IP の例を示しています。

| FQDN | IP アドレス |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.us` | `10.1.0.5` |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.cert.api.ml.azure.us` | `10.1.0.5` |
| `ml-mype-plt-usgovvirginia-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.usgovcloudapi.net` | `10.1.0.6` |

<a id='dns-vnet'></a>

### <a name="create-a-records-in-custom-dns-server"></a>カスタム DNS サーバーで A レコードを作成する

FQDN と対応する IP アドレスの一覧が収集された後、構成された DNS サーバーでの A レコードの作成に進みます。 A レコードを作成する方法については、お使いの DNS サーバーのドキュメントを参照してください。 FQDN 全体に対して一意のゾーンを作成し、ゾーンのルートに A レコードを作成することが推奨されていることにご注意ください。

## <a name="example-custom-dns-server-hosted-in-vnet"></a>例: VNet でホストされているカスタム DNS サーバー

このアーキテクチャでは、一般的なハブ アンド スポーク仮想ネットワーク トポロジを使用します。 1 つの仮想ネットワークには DNS サーバーが含まれ、もう 1 つには Azure Machine Learning ワークスペースおよびそれと関連付けられているリソースへのプライベート エンドポイントが含まれます。 両方の仮想ネットワークの間には有効なルートが存在する必要があります。 たとえば、一連のピアリングされた仮想ネットワークなどです。

:::image type="content" source="./media/how-to-custom-dns/custom-dns-topology.svg" alt-text="Azure トポロジでホストされているカスタム DNS の図":::

次の手順では、このトポロジがどのように動作するのかについて説明します。

1. **プライベート DNS ゾーンを作成し、DNS サーバーの仮想ネットワークにリンクします**:

    カスタム DNS ソリューションを Azure Machine Learning ワークスペースで動作させるための最初のステップでは、次のドメインをルートとする 2 つのプライベート DNS ゾーンを作成します。

    **Azure パブリック リージョン**:
    - ```privatelink.api.azureml.ms```
    - ```privatelink.notebooks.azure.net```
    
    **Azure China リージョン**:
    - ```privatelink.api.ml.azure.cn```
    - ```privatelink.notebooks.chinacloudapi.cn```
    
    **Azure US Government リージョン**:
    - ```privatelink.api.ml.azure.us```
    - ```privatelink.notebooks.usgovcloudapi.net```

    プライベート DNS ゾーンを作成した後、DNS サーバーの仮想ネットワークにそれをリンクする必要があります。 DNS サーバーが含まれている仮想ネットワーク。

    プライベート DNS ゾーンによって、ゾーンのルートのスコープ内のすべての名前の名前解決がオーバーライドされます。 このオーバーライドは、プライベート DNS ゾーンがリンクされているすべての仮想ネットワークに適用されます。 たとえば、`privatelink.api.azureml.ms` をルートとするプライベート DNS ゾーンが仮想ネットワーク foo にリンクされている場合、`bar.workspace.westus2.privatelink.api.azureml.ms` の解決を試みる仮想ネットワーク foo 内のすべてのリソースは、`privatelink.api.azureml.ms` ゾーンにリストされているレコードを受け取ります。

    ただし、プライベート DNS ゾーンにリストされているレコードは、既定の Azure DNS 仮想サーバーの IP アドレスを使用してドメインを解決するデバイスにのみ返されます。 そのため、カスタム DNS サーバーは、ネットワーク トポロジ全体に広がるデバイスのドメインを解決します。 しかし、カスタム DNS サーバーは、Azure DNS 仮想サーバーの IP アドレスに対して Azure Machine Learning 関連のドメインを解決する必要があります。

2. **DNS サーバー仮想ネットワークにリンクされているプライベート DNS ゾーンをターゲットとするプライベート DNS 統合を使用してプライベート エンドポイントを作成します**:

    次のステップでは、Azure Machine Learning ワークスペースへのプライベート エンドポイントを作成します。 プライベート エンドポイントにより、プライベート DNS 統合が有効になることが保証されます。 プライベート エンドポイントは、ステップ 1 で作成した両方のプライベート DNS ゾーンを対象とします。 これにより、ワークスペースとのすべての通信は、Azure Machine Learning 仮想ネットワークのプライベート エンドポイントを介して行われます。

3. **Azure DNS に転送するための条件付きフォワーダーを DNS サーバーに作成します**: 

    次に、Azure DNS 仮想サーバーへの条件付きフォワーダーを作成します。 条件付きフォワーダーを使用すると、DNS サーバーは常に、ワークスペースに関連付けられている FQDN の Azure DNS 仮想サーバーの IP アドレスを照会します。 これは、DNS サーバーによってプライベート DNS ゾーンから対応するレコードが返されることを意味します。

    条件付きで転送されるゾーンの一覧を次に示します。 Azure DNS 仮想サーバーの IP アドレスは 168.63.129.16 です。

    **Azure パブリック リージョン**:
    - ``` privatelink.api.azureml.ms```
    - ``` privatelink.notebooks.azure.net```
    
    **Azure China リージョン**:
    - ```privatelink.api.ml.azure.cn```
    - ```privatelink.notebooks.chinacloudapi.cn```
    
    **Azure US Government リージョン**:
    - ```privatelink.api.ml.azure.us```
    - ```privatelink.notebooks.usgovcloudapi.net```

    > [!IMPORTANT]
    > カスタム DNS サーバーとして使用できる多くの DNS ソリューションがあるため、ここには DNS サーバーの構成手順は含まれていません。 条件付き転送を適切に構成する方法については、お使いの DNS ソリューションのドキュメントを参照してください。

4. **ワークスペースのドメインを解決します**:

    この時点で、すべてのセットアップが完了します。 これで、名前解決に DNS サーバーを使用し、Azure Machine Learning プライベート エンドポイントへのルートを持つクライアントは、ワークスペースへのアクセスを続行できます。
    クライアントはまず、次の FQDN のアドレスを DNS サーバーに照会することで開始します。

    **Azure パブリック リージョン**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.azure.net```
    
    **Azure China リージョン**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.chinacloudapi.cn```
    
    **Azure US Government リージョン**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.usgovcloudapi.net```

5. **パブリック DNS が CNAME で応答します**:

    DNS サーバーは、ステップ 4 の FQDN をパブリック DNS から解決します。 パブリック DNS は、ステップ 1 の情報セクションに記載されているドメインの 1 つで応答します。

6. **DNS サーバーは、Azure DNS からのワークスペース ドメイン CNAME レコードを再帰的に解決します**:

    DNS サーバーは、ステップ 5 で受信した CNAME を再帰的に解決します。 ステップ 3 で設定された条件付きフォワーダーがあるので、DNS サーバーにより要求が解決のため Azure DNS 仮想サーバーの IP アドレスに送信されます。

7. **Azure DNS により、プライベート DNS ゾーンからレコードが返されます**:

    プライベート DNS ゾーンに格納されている対応するレコードが、DNS サーバーに返されます。これは、Azure DNS 仮想サーバーによりプライベート エンドポイントの IP アドレスが返されることを意味します。

8. **カスタム DNS サーバーにより、ワークスペース ドメイン名がプライベート エンドポイント アドレスに解決されます**:

    最終的に、カスタム DNS サーバーにより、ステップ 4 のプライベート エンドポイントの IP アドレスがクライアントに返されます。 これにより、Azure Machine Learning ワークスペースへのすべてのトラフィックがプライベート エンドポイントを経由することが保証されます。

#### <a name="troubleshooting"></a>トラブルシューティング

仮想マシンからワークスペースにアクセスできない場合、または仮想ネットワークのコンピューティング リソースでジョブが失敗する場合は、次の手順を使用して原因を特定します。

1. **プライベート エンドポイントでワークスペースの FQDN を見つけます**:

    次のいずれかのリンクを使用して、Azure portal に移動します。
    - [Azure パブリック リージョン](https://ms.portal.azure.com/?feature.privateendpointmanagedns=false)
    - [Azure 中国リージョン](https://portal.azure.cn/?feature.privateendpointmanagedns=false)
    - [Azure US Government リージョン](https://portal.azure.us/?feature.privateendpointmanagedns=false)

    Azure Machine Learning ワークスペースへのプライベート エンドポイントに移動します。 ワークスペースの FQDN が [概要] タブに表示されます。

1. **Virtual Network トポロジ内のコンピューティング リソースにアクセスします**:

    Azure Virtual Network トポロジ内のコンピューティング リソースへのアクセスに進みます。 これには、ハブ仮想ネットワークとピアリングされている仮想ネットワーク内の仮想マシンにアクセスすることが必要な場合があります。 

1. **ワークスペースの FQDN を解決します**:

    コマンド プロンプト、シェル、または PowerShell を開きます。 次に、各ワークスペース FQDN について、次のコマンドを実行します。

    ```nslookup <workspace FQDN>```
        
    各 nslookup の結果により、Azure Machine Learning ワークスペースへのプライベート エンドポイントの 2 つのプライベート IP アドレスの 1 つが返されます。 そうでない場合は、カスタム DNS ソリューションで何かが正しく構成されていません。

    考えられる原因:
    - トラブルシューティング コマンドを実行しているコンピューティング リソースで、DNS 解決に DNS サーバーが使用されていません
    - プライベート エンドポイントの作成時に選択したプライベート DNS ゾーンが、DNS サーバーの VNet にリンクされていません
    - Azure DNS 仮想サーバーの IP への条件付きフォワーダーが、正しく構成されていません

<a id='dns-on-premises'></a>

## <a name="example-custom-dns-server-hosted-on-premises"></a>例: オンプレミスでホストされているカスタム DNS サーバー

このアーキテクチャでは、一般的なハブ アンド スポーク仮想ネットワーク トポロジを使用します。 オンプレミス ネットワークからハブ仮想ネットワークに接続するには、ExpressRoute が使用されます。 カスタム DNS サーバーはオンプレミスでホストされています。 別の仮想ネットワークには、Azure Machine Learning ワークスペースおよびそれと関連付けられているリソースへのプライベート エンドポイントが含まれます。 このトポロジでは、Azure DNS 仮想サーバーの IP アドレスに要求を送信できる DNS サーバーをホストする別の仮想ネットワークが必要です。

:::image type="content" source="./media/how-to-custom-dns/custom-dns-express-route.svg" alt-text="オンプレミスでホストされているカスタム DNS のトポロジの図":::

次の手順では、このトポロジがどのように動作するのかについて説明します。

1. **プライベート DNS ゾーンを作成し、DNS サーバーの仮想ネットワークにリンクします**:

    カスタム DNS ソリューションを Azure Machine Learning ワークスペースで動作させるための最初のステップでは、次のドメインをルートとする 2 つのプライベート DNS ゾーンを作成します。
    
    **Azure パブリック リージョン**:
    - ``` privatelink.api.azureml.ms```
    - ``` privatelink.notebooks.azure.net```
    
    **Azure China リージョン**:
    - ```privatelink.api.ml.azure.cn```
    - ```privatelink.notebooks.chinacloudapi.cn```
    
    **Azure US Government リージョン**:
    - ```privatelink.api.ml.azure.us```
    - ```privatelink.notebooks.usgovcloudapi.net```

    プライベート DNS ゾーンの作成後、DNS サーバー VNet (DNS サーバーが含まれている仮想ネットワーク) にそれをリンクする必要があります。 

    > [!NOTE]
    > 仮想ネットワーク内の DNS サーバーは、オンプレミスの DNS サーバーとは別のものです。

    プライベート DNS ゾーンによって、ゾーンのルートのスコープ内のすべての名前の名前解決がオーバーライドされます。 このオーバーライドは、プライベート DNS ゾーンがリンクされているすべての仮想ネットワークに適用されます。 たとえば、`privatelink.api.azureml.ms` をルートとするプライベート DNS ゾーンが仮想ネットワーク foo にリンクされている場合、`bar.workspace.westus2.privatelink.api.azureml.ms` の解決を試みる仮想ネットワーク foo 内のすべてのリソースは、privatelink.api.azureml.ms ゾーンにリストされているレコードを受け取ります。

    ただし、プライベート DNS ゾーンにリストされているレコードは、既定の Azure DNS 仮想サーバーの IP アドレスを使用してドメインを解決するデバイスにのみ返されます。 Azure DNS 仮想サーバーの IP アドレスは、仮想ネットワークのコンテキスト内でのみ有効です。 オンプレミスの DNS サーバーを使用している場合、Azure DNS 仮想サーバーの IP アドレスにクエリを実行してレコードを取得することはできません。

    この動作を回避するには、仮想ネットワークに中間 DNS サーバーを作成します。 この DNS サーバーは、Azure DNS 仮想サーバーの IP アドレスにクエリを実行して、仮想ネットワークにリンクされたプライベート DNS ゾーンのレコードを取得できます。

    オンプレミスの DNS サーバーは、ネットワーク トポロジ全体に広がるデバイスのドメインを解決しますが、Azure Machine Learning 関連のドメインは DNS サーバーで解決します。 DNS サーバーは、それらのドメインを Azure DNS 仮想サーバーの IP アドレスから解決します。

2. **DNS サーバー仮想ネットワークにリンクされているプライベート DNS ゾーンをターゲットとするプライベート DNS 統合を使用してプライベート エンドポイントを作成します**:

    次のステップでは、Azure Machine Learning ワークスペースへのプライベート エンドポイントを作成します。 プライベート エンドポイントにより、プライベート DNS 統合が有効になることが保証されます。 プライベート エンドポイントは、ステップ 1 で作成した両方のプライベート DNS ゾーンを対象とします。 これにより、ワークスペースとのすべての通信は、Azure Machine Learning 仮想ネットワークのプライベート エンドポイントを介して行われます。

3. **Azure DNS に転送するための条件付きフォワーダーを DNS サーバーに作成します**:

    次に、Azure DNS 仮想サーバーへの条件付きフォワーダーを作成します。 条件付きフォワーダーを使用すると、DNS サーバーは常に、ワークスペースに関連付けられている FQDN の Azure DNS 仮想サーバーの IP アドレスを照会します。 これは、DNS サーバーによってプライベート DNS ゾーンから対応するレコードが返されることを意味します。

    条件付きで転送されるゾーンの一覧を次に示します。 Azure DNS 仮想サーバーの IP アドレスは 168.63.129.16 です。

    **Azure パブリック リージョン**:
    - ``` privatelink.api.azureml.ms```
    - ``` privatelink.notebooks.azure.net```
    
    **Azure China リージョン**:
    - ```privatelink.api.ml.azure.cn```
    - ```privatelink.notebooks.chinacloudapi.cn```
    
    **Azure US Government リージョン**:
    - ```privatelink.api.ml.azure.us```
    - ```privatelink.notebooks.usgovcloudapi.net```

    > [!IMPORTANT]
    > カスタム DNS サーバーとして使用できる多くの DNS ソリューションがあるため、ここには DNS サーバーの構成手順は含まれていません。 条件付き転送を適切に構成する方法については、お使いの DNS ソリューションのドキュメントを参照してください。

4. **オンプレミスの DNS サーバーに条件付きフォワーダーを作成して、DNS サーバーに転送します**:

    次に、DNS サーバー仮想ネットワーク内の DNS サーバーへの条件付きフォワーダーを作成します。 このフォワーダーは、ステップ 1 の一覧で示されているゾーンに対するものです。 これはステップ 3 と似ていますが、Azure DNS 仮想サーバーの IP アドレスに転送するのではなく、オンプレミスの DNS サーバーは DNS サーバーの IP アドレスを対象とします。 オンプレミスの DNS サーバーは Azure にないため、プライベート DNS ゾーンのレコードを直接解決することはできません。 この場合、DNS サーバーによって、オンプレミスの DNS サーバーからの要求は Azure DNS 仮想サーバーの IP にプロキシされます。 これにより、オンプレミスの DNS サーバーは、DNS サーバーの仮想ネットワークにリンクされているプライベート DNS ゾーン内のレコードを取得できます。 

    条件付きで転送されるゾーンの一覧を次に示します。 転送先の IP アドレスは、DNS サーバーの IP アドレスです。

    **Azure パブリック リージョン**:
    - ``` privatelink.api.azureml.ms```
    - ``` privatelink.notebooks.azure.net```
    
    **Azure China リージョン**:
    - ```privatelink.api.ml.azure.cn```
    - ```privatelink.notebooks.chinacloudapi.cn```
    
    **Azure US Government リージョン**:
    - ```privatelink.api.ml.azure.us```
    - ```privatelink.notebooks.usgovcloudapi.net```

    > [!IMPORTANT]
    > カスタム DNS サーバーとして使用できる多くの DNS ソリューションがあるため、ここには DNS サーバーの構成手順は含まれていません。 条件付き転送を適切に構成する方法については、お使いの DNS ソリューションのドキュメントを参照してください。

5. **ワークスペースのドメインを解決します**:

    この時点で、すべてのセットアップが完了します。 名前解決にオンプレミスの DNS サーバーを使用し、Azure Machine Learning プライベート エンドポイントへのルートを持つクライアントは、ワークスペースへのアクセスを続行できます。

    クライアントはまず、次の FQDN のアドレスをオンプレミスの DNS サーバーに照会することで開始します。

    **Azure パブリック リージョン**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.azure.net```
    
    **Azure China リージョン**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.chinacloudapi.cn```
    
    **Azure US Government リージョン**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.usgovcloudapi.net```

6. **パブリック DNS が CNAME で応答します**:

    DNS サーバーは、ステップ 4 の FQDN をパブリック DNS から解決します。 パブリック DNS は、ステップ 1 の情報セクションに記載されているドメインの 1 つで応答します。

7. **オンプレミスの DNS サーバーは、DNS サーバーからのワークスペース ドメイン CNAME レコードを再帰的に解決します**:

    オンプレミスの DNS サーバーは、ステップ 6 で受信した CNAME を再帰的に解決します。 ステップ 4 で条件付きフォワーダーのセットアップを行ったので、オンプレミスの DNS サーバーは解決のために DNS サーバーに要求を送信します。

8. **DNS サーバーは、Azure DNS からのワークスペース ドメイン CNAME レコードを再帰的に解決します**:

    DNS サーバーは、ステップ 5 で受信した CNAME を再帰的に解決します。 ステップ 3 で設定された条件付きフォワーダーがあるので、DNS サーバーにより要求が解決のため Azure DNS 仮想サーバーの IP アドレスに送信されます。

9. **Azure DNS により、プライベート DNS ゾーンからレコードが返されます**:

    プライベート DNS ゾーンに格納されている対応するレコードが、DNS サーバーに返されます。これは、Azure DNS 仮想サーバーがプライベート エンドポイントの IP アドレスを返すことを意味します。

10. **オンプレミスの DNS サーバーが、ワークスペース ドメイン名をプライベート エンドポイント アドレスに解決します**:

    ステップ 7 のオンプレミス DNS サーバーから DNS サーバーへのクエリでは、最終的に、Azure Machine Learning ワークスペースへのプライベート エンドポイントに関連付けられている IP アドレスが返されます。 これらの IP アドレスを返された元のクライアントは、ステップ 1 で構成したプライベート エンドポイントを介して Azure Machine Learning ワークスペースと通信するようになります。


#### <a name="troubleshooting"></a>トラブルシューティング

上記の手順を行った後に、仮想マシンからワークスペースにアクセスできない場合、または Azure Machine Learning ワークスペースへのプライベート エンドポイントが含まれている仮想ネットワークのコンピューティング リソースでジョブが失敗する場合は、次の手順に従って原因を特定してください。

1. **プライベート エンドポイントでワークスペースの FQDN を見つけます**:

    次のいずれかのリンクを使用して、Azure portal に移動します。
    - [Azure パブリック リージョン](https://ms.portal.azure.com/?feature.privateendpointmanagedns=false)
    - [Azure 中国リージョン](https://portal.azure.cn/?feature.privateendpointmanagedns=false)
    - [Azure US Government リージョン](https://portal.azure.us/?feature.privateendpointmanagedns=false)

    Azure Machine Learning ワークスペースへのプライベート エンドポイントに移動します。 ワークスペースの FQDN が [概要] タブに表示されます。

1. **Virtual Network トポロジ内のコンピューティング リソースにアクセスします**:

    Azure Virtual Network トポロジ内のコンピューティング リソースへのアクセスに進みます。 これには、ハブ仮想ネットワークとピアリングされている仮想ネットワーク内の仮想マシンにアクセスすることが必要な場合があります。 

1. **ワークスペースの FQDN を解決します**:

    コマンド プロンプト、シェル、または PowerShell を開きます。 次に、各ワークスペース FQDN について、次のコマンドを実行します。

    ```nslookup <workspace FQDN>```
        
    各 nslookup の結果で、Azure Machine Learning ワークスペースへのプライベート エンドポイントの 2 つのプライベート IP アドレスの 1 つが生成される必要があります。 そうでない場合は、カスタム DNS ソリューションで何かが正しく構成されていません。

    考えられる原因:
    - トラブルシューティング コマンドを実行しているコンピューティング リソースで、DNS 解決に DNS サーバーが使用されていません
    - プライベート エンドポイントの作成時に選択したプライベート DNS ゾーンが、DNS サーバーの VNet にリンクされていません
    - DNS サーバーから Azure DNS 仮想サーバーの IP への条件付きフォワーダーが、正しく構成されていません
    - オンプレミスの DNS サーバーから DNS サーバーへの条件付きフォワーダーが、正しく構成されていません

## <a name="next-steps"></a>次の手順

仮想ネットワークでの Azure Machine Learning の使用の詳細については、[仮想ネットワークの概要](how-to-network-security-overview.md)に関するページを参照してください。

プライベート エンドポイントの DNS 構成への統合については、「[Azure プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-dns.md)」をご覧ください。