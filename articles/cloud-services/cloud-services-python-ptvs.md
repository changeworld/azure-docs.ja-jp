<properties
	pageTitle="Visual Studio による Python Web ロールと Python worker ロール | Microsoft Azure"
	description="Azure クラウド サービス (Web ロール、worker ロールを含む) を Python Tools for Visual Studio で作成する方法の概要"
	services="cloud-services"
	documentationCenter="python"
	authors="thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.date="08/03/2016"
	ms.author="adegeo"/>


# Python Tools for Visual Studio による Python Web ロールと Python worker ロール

この記事では、[Python Tools for Visual Studio][] で Python Web ロールと Python worker ロールを扱う方法について概説します。Python を使用する基本的なクラウド サービスを、Visual Studio を使って作成、デプロイする方法を学習します。

## 前提条件

 - Visual Studio 2013 または 2015
 - [Python Tools for Visual Studio][] (PTVS)
 - [Azure SDK Tools for VS 2013][] または [Azure SDK Tools for VS 2015][]
 - [Python 2.7 (32 ビット)][] または [Python 3.5 (32 ビット)][]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## Python Web ロールと Python worker ロールについて

Azure にはアプリケーションを実行するためのコンピューティング モデルとして、[Azure App Service の Web Apps 機能][execution model-web sites]、[Azure Virtual Machines][execution model-vms]、[Azure Cloud Services][execution model-cloud services] の 3 種類があります。これら 3 つのモデルはすべて、Python をサポートしています。クラウド サービスには、Web ロールとワーカー ロールが含まれ、*サービスとしてのプラットフォーム (PaaS)* を提供します。クラウド サービス内で、Web ロールは、フロントエンド Web アプリケーションのホスト専用のインターネット インフォメーション サービス (IIS) Web サーバーを提供します。ワーカー ロールは、ユーザーの操作や入力とは関係なく、長期間または恒久的な非同期タスクを実行できます。

詳細については、「[What is a Cloud Service? (クラウド サービスとは)]」を参照してください。

> [AZURE.NOTE] *単純な Web サイトを構築する場合* シナリオが単純な Web サイトのフロントエンドにのみ関係している場合は、Azure App Service の軽量の Web Apps 機能を使用することを検討してください。Web サイトの規模が増大し、要件が変化したときには、容易にクラウド サービスにアップグレードできます。Azure App Service の Web Apps 機能の開発に関する記事については、<a href="/develop/python/">Python デベロッパー センター</a>を参照してください。<br />


## プロジェクトの作成

Visual Studio で、**[新しいプロジェクト]** ダイアログ ボックスの **[Python]** から **[Azure クラウド サービス]** を選択します。

