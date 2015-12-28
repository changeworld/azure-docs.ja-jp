<properties
	pageTitle="継続的な配信でのリモート デバッグを有効にする | Microsoft Azure"
	description="継続的な配信を使用した Azure へのデプロイ時にリモート デバッグを有効にする方法を説明します。"
	services="cloud-services"
	documentationCenter=".net"
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="10/19/2015"
	ms.author="tarcher"/>
# 継続的な配信を使用して Azure に発行する場合にリモート デバッグを有効にする

次の手順に従って、[継続的な配信](cloud-services-dotnet-continuous-delivery.md)を使用して Azure に発行する場合に、クラウド サービスまたは仮想マシンに対して、Azure でリモート デバッグを有効にすることができます。

## クラウド サービス用にリモート デバッグを有効にする

1. 「[Azure のコマンド ライン ビルド](http://msdn.microsoft.com/library/hh535755.aspx)」の説明に従って、ビルド エージェントで Azure の初期環境を設定します。
2. リモート デバッグ ランタイム (msvsmon.exe) がパッケージに必要であるため、[Remote Tools for Visual Studio 2015](http://www.microsoft.com/ja-JP/download/details.aspx?id=48155) (Visual Studio 2013 を使用している場合は [Remote Tools for Visual Studio 2013 Update 5](https://www.microsoft.com/ja-JP/download/details.aspx?id=48156)) をインストールします。代替方法として、Visual Studio がインストールされているシステムからリモート デバッグ バイナリをコピーすることもできます。
3. 「[Azure Cloud Services の証明書の概要](cloud-services-certs-create.md)」の説明に従って、証明書を作成します。.pfx および RDP 証明書の拇印を保持し、その証明書をターゲット クラウド サービスにアップロードします。
4. MSBuild コマンド ラインで次のオプションを使用し、リモート デバッグを有効にしてビルドおよびパッケージ化します(山かっこの項目については、システムおよびプロジェクト ファイルの実際のパスに置き換えてください。)

		msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"

	`VSX64RemoteDebuggerPath` は、msvsmon.exe を含む Remote Tools for Visual Studio のフォルダーへのパスです。

5. 前の手順で生成されたパッケージと .cscfg ファイルを使用してターゲット クラウド サービスに発行します。
6. Visual Studio と Azure SDK for .NET がインストールされているマシンに証明書 (.pfx ファイル) をインポートします。

## 仮想マシン用にリモート デバッグを有効にする

1. Azure 仮想マシンを作成します。「[Windows Server を実行する仮想マシンの作成](virtual-machines-windows-tutorial.md)」または「[Visual Studio での Azure Virtual Machines の作成と管理](vs-azure-tools-virtual-machines-create-manage.md)」を参照してください。
2. [Azure クラシック ポータル ページ](http://go.microsoft.com/fwlink/p/?LinkID=269851)で、仮想マシンのダッシュボードを表示し、仮想マシンの **RDP 証明書の拇印**を確認します。この値は、拡張機能構成の `ServerThumbprint` の値に使用されます。
3. 「[Azure Cloud Services の証明書の概要](cloud-services-certs-create.md)」の説明に従って、クライアント証明書を作成します (.pfx および RDP 証明書の拇印を保持)。
4. 「[Azure PowerShell のインストールおよび構成方法](powershell-install-configure.md)」の説明に従って、Azure PowerShell (バージョン 0.7.4 以降) をインストールします。
5. 次のスクリプトを実行して RemoteDebug 拡張機能を有効にします。パスおよび個人用データを自分自身のサブスクリプション名、サービス名、サムプリントなどで置き換えます。

	>[AZURE.NOTE]このスクリプトは Visual Studio 2015 用に構成されています。Visual Studio 2013 を使用する場合は、ReferenceName と ExtensionName に対して "RemoteDebugVS2013" を使用します。

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

$referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015"
$publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
$extensionName = "RemoteDebugVS2015"
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
{
{

$vm | Update-AzureVM
</pre>

6. Visual Studio と Azure SDK for .NET がインストールされているマシンに証明書 (.pfx) をインポートします。

<!---HONumber=AcomDC_1217_2015-->