<properties 
   pageTitle="PowerShell を使用してアクセス制御リスト (ACL) のエンドポイントを管理する方法"
   description="PowerShell を使用して ACL を管理する方法を説明します。"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="telmos" />

# PowerShell を使用してアクセス制御リスト (ACL) のエンドポイントを管理する方法

Azure PowerShell を使用して、または管理ポータルで、エンドポイントのネットワーク アクセス制御リスト (ACL) を作成し、管理できます。このトピックでは PowerShell を使用して完了できる ACL の一般的なタスクの手順を説明します。Azure PowerShell コマンドレットの一覧については、[Azure の管理コマンドレット](http://go.microsoft.com/fwlink/?LinkId=317721)に関するページを参照してください。ACL の詳細については、「[ネットワーク アクセス制御リスト (ACL) とは](virtual-networks-acl.md)」を参照してください。管理ポータルを使用して ACL を管理する場合は、[仮想マシンにエンドポイントを設定する方法](../virtual-machines/virtual-machines-set-up-endpoints.md)に関するページを参照してください。

## Azure PowerShell を使用してネットワーク ACL を管理する

Azure PowerShell コマンドレットを使用して、ネットワーク アクセス制御リスト (ACL) を作成、削除、および構成 (設定) できます。PowerShell を使用して ACL を構成する方法の例をいくつか記載しました。

ACL PowerShell コマンドレットの完全な一覧を取得するには、次のいずれかを使用できます。

	Get-Help *AzureACL*
	Get-Command -Noun AzureACLConfig

### リモート サブネットからのアクセスを許可するルールを含んだネットワーク ACL を作成する

次のルールを含んだ新しい ACL を作成する方法を示しています。この ACL は仮想マシンのエンドポイントに適用されます。次の例の ACL では、リモート サブネットからのアクセスが許可されます。リモート サブネットに対する許可ルールを含んだ新しいネットワーク ACL を作成するには、Azure PowerShell ISE を開きます。以下のスクリプトをコピーし、貼り付けて、独自の値でスクリプトを構成してから、スクリプトを実行します。

1. 新しいネットワーク ACL オブジェクトを作成します。

		$acl1 = New-AzureAclConfig

1. リモート サブネットからのアクセスを許可するルールを設定します。次の例では、ルール *100* (これはルール 200 以降よりも優先されます) を設定し、リモート サブネット *10.0.0.0/8* から仮想マシンのエンドポイントへのアクセスを許可します。値を独自の構成要件に置き換えます。このルールの名前を表示名にして、"SharePoint ACL config" という名前を置き換える必要があります。

		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
			–Action permit –RemoteSubnet "10.0.0.0/8" `
			–Description "SharePoint ACL config"

1. ルールを追加する場合は、コマンドレットを繰り返して、独自の構成の要件に値を置き換えます。必ず Order のルール番号を変更して、ルールを適用する順序を反映します。小さいルール番号が、大きい番号よりも優先されます。

		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
			–Action permit –RemoteSubnet "157.0.0.0/8" `
			–Description "web frontend ACL config"

1. 次に、新しいエンドポイントを作成 (Add) するか、既存のエンドポイントの ACL を設定 (Set) できます。この例では "web" と呼ばれる新しい仮想マシンのエンドポイントを追加し、ACL の設定で仮想マシンのエンドポイントを更新します。

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
		| Update-AzureVM

1. 次に、コマンドレットを結合し、スクリプトを実行します。この例では、結合されたコマンドレットは次のようになります。

		$acl1 = New-AzureAclConfig
		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
			–Action permit –RemoteSubnet "10.0.0.0/8" `
			–Description "Sharepoint ACL config"
		Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
			–Action permit –RemoteSubnet "157.0.0.0/8" `
			–Description "web frontend ACL config"
		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		|Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
		|Update-AzureVM

### リモート サブネットからのアクセスを許可するネットワーク ACL ルールを削除します。

次の例では、ネットワーク ACL ルールを削除する方法を示しています。リモート サブネットの許可ルールを含んだネットワーク ACL ルールを削除するには、Azure PowerShell ISE を開きます。以下のスクリプトをコピーし、貼り付けて、独自の値でスクリプトを構成してから、スクリプトを実行します。

1. 最初の手順では、仮想マシンのエンドポイントのネットワーク ACL オブジェクトを取得します。次に、ACL ルールを削除します。この場合は、ルール ID を指定して削除します。ここでは、ACL からルール ID 0 のみ削除されます。仮想マシンのエンドポイントから ACL オブジェクトは削除されません。 

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Get-AzureAclConfig –EndpointName "web" `
		| Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1

1. 次に、仮想マシンのエンドポイントにネットワーク ACL オブジェクトを適用し、仮想マシンを更新する必要があります。

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Set-AzureEndpoint –ACL $acl1 –Name "web" `
		| Update-AzureVM

### 仮想マシンのエンドポイントからネットワーク ACL を削除します。

特定のシナリオでは、仮想マシンのエンドポイントからネットワーク ACL オブジェクトを削除する場合があります。そのためには、Azure PowerShell ISE を開きます。以下のスクリプトをコピーし、貼り付けて、独自の値でスクリプトを構成してから、スクリプトを実行します。

		Get-AzureVM –ServiceName $serviceName –Name $vmName `
		| Remove-AzureAclConfig –EndpointName "web" `
		| Update-AzureVM

## 次のステップ

[ネットワーク アクセス制御リスト (ACL) とは](virtual-networks-acl.md)

<!---HONumber=AcomDC_0323_2016-->