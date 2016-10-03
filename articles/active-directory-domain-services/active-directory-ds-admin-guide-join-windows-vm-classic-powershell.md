<properties
	pageTitle="Azure Active Directory Domain Services: 管理ガイド | Microsoft Azure"
	description="Azure PowerShell とクラシック デプロイ モデルを使用して Windows 仮想マシンを管理対象ドメインに参加させます。"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="maheshu"/>


# PowerShell を使用して Windows Server 仮想マシンを管理対象ドメインに参加させる

> [AZURE.SELECTOR]
- [Azure クラシック ポータル - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

> [AZURE.IMPORTANT] Azure には、リソースの作成と操作に関して 2 種類のデプロイ モデルがあります。[リソース マネージャー デプロイ モデルとクラシック デプロイ モデル](../resource-manager-deployment-model.md)です。この記事では、クラシック デプロイ モデルの使用方法について説明します。現在、Azure AD ドメイン サービスでは、Resource Manager モデルをサポートしていません。

以下の手順では、構成ブロック手法を使用して、Azure PowerShell コマンド セットをカスタマイズする方法を示します。このコマンド セットでは、Windows ベースの Azure 仮想マシンを作成および事前構成します。この手順に従うと、Windows ベースの Azure 仮想マシンを作成して、Azure AD ドメイン サービスで管理されているドメインに参加させることができます。

これらの手順では、空白に記入する方式に従って Azure PowerShell コマンド セットを作成します。この方法は、PowerShell を初めて使う場合や、構成を正しく行うためにどの値を指定するとよいかを知りたい場合に役立ちます。PowerShell に慣れているユーザーは、コマンドの変数を独自の値で置き換えることができます ("$" で始まる行)。

まだ完了していない場合は、[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)に関するページの手順に従って、Azure PowerShell をご使用のローカル コンピューターにインストールします。次に、Windows PowerShell コマンド プロンプトを開きます。

## 手順 1: アカウントを追加する

1. PowerShell プロンプトで、「**Add-AzureAccount**」と入力し、**Enter** キーを押します。
2. お使いの Azure サブスクリプションに関連付けられている電子メール アドレスを入力し、**[続行]** をクリックします。
3. アカウントのパスワードを入力します。
4. **[サインイン]** をクリックします。

## 手順 2. サブスクリプションとストレージ アカウントを設定する

Windows PowerShell コマンド プロンプトで次のコマンドを実行して、Azure サブスクリプションとストレージ アカウントを設定します。引用符内のすべての文字 (< および > を含む) を、正しい名前に置き換えます。

	$subscr="<subscription name>"
	$staccount="<storage account name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

**Get-AzureSubscription** コマンドで出力される SubscriptionName プロパティで正しいサブスクリプション名を取得できます。**Select-AzureSubscription** コマンドの実行後、**Get-AzureStorageAccount** コマンドを実行して出力される Label プロパティで正しいストレージ アカウント名を取得できます。


## 手順 3: 詳細な手順: 仮想マシンをプロビジョニングし、管理対象ドメインに参加させる
この仮想マシンを作成するための対応する Azure PowerShell コマンド セットは次のとおりです。読みやすくするために各ブロックの間に空白行を入れてあります。

プロビジョニングする Windows 仮想マシンに関する情報を指定します。

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

D、DS、または G シリーズの各仮想マシンの InstanceSize 値の詳細については、「[Azure の仮想マシンおよびクラウド サービスのサイズ](https://msdn.microsoft.com/library/azure/dn197896.aspx)」を参照してください。

管理対象ドメインに関する情報を指定します。

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

クラウド サービスの名前を指定します。

    $svcname="Contoso100-test"

VM が参加する仮想ネットワークの名前を指定します。AAD-DS 管理対象ドメインをこの仮想ネットワークで使用できることを確認します。

    $vnetname="MyPreviewVnet"

VM のプロビジョニングに使用する VM イメージを選択します。

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

VM を構成します。VM 名、インスタンス サイズ、および使用するイメージを設定します。

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

VM のローカル管理者の資格情報を取得します。強力なローカル管理者パスワードを選択します。強度を確認するには、[パスワード チェッカーの強力なパスワードの使用](https://www.microsoft.com/security/pc-security/password-checker.aspx)に関するページを参照してください。

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

VM を管理対象ドメインに参加させるために必要となる、"AAD DC Administrators" グループに属するユーザー アカウントの資格情報を取得します。ドメイン名を指定しないでください。たとえば、この例では、ユーザー名として "bob" を指定しています。

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

VM を構成します。ドメイン参加要件と必要な資格情報を指定します。

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

VM のサブネットを設定します。

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

省略可能: ドメインの IP アドレスを設定します。Azure AD ドメイン サービス管理対象ドメインの IP アドレスを仮想ネットワークの DNS サーバーに設定した場合は、この手順は必要ありません。

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

ドメイン参加済みの Windows VM をプロビジョニングします。

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## Windows VM をプロビジョニングし、AAD ドメイン サービスの管理対象ドメインに自動的に参加させるためのスクリプト
下記の PowerShell コマンド セットを実行すると、次の条件で基幹業務サーバー用の仮想マシンを作成できます。

- Windows Server 2012 R2 Datacenter イメージを使用する。
- 仮想マシンを極小にする。
- 名前を contoso-test にする。
- contoso100 という管理対象ドメインに自動的に参加する。
- 管理対象ドメインと同じ仮想ネットワークに追加される。

以下は、Windows 仮想マシンを作成し、Azure AD ドメイン サービスで管理されているドメインに自動的に参加するためのサンプル スクリプトの全文です。

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## 関連コンテンツ
- [Azure AD ドメイン サービス - 作業開始ガイド](./active-directory-ds-getting-started.md)

- [Azure AD ドメイン サービスで管理されているドメインの管理](./active-directory-ds-admin-guide-administer-domain.md)

<!---HONumber=AcomDC_0921_2016-->