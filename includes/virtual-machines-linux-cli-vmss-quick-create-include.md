まだない場合は、[Azure サブスクリプションの無料試用版](https://azure.microsoft.com/pricing/free-trial/)と、[Azure アカウント](../articles/xplat-cli-connect.md)に接続された [Azure CLI](../articles/xplat-cli-install.md) を入手できます。 次のコマンドを実行して、Azure CLI が Resource Manager モードになっていることを確認します。

```azurecli
azure config mode arm
```

次に、`azure vmss quick-create` コマンドを使ってスケール セットを作成します。 次の例では、`myResourceGroup` という名前のリソース グループに、5 個の VM を含む `myVMSS` という名前のスケール セットを作成します。

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q Canonical:UbuntuServer:14.04.4-LTS:latest
```

場所またはイメージの URN をカスタマイズする場合は、`azure location list` コマンドと `azure vm image {list-publishers|list-offers|list-skus|list|show}` コマンドを確認してください。

このコマンドから制御が返された時点で、スケール セットは既に作成されています。 このスケール セットは、ロード バランサー上のポート 50,000+i を VM i のポート 22 にマッピングする NAT 規則が構成されたロード バランサーを備えています。 したがって、ロード バランサーの FQDN がわかっていれば、VM に SSH で接続することができます。

```bash
# (if you decide to run this as a script, please invoke using bash)

# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g negatvmssrg | grep negatvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is 
# associated to it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LOAD-BALANCER-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g negatvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g negatvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can use ssh on port 50,000+i to connect to VM i (where i is 0-indexed)
#
# example to connct via ssh into VM "0":
ssh -p 50000 negat@$FQDN
```

<!--HONumber=Nov16_HO2-->


