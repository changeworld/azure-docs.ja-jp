<properties 
	pageTitle="継続的な配信でのリモート デバッグを有効にする" 
	description="継続的な配信を使用した Azure へのデプロイ時にリモート デバッグを有効にする方法を説明します。" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="kempb"/>
# 継続的な配信を使用して Azure に発行する場合にリモート デバッグを有効にする

次の手順に従って、[継続的な配信](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-continuous-delivery/)を使用して Azure に発行する場合にリモート デバッグを有効にすることができます。

このトピックの内容:

[クラウド サービス用にリモート デバッグを有効にする](#cloudservice)

[仮想マシン用にリモート デバッグを有効にする](#virtualmachine)

<h2> <a name="cloudservice"></a>クラウド サービス用にリモート デバッグを有効にする</h2>

1. 「[Azure のコマンド ライン ビルド](http://msdn.microsoft.com/library/hh535755.aspx)」の説明に従って、ビルド エージェントで Azure の初期環境を設定します。
2. リモート デバッグ ランタイム (msvsmon.exe) がパッケージに必要であるため、[Remote Tools for Visual Studio 2013](http://www.microsoft.com/ja-jp/download/details.aspx?id=40781) (Visual Studio 2012 を使用している場合は [Remote Tools for Visual Studio 2012 Update 4](http://www.microsoft.com/ja-jp/download/details.aspx?id=38184)) をインストールします。代替方法として、Visual Studio がインストールされているシステムからリモート デバッグ バイナリをコピーすることもできます。
3. 「[Azure のサービス証明書を作成する](http://msdn.microsoft.com/library/azure/gg432987.aspx)」の説明に従って、証明書を作成します。.pfx および RDP 証明書のサムプリントを保持し、その証明書をターゲット クラウド サービスにアップロードします。
4. MSBuild コマンド ラインで次のオプションを使用し、リモート デバッグを有効にしてビルドおよびパッケージ化します(システム ファイルとプロジェクト ファイルのパスを更新します)。

	/TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="C:\Remote Debugger\x64\\";RemoteDebuggerConnectorCertificateThumbprint="56D7D1B25B472268E332F7FC0C87286458BFB6B2";RemoteDebuggerConnectorVersion="2.4" "C:\Users\yourusername\Documents\visual studio 2013\Projects\WindowsAzure1\WindowsAzure1.sln"

5. 前の手順で生成されたパッケージと .cscfg ファイルを使用してターゲット クラウド サービスに発行します。
6. Visual Studio と SDK 2.4 がインストールされているマシンに証明書 (.pfx ファイル) をインポートします。

<h2> <a name="virtualmachine"></a>仮想マシン用にリモート デバッグを有効にする</h2>

1. Azure 仮想マシンを作成します。「[Windows Server を実行する仮想マシンの作成](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/)」または「[Creating Azure Virtual Machines in Visual Studio (Visual Studio で Azure 仮想マシンを作成する)](http://msdn.microsoft.com/library/azure/dn569263.aspx)」を参照してください。
2. [Azure ポータル ページ](http://go.microsoft.com/fwlink/p/?LinkID=269851)で、仮想マシン ダッシュボードを表示し、仮想マシンの "RDP 証明書のサムプリント" を確認します。これは、拡張機能構成の ServerThumbprint の値に使用されます。
3. 「[Azure のサービス証明書を作成する](http://msdn.microsoft.com/library/azure/gg432987.aspx)」の説明に従って、クライアント証明書を作成します (.pfx および RDP 証明書サムプリントを保持)。
4. Microsoft ダウンロード センターから [Azure Powershell](http://go.microsoft.com/?linkid=9811175&clcid=0x409) (Version 0.7.4 以降) をインストールします。
5. 次のスクリプトを実行して RemoteDebug 拡張機能を有効にします。個人用データを自分自身のサブスクリプション名、サービス名、サムプリントなどで置き換えます。(注:このスクリプトは Visual Studio 2013 用に構成されています。Visual Studio 2012 を使用する場合は、ReferenceName と ExtensionName に対して "RemoteDebugVS2013" を使用します。)

	<pre>
    Add-AzureAccount
    
    Select-AzureSubscription "My Microsoft Subscription"
    
    $vm = Get-AzureVM -ServiceName "mytestvm1" -Name "mytestvm1"
    
    $endpoints = @(
    ,@{Name="RDConnVS2013"; PublicPort=30400; PrivatePort=30398}
    ,@{Name="RDFwdrVS2013"; PublicPort=31400; PrivatePort=31398}
    )
    
    foreach($endpoint in $endpoints)
    {
    Add-AzureEndpoint -VM $vm -Name $endpoint.Name -Protocol tcp -PublicPort $endpoint.PublicPort -LocalPort $endpoint.PrivatePort
    }
    
    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2013"
    $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
    $extensionName = "RemoteDebugVS2013"
    $version = "1.*"
    $publicConfiguration = "<PublicConfig><Connector.Enabled>true</Connector.Enabled><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"
    
    $vm | Set-AzureVMExtension `
    -ReferenceName $referenceName `
    -Publisher $publisher `
    -ExtensionName $extensionName `
    -Version $version `
    -PublicConfiguration $publicConfiguration
    
    foreach($extension in $vm.VM.ResourceExtensionReferences)
    {   
    if(($extension.ReferenceName -eq $referenceName) `
    -and ($extension.Publisher -eq $publisher) `
    -and ($extension.Name -eq $extensionName) `
    -and ($extension.Version -eq $version))
    {
    $extension.ResourceExtensionParameterValues[0].Key = 'config.txt'
    break
    }
    }
    
    $vm | Update-AzureVM 
	</pre>
    
6. Visual Studio と Azure SDK for .NET 2.4 がインストールされているマシンに証明書 (.pfx) をインポートします。
<!--HONumber=45--> 
