---
title: Azure Arc 対応 SQL Managed Instance に接続する
description: Azure Arc 対応 SQL Managed Instance に接続する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 62442749ccff4a588daef57c7e3ecbc374ff5fde
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061734"
---
# <a name="connect-to-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance に接続する

この記事では、Azure Arc 対応 SQL Managed Instance に接続する方法について説明します。 


## <a name="view-azure-arc-enabled-sql-managed-instances"></a>Azure Arc 対応 SQL Managed Instance を表示する

Azure Arc 対応 SQL Managed Instance と外部エンドポイントを表示するには、次のコマンドを使用します。

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s -o table
```

出力は次のようになります。

```console
Name       PrimaryEndpoint      Replicas    State
---------  -------------------  ----------  -------
sqldemo    10.240.0.107,1433    1/1         Ready
```

AKS、kubeadm、OpenShift などを使用している場合は、ここから外部 IP とポート番号をコピーし、Azure Data Studio や SQL Server Management Studio などの SQL Server/Azure SQL インスタンスに接続するためのお気に入りのツールを使用して接続できます。  ただし、クイック スタート VM を使用している場合は、Azure の外部からその VM に接続する方法に関する特別な情報について、以下を参照してください。 

> [!NOTE]
> 特にパブリック クラウドに作成されている場合は、企業ポリシーによって IP やポートへのアクセスがブロックされる場合があります。

## <a name="connect"></a>接続する 

Azure Data Studio または SQL Server Management Studio、または SQLCMD を使用して接続する

Azure Data Studio を開き、上記の外部エンドポイント IP アドレスとポート番号を使用してインスタンスに接続します。 Azure VM を使用している場合は、_パブリック_ IP アドレスが必要になります。これを識別する方法については、「[Azure 仮想マシンのデプロイに関する特別な注意事項](#special-note-about-azure-virtual-machine-deployments)」を参照してください。

次に例を示します。

- サーバー: 52.229.9.30,30913
- ユーザー名: sa
- パスワード: プロビジョニング時に指定した SQL パスワード

> [!NOTE]
> Azure Data Studio を使用して、[SQL Managed Instance ダッシュボードを表示](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)することができます。

> [!NOTE]
> Kubernetes マニフェストを使用して作成されたマネージド インスタンスに接続するには、ユーザー名とパスワードを base64 のエンコード形式で sqlcmd に提供する必要があります。

SQLCMD、Linux、または Windows を使用して接続するには、次のようなコマンドを使用できます。 SQL パスワードの入力を求められたら、入力します。

```bash
sqlcmd -S 52.229.9.30,30913 -U sa
```

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Azure 仮想マシンのデプロイに関する特別な注意事項

Azure 仮想マシンを使用している場合は、エンドポイントの IP アドレスにパブリック IP アドレスが表示されません。 外部の IP アドレスを検索するには、次のコマンドを使用します。

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

その後、パブリック IP アドレスとポートを組み合わせて接続できます。

また、ネットワーク セキュリティ ゲートウェイ (NSG) を使用して SQL インスタンスのポートを公開する必要がある場合もあります。 NSG 経由のトラフィックを許可するには、次のコマンドを使用して実行できる規則を追加する必要があります。

規則を設定するには、次のコマンドを使用して確認できる NSG の名前を把握しておく必要があります。

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

NSG の名前を取得したら、次のコマンドを使用してファイアウォール規則を追加できます。 この例の値は、ポート 30913 に対する NSG 規則を作成し、**任意の** ソース IP アドレスからの接続を許可します。  これは、セキュリティ上、ベスト プラクティスとはいえません。  クライアントの IP アドレスや、チームまたは組織の IP アドレスが含まれる IP アドレス範囲に固有の -source-address-prefixes 値を指定すると、適切にロックダウンできます。

次の `--destination-port-ranges` パラメーターの値を、上記の `az sql mi-arc list` コマンドから受け取ったポート番号に置き換えます。

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30913 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="next-steps"></a>次のステップ

- [SQL Managed Instance ダッシュボードを表示する](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)
- [Azure portal で SQL Managed Instance を表示する](view-arc-data-services-inventory-in-azure-portal.md)
