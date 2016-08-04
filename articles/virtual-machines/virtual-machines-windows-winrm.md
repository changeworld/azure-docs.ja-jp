<properties
	pageTitle="Azure Resource Manager の仮想マシンに WinRM アクセスを設定する | Microsoft Azure"
	description="Azure Resource Manager の仮想マシンと共に使用するために WinRM アクセスを設定する方法"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="singhkay"/>

# Azure Resource Manager の仮想マシンの WinRM アクセスを設定する

## Azure サービス管理の WinRM と Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] クラシック デプロイ モデル

* Azure Resource Manager の概要については、こちらの[記事](../resource-group-overview.md)を参照してください。
* Azure サービス管理と Azure Resource Manager の違いについては、こちらの[記事](../resource-manager-deployment-model.md)を参照してください。

2 つのスタックで WinRM 構成を設定する場合の主な違いは、VM に証明書をインストールする方法です。Azure Resource Manager スタックでは、Key Vault リソース プロバイダーが管理するリソースとして証明書がモデル化されます。そのため、ユーザーは自分の証明書を VM で使用する前に、Key Vault にアップロードする必要があります。

ここでは WinRM 接続を備えた VM のセットアップに必要な手順を説明します。

1. Key Vault の作成
2. 自己署名証明書の作成
3. 自己署名証明書を Key Vault にアップロードする
4. Key Vault の自己署名証明書の URL を取得する
5. VM を作成するときに、自己署名証明書の URL を参照する

## 手順 1: Key Vault を作成する

次のコマンドを使用して、Key Vault を作成します

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## 手順 2: 自己署名証明書を作成する
この PowerShell スクリプトを使用して、自己署名証明書を作成します

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## 手順 3: Key Vault に自己署名証明書をアップロードする

手順 1 で作成した Key Vault に証明書をアップロードする前に、Microsoft.Compute リソース プロバイダーが理解する形式への変換が必要です。次の PowerShell スクリプトにより、実行が許可されます

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## 手順 4: Key Vault の自己署名証明書の URL を取得する

VM をプロビジョニングするときに、Microsoft.Compute リソース プロバイダーには Key Vault 内部のシークレットへの URL が必要です。これにより、Microsoft.Compute のリソース プロバイダーがシークレットをダウンロードして、VM 上に同様の証明書を作成することができます。

>[AZURE.NOTE]シークレットの URL には、バージョンも含める必要があります。URL の例を次に示します https://contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve


#### テンプレート

次のコードを使用して、テンプレートの URL へのリンクを取得する事ができます

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### PowerShell

次の PowerShell コマンドを使用して、この URL を取得することができます

	$secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## 手順 5: VM を作成するときに、自己署名証明書の URL を参照する

#### Azure Resource Manager のテンプレート

テンプレートを使用して VM を作成する場合、"secrets" セクションと "WinRM" セクションで証明書を次のように参照します。

	"osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

上記のサンプル テンプレートは、[201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows) にあります。

このテンプレートのソース コードは [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows) にあります。

#### PowerShell

	$vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
	$credential = Get-Credential
	$secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
	$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
	$sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
	$CertificateStore = "My"
	$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## 手順 6 - VM に接続する
VM に接続する前に、WinRM リモート管理のためにコンピューターが構成されていることを確認する必要があります。管理者として PowerShell を開始し、次のコマンドを実行して設定を確認します。

    Enable-PSRemoting -Force

>[AZURE.NOTE] 上記が動作しない場合は、WinRM サービスが実行されていることを確認する必要があります。`Get-Service WinRM` を使用して、確認することができます。

設定が完了すると、次のコマンドを使用して VM に接続することができます。

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate

<!---HONumber=AcomDC_0629_2016-->