![[新しいプロジェクト] ダイアログ](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Azure クラウド サービス ウィザードでは、新しい Web ロールまたは worker ロールを作成できます。

![Azure クラウド サービス ダイアログ](./media/cloud-services-python-ptvs/new-service-wizard.png)

worker ロール テンプレートには、Azure ストレージ アカウントまたは Azure Service Bus に接続するための定型コードが含まれています。

![クラウド サービス ソリューション](./media/cloud-services-python-ptvs/worker.png)

Web ロールまたは worker ロールは、既存のクラウド サービスにいつでも追加することができます。既存のプロジェクトをソリューションに追加するか、または新たに作成するかを選択できます。

![ロール コマンドの追加](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

クラウド サービスには、異なる言語で実装されたロールを含めることができます。たとえば、Django を使用して実装された Python Web ロールを、Python worker ロールや C# worker ロールと共存させることができます。ロール間のやり取りは、Service Bus キューまたはストレージ キューを使用することで簡単に行うことができます。

## クラウド サービスに Python をインストールする

>[AZURE.WARNING] (この記事が最後に更新された時点で) Visual Studio と共にインストールされるセットアップ スクリプトは動作しません。このセクションでは、回避策について説明します。

セットアップ スクリプトの一番の問題は、Python がインストールされないことです。最初に、2 つの[スタートアップ タスク](cloud-services-startup-tasks.md)を [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) ファイルに定義します。最初のタスク (**PrepPython.ps1**) は、Python ランタイムをダウンロードしてインストールします。2 番目のタスク (**PipInstaller.ps1**) は、pip を実行して、すべての依存関係をインストールします。

以下のスクリプトは、Python 3.5 を対象に書かれています。Python のバージョン 2.x を使用する場合は、2 つのスタートアップ タスクとランタイム タスクの **PYTHON2** 変数ファイルを **on** に設定してください: `<Variable name="PYTHON2" value="<mark>on</mark>" />`。


```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
	  <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
    </Environment>
	<Variable name="PYTHON2" value="off" />
  </Task>

</Startup>
```

**PYTHON2** 変数と **PYPATH** 変数は worker スタートアップ タスクに追加する必要があります。**PYPATH** 変数は、**PYTHON2** 変数を **on** に設定した場合にのみ使用します。

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### サンプルの ServiceDefinition.csdef

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



次に、**PrepPython.ps1** ファイルと **PipInstaller.ps1** ファイルをロールの **./bin** フォルダーに作成します。

#### PrepPython.ps1

このスクリプトは、Python をインストールします。**PYTHON2** 環境変数を **on** に設定すると Python 2.7 がインストールされ、それ以外の場合は Python 3.5 がインストールされます。

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
	Write-Host "Checking if python is installed...$nl"
	if ($is_python2) {
		& "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
	}
	else {
		py -V | Out-Null
	}

	if (-not $?) {

		$url = "https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64.exe"
		$outFile = "${env:TEMP}\python-3.5.2-amd64.exe"

		if ($is_python2) {
			$url = "https://www.python.org/ftp/python/2.7.12/python-2.7.12.amd64.msi"
			$outFile = "${env:TEMP}\python-2.7.12.amd64.msi"
		}
		
		Write-Host "Not found, downloading $url to $outFile$nl"
		Invoke-WebRequest $url -OutFile $outFile
		Write-Host "Installing$nl"

		if ($is_python2) {
			Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
		}
		else {
			Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
		}

		Write-Host "Done$nl"
	}
	else {
		Write-Host "Already installed"
	}
}
```

#### PipInstaller.ps1

このスクリプトは、pip を呼び出し、**requirements.txt** ファイル内のすべての依存関係をインストールします。**PYTHON2** 環境変数を **on** に設定すると Python 2.7 が使用され、それ以外の場合は Python 3.5 が使用されます。

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
	Write-Host "Checking if requirements.txt exists$nl"
	if (Test-Path ..\requirements.txt) {
		Write-Host "Found. Processing pip$nl"

		if ($is_python2) {
			& "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
		}
		else {
			py -m pip install -r ..\requirements.txt
		}

		Write-Host "Done$nl"
	}
	else {
		Write-Host "Not found$nl"
	}
}
```

#### LaunchWorker.ps1 を変更する

**bin\\LaunchWorker.ps1** は本来多くの準備作業を行うために作成されていますが、実際には機能しません。このファイルの内容を次のスクリプトに置き換えます。

このスクリプトは、Python プロジェクトの **worker.py** ファイルを呼び出します。**PYTHON2** 環境変数を **on** に設定すると Python 2.7 が使用され、それ以外の場合は Python 3.5 が使用されます。

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
	Write-Host "Running worker.py$nl"

	if ($is_python2) {
		cd..
		iex "$env:PYPATH\python.exe worker.py"
	}
	else {
		cd..
		iex "py worker.py"
	}
}
else
{
	Write-Host "Running (EMULATED) worker.py$nl"

	# Customize to your local dev environment

	if ($is_python2) {
		cd..
		iex "$env:PYPATH\python.exe worker.py"
	}
	else {
		cd..
		iex "py worker.py"
	}
}
```

#### ps.cmd

Visual Studio テンプレートによって、**ps.cmd** ファイルが **./bin** フォルダーに作成されています。このシェル スクリプトは、上記の PowerShell ラッパー スクリプトを呼び出し、呼び出された PowerShell ラッパーの名前に基づくログを提供します。このファイルが作成されていない場合は、次の内容のファイルを作成します。

```bat
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## ローカルで実行する

クラウド サービス プロジェクトをスタートアップ プロジェクトとして設定し、F5 キーを押した場合、ローカルの Azure エミュレーター内でクラウド サービスが実行されます。

PTVS (Python Tools for Visual Studio) はエミュレーターでの起動をサポートしていますが、デバッグ操作 (ブレークポイントなど) は機能しません。

Web ロールまたは worker ロールをデバッグするには、対象となるロール プロジェクトをスタートアップ プロジェクトに設定したうえで、デバッグするようにしてください。複数のスタートアップ プロジェクトを設定することもできます。ソリューションを右クリックし、**[スタートアップ プロジェクトの設定]** を選択します。

![ソリューション スタートアップ プロジェクト プロパティ](./media/cloud-services-python-ptvs/startup.png)

## Azure に発行する

クラウド サービス プロジェクトを発行するには、対象のクラウド サービス プロジェクトをソリューション内で右クリックし、**[発行]** を選択します。

![Microsoft Azure 発行サインイン](./media/cloud-services-python-ptvs/publish-sign-in.png)

ウィザードに従って操作します。必要に応じて、リモート デスクトップを有効にします。リモート デスクトップは、デバッグの必要があるときに便利です。

設定が済んだら、**[発行]** をクリックします。

出力ウィンドウにいくつかの進行状況が表示された後、[Microsoft Azure のアクティビティ ログ] ウィンドウが表示されます。

![[Microsoft Azure のアクティビティ ログ] ウィンドウ](./media/cloud-services-python-ptvs/publish-activity-log.png)

数分経過するとデプロイメントが完了し、Web ロールまたは worker ロールが Azure 上で稼働状態となります。

### ログを調査する

クラウド サービスの仮想マシンが起動され、Python がインストールされた後、ログにエラー メッセージが含まれているかどうかを確認します。これらのログは、**C:\\Resources\\Directory{role}\\LogFiles** フォルダーに格納されます。**PrepPython.err.txt** には、Python がインストールされているかどうかを検出しようとしたとき以降のエラーが少なくとも 1 つ含まれます。**PipInstaller.err.txt** には、pip のバージョンが古いことに関するエラーが含まれる場合があります。

## 次のステップ

Python Tools for Visual Studio で Web ロールまたは worker ロールを扱う方法の詳細については、次の PTVS 関連ドキュメントを参照してください。

- [クラウド サービス プロジェクト][]

Web ロールまたは worker ロールから Azure Storage や Service Bus などの Azure サービスを使用する方法の詳細については、次の記事を参照してください。

- [BLOB サービス][]
- [テーブル サービス][]
- [キュー サービス][]
- [Service Bus キュー][]
- [Service Bus トピック][]


<!--Link references-->

[What is a Cloud Service? (クラウド サービスとは)]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service-web/app-service-web-overview.md
[execution model-vms]: ../virtual-machines/virtual-machines-windows-about.md
[execution model-cloud services]: cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[BLOB サービス]: ../storage/storage-python-how-to-use-blob-storage.md
[キュー サービス]: ../storage/storage-python-how-to-use-queue-storage.md
[テーブル サービス]: ../storage/storage-python-how-to-use-table-storage.md
[Service Bus キュー]: ../service-bus/service-bus-python-how-to-use-queues.md
[Service Bus トピック]: ../service-bus/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: http://aka.ms/ptvsdocs
[クラウド サービス プロジェクト]: http://go.microsoft.com/fwlink/?LinkId=624028
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 (32 ビット)]: https://www.python.org/downloads/
[Python 3.5 (32 ビット)]: https://www.python.org/downloads/

<!---HONumber=AcomDC_0803_2016-->