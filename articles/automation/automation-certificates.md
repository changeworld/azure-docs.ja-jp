<properties 
   pageTitle="Azure Automation の証明書資産 | Microsoft Azure"
   description="証明書を Azure Automation に安全に保存し、Runbook でアクセスして Azure およびサードパーティのリソースで認証できます。この記事では、証明書の詳細およびテキスト作成とグラフィカル作成の両方で証明書を使用する方法について説明します。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/18/2015"
   ms.author="bwren" />

# Azure Automation の証明書資産

証明書を Azure Automation に安全に保存し、**Get-AutomationCertificate** アクティビティを使用して Runbook でアクセスできます。これにより、認証に証明書を使用する Runbook を作成したり、Runbook が作成または構成できる Azure またはサードパーティのリソースに証明書を追加したりできます。

>[AZURE.NOTE]Secure assets in Azure Automation include credentials, certificates, connections, and encrypted variables.These assets are encrypted and stored in the Azure Automation using a unique key that is generated for each automation account.This key is encrypted by a master certificate and stored in Azure Automation.セキュリティで保護された資産を格納する前に、オートメーション アカウントのキーがマスター証明書を使用して復号化され、資産の暗号化に使用されます。

## Windows PowerShell コマンドレット

Windows PowerShell でオートメーション証明書資産を作成および管理するには、次の表のコマンドレットを使用します。これらは、Automation Runbook で使用できる [Azure PowerShell モジュール](../powershell-install-configure.md)に付属しています。

|コマンドレット|説明|
|:---|:---|
|[Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx)|証明書に関する情報を取得します。Get-AutomationCertificate アクティビティから取得できるのは、証明書自体のみです。|
|[New-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913764.aspx)|新しい証明書を Azure Automation にインポートします。|
|[Remove-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913773.aspx)|証明書を Azure Automation から削除します。|
|[Set-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913763.aspx)|証明書ファイルのアップロードや .pfx のパスワードの設定など、既存の証明書のプロパティを設定します。|

## Runbook アクティビティ

次の表のアクティビティは、Runbook で証明書にアクセスするために使用されます。

|Activities|Description|
|:---|:---|
|Get-AutomationCertificate|Gets a certificate to use in a runbook.|

>[AZURE.NOTE]Get-AutomationCertificate の –Name パラメーターを使用すると、設計時に Runbook と証明書資産の間の依存関係の検出が複雑になる可能性があるため、使用しないようにする必要があります。

## 新しい証明書の作成

新しい証明書を作成するときは、cer または pfx ファイルを Azure Automation にアップロードします。If you mark the certificate as exportable, then you can transfer it out of the Azure Automation certificate store.エクスポート可能ではない場合は、Runbook 内での署名にのみ使用できます。

### Azure ポータルで新しい証明書を作成するには

1. オートメーション アカウントから、ウィンドウの上部にある **[資産]** をクリックします。
1. ウィンドウの下部にある **[設定の追加]** をクリックします。
1. **[資格情報の追加]** をクリックします。
2. **[資格情報の種類]** ドロップダウンで、**[証明書]** を選択します。
3. **[名前]** ボックスに証明書の名前を入力し、右矢印をクリックします。
4. .cer または .pfx ファイルを参照します。If you select a .pfx file, specify a password and whether it should be allowed to be exported.
1. チェック マークをクリックして証明書ファイルをアップロードし、新しい証明書資産を保存します。


### Azure プレビュー ポータルで新しい証明書を作成するには

1. オートメーション アカウントから、**[資産]** 部分をクリックして **[資産]** ブレードを開きます。
1. **[証明書]** 部分をクリックして **[証明書]** ブレードを開きます。
1. ブレード上部の **[証明書の追加]** をクリックします。
2. **[名前]** ボックスに証明書の名前を入力します。
2. **[証明書ファイルをアップロードします]** の **[ファイルの選択]** をクリックして、.cer または .pfx ファイルを参照します。.pfx ファイルを選択する場合は、パスワードおよびエクスポートを許可するかどうかを指定します。
1. **[作成]** をクリックして、新しい証明書資産を保存します。


### Windows PowerShell で新しい証明書を作成するには

次のサンプル コマンドでは、新しいオートメーション証明書を作成してエクスポート可能に指定する方法について説明します。これは既存の pfx ファイルをインポートします。

	$certName = 'MyCertificate'
	$certPath = '.\MyCert.pfx'
	$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
	
	New-AzureAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable

## Runbook での証明書の使用

Runbook で証明書を使用するには、**Get-AutomationCertificate** アクティビティを使用する必要があります。[Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx) コマンドレットは、証明書資産に関する情報は返しますが証明書自体を返さないので使用できません。

### テキストの Runbook のサンプル

次のサンプル コードでは、Runbook でクラウド サービスに証明書を追加する方法を示します。このサンプルでは、パスワードは暗号化されたオートメーション変数から取得されます。

	$serviceName = 'MyCloudService'
	$cert = Get-AutomationCertificate -Name 'MyCertificate'
	$certPwd = Get-AutomationVariable –Name 'MyCertPassword'
	Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### グラフィカルな Runbook のサンプル

グラフィカル エディターの [ライブラリ] ウィンドウで証明書を右クリックして **[キャンバスに追加]** を選択することにより、**Get-AutomationCerticiate** をグラフィカルな Runbook に追加します。

![](media/automation-certificates/certificate-add-canvas.png)

次の図は、グラフィカルな Runbook で証明書を使用する例を示したものです。これは、テキストの Runbook からクラウド サービスに証明書を追加する前述の例と同じです。

この例では、サービスに対する認証に接続オブジェクトを使用する **Send-TwilioSMS** アクティビティに対して設定された **UseConnectionObject** パラメーターを使用します。シーケンス リンクは Connection パラメーターが予期しない単一のオブジェクトを含むコレクションを返すため、ここでは[パイプライン リンク](automation-graphical-authoring-intro.md#links-and-workflow)を使用する必要があります。

![](media/automation-certificates/add-certificate.png)


## 関連項目

- [グラフィカル作成でのリンク](automation-graphical-authoring-intro.md#links-and-workflow) 

<!---HONumber=Oct15_HO3-->