仮想マシン スケール セットを利用すれば、複数の VM を セットとして管理できます。大まかに言うと、スケール セットには次のような長所と短所があります。

長所:

1. 高可用性:各スケール セットでは、VM が 5 つの障害ドメイン (FD) と 5 つの更新ドメイン (UD) を持つ可用性セットに割り当てられ、可用性が保証されます (FD と UD の詳細については、「[Linux 仮想マシンの可用性管理](../articles/virtual-machines/virtual-machines-linux-manage-availability.md)」をご覧ください)。
2. Azure Load Balancer および Application Gateway との容易な統合。
3. Azure 自動スケールとの容易な統合。
4. VM の簡略化されたデプロイ、管理、クリーンアップ。
5. カスタム イメージだけでなく、Windows や Linux の一般的なディストリビューションもサポート。

短所:

1. スケール セット内の VM インスタンスにデータ ディスクをアタッチすることはできません。代わりに、Blob Storage、Azure Files、Azure テーブルなどのストレージ ソリューションを使用する必要があります。

## Azure CLI を使用した簡易作成

[Azure サブスクリプションの無料試用版](https://azure.microsoft.com/pricing/free-trial/)と [Azure CLI](../articles/xplat-cli-install.md) は、[Azure アカウントに接続](../articles/xplat-cli-connect.md)できます (まだ接続していない場合)。接続が完了したら、次のコマンドを実行して VM スケール セットを簡易作成できます。

```bash
# make sure we are in resource manager mode 
azure config mode arm

# quick-create a VM scale set
#
# generic syntax:
# azure vmss quick-create -n VMSS-NAME -g RESOURCE-GROUP-NAME -l LOCATION -u USERNAME -p PASSWORD -C INSTANCE-COUNT -Q IMAGE-URN
#
# example:
azure vmss quick-create -n negatvmss -g negatvmssrg -l westus -u negat -p P4$$w0rd -C 5 -Q Canonical:UbuntuServer:14.04.4-LTS:latest
```

場所またはイメージの URN をカスタマイズする場合は、`azure location list` コマンドと `azure vm image {list-publishers|list-offers|list-skus|list|show}` コマンドをご確認ください。

このコマンドから制御が返された時点で、スケール セットは既に作成されています。このスケール セットは、ロード バランサー上のポート 50,000+i を VM i のポート 22 にマッピングする NAT 規則が構成されたロード バランサーを備えています。したがって、ロード バランサーの FQDN がわかっていれば、VM に SSH で接続することができます。

```bash
# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g nsgvmsrg | grep nsgvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is associated with it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LB-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g nsgvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g nsgvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can ssh on port 50,000+i to ssh into VM i (where i is 0-indexed)
#
# example to ssh into VM "0":
ssh -p 50000 $FQDN
```

## 次のステップ

全般的な情報については、[VM スケール セットのメイン ランディング ページ](https://azure.microsoft.com/services/virtual-machine-scale-sets/)をご覧ください。

ドキュメントについては、[VM スケール セットのメイン ドキュメント ページ](https://azure.microsoft.com/documentation/services/virtual-machines-scale-sets/)をご覧ください。

VM スケール セットを使用した Azure Resource Manager テンプレートの例については、[Azure クイックスタート テンプレートの GitHub リポジトリ](https://github.com/Azure/azure-quickstart-templates)で "vmss" を検索してください。

<!---HONumber=AcomDC_0406_2016-->