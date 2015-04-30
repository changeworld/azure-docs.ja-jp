<properties
  pageTitle="Azure でホストされているカスタム AD ドメイン コントローラーへの Azure クラウド サービス ロールの接続"
  description="PowerShell と AD ドメイン拡張機能を使ってカスタム AD ドメインに Web ロールまたは worker ロールを接続する方法について説明します"
  services="cloud-services"
  documentationCenter=""
  authors="VMak"
  manager="MadhanA"
  editor=""/>

  <tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="3/5/2015"
    ms.author="vmaker"/>

# Azure でホストされているカスタム AD ドメイン コントローラーへの Azure クラウド サービス ロールの接続

## Azure でホストされているカスタム ドメイン コントローラーに Azure Web ロールまたは worker ロールを接続するための手順ガイド

最初に、Azure に仮想ネットワーク (VNET) を設定します。次に、(Azure 仮想マシンでホストされている) Active Directory ドメイン コントローラーを VNET に追加します。次に、事前に作成した VNET に既存のクラウド サービス ロールを追加した後、それらをドメイン コントローラーに接続します。

作業を開始するにあたり、いくつか注意することがあります。
1.	このチュートリアルでは PowerShell を使用します。Azure PowerShell がインストールされ、使用する準備が整っていることを確認してください。Azure PowerShell の設定方法については、[Azure PowerShell をインストールして構成する方法](install-configure-powershell.md)に関するページを参照してください。
2.	AD ドメイン コントローラーと Web ロールまたは worker ロールのインスタンスは VNET 内にある必要があります。

このステップ バイ ステップ ガイドに従って作業しているときに問題が発生した場合はコメントを残してください。私たちはコメントを必ず読んでいるので、だれかが対応します。

## 仮想ネットワークの作成

Azure に仮想ネットワークを作成するには、Azure ポータルか PowerShell を使います。このチュートリアルでは、PowerShell を使用します。Azure ポータルを使用して仮想ネットワークを作成するには、[仮想ネットワークの作成](create-virtual-network.md)に関するページを参照してください。

    #Create Virtual Network

    $vnetStr =
    @"<?xml version="1.0" encoding="utf-8"?>
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
            <AddressSpace>
              <AddressPrefix>[your-address-prefix]</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="[your-subnet-name]">
                <AddressPrefix>[your-subnet-range]</AddressPrefix>
              </Subnet>
            </Subnets>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
    "@;

    $vnetConfigPath = "<path-to-vnet-config>"
    Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath;

## 仮想マシンを作成する

仮想ネットワークの設定が完了したら、AD ドメイン コントローラーを作成する必要があります。このチュートリアルでは、Azure 仮想マシンに AD ドメイン コントローラーを設定します。

そのためには、PowerShell で次のコマンドを使用して仮想マシンを作成します。

    #Initialize variables

    $vnetname = '<your-vnet-name>'
    $subnetname = '<your-subnet-name>'
    $vmsvc1 = '<your-hosted-service>'
    $vm1 = '<your-vm-name>'
    $username = '<your-username>'
    $password = '<your-password>'
    $ affgrp = '<your- affgrp>'

    #Create a VM and add it to the Virtual Network

    New-AzureQuickVM -Windows -ServiceName $vmsvc1 -name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname


## 仮想マシンをドメイン コントローラーに昇格させる
仮想マシンを AD ドメイン コントローラーとして構成するには、VM にログインして構成する必要があります。

VM にログインするには、PowerShell で次のコマンドを使用して RDP ファイルを取得します。

    #Get RDP file

    Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>

VM にログインした後、[カスタム AD ドメイン コントローラーの設定方法](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx)に関するステップ バイ ステップ ガイドに従って仮想マシンを AD ドメイン コントローラーとして設定します。

## クラウド サービス デプロイメントを仮想ネットワークに追加する

次に、先ほど作成した VNET にクラウド サービス デプロイメントを追加する必要があります。そのためには、Visual Studio または任意のエディターを使用して cscfg に該当するセクションを追加して、クラウド サービスの cscfg を変更します。

    <ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
        <Role name="[role-name]">
        <Instances count="[number-of-instances]" />
      </Role>
      <NetworkConfiguration>

        <!--optional-->
        <Dns>
          <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
        </Dns>
        <!--optional-->

        <!--VNET settings-->
        <VirtualNetworkSite name="[virtual-network-name]" />
        <AddressAssignments>
          <InstanceAddress roleName="[role-name]">
            <Subnets>
              <Subnet name="[subnet-name]" />
            </Subnets>
          </InstanceAddress>
        </AddressAssignments>
        <!--VNET settings-->

      </NetworkConfiguration>
    </ServiceConfiguration>

次に、クラウド サービス プロジェクトをビルドして Azure にデプロイします。クラウド サービス パッケージを Azure にデプロイする方法については、[クラウド サービスを作成して展開する方法]に関するページを参照してください。](cloud-services-how-to-create-deploy.md#deploy)

## AD ドメイン拡張機能を使用して、Web ロールまたは worker ロールをカスタム ドメインに接続する

Azure にクラウド サービス プロジェクトをデプロイした後は、AD ドメイン拡張機能を使用して、ロール インスタンスをカスタム AD ドメインに接続します。AD ドメイン拡張機能を既存のクラウド サービス デプロイメントに追加し、カスタム ドメインに参加させるには、PowerShell で次のコマンドを実行します。

    #Initialize domain variables

    $domain = '<your-domain-name>';
    $dmuser = '$domain\<your-username>';
    $dmpswd = '<your-domain-password>';
    $dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force;
    $dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd);

    #Add AD Domain Extension to the cloud service roles

    Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35;

これで終わりです。

クラウド サービスがカスタム ドメイン コントローラーに参加します。AD ドメイン拡張機能を構成するための他のオプションを調べるには、次のように PowerShell のヘルプを使用します。

    help Set-AzureServiceADDomainExtension;
    help New-AzureServiceADDomainExtensionConfig;

私たちは、仮想マシンをドメイン コントローラーに昇格させる拡張機能の有用性に関して皆さんからのご意見を募集しています。このような拡張機能が便利だと思われる方は、コメントを残してください。

この記事がお役に立てばさいわいです。

<!--HONumber=52-->