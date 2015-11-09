<properties 
	pageTitle="Azure VM で SQL Server 用に Azure Key Vault 統合を構成する"
	description="Azure Key Vault で使用するために SQL Server 暗号化の構成を自動化する方法について説明します。このトピックでは、SQL Server 仮想マシンで Azure Key Vault 統合を使用する方法について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="10/23/2015"
	ms.author="jroth"/>

# Azure VM で SQL Server 用に Azure Key Vault 統合を構成する

## 概要
[TDE (Transparent Data Encryption/透過的データ暗号化)](https://msdn.microsoft.com/library/bb934049.aspx)、[CLE (Column Level Encryption/列レベル暗号化)](https://msdn.microsoft.com/library/ms173744.aspx)、[バックアップ暗号化](https://msdn.microsoft.com/library/dn449489.aspx) など、SQL Server 暗号化機能が複数存在します。これらの形態の暗号化では、暗号化に利用する暗号鍵を管理し、保存する必要があります。Azure Key Vault (AKV) サービスは、セキュリティを強化し、安全かつ可用性の高い場所で鍵を管理できるように設計されています。[SQL Server コネクタ](http://www.microsoft.com/download/details.aspx?id=45344)を利用すると、SQL Server は Azure Key Vault にある鍵を利用できます。

SQL Server をオンプレミス コンピューターで実行している場合、[いくつかの手順を踏んでオンプレミスの SQL Server コンピューターから Azure Key Vault にアクセスできます](https://msdn.microsoft.com/library/dn198405.aspx)。ただし、Azure VM の SQL Server の場合、*Azure Key Vault 統合*機能を利用し、時間を節約できます。いくつかの Azure PowerShell コマンドレットでこの機能を有効にし、SQL VM が Key Vault にアクセスするために必要な構成を自動化できます。

この機能が有効になっていると、SQL Server コネクタが自動的にインストールされ、Azure Key Vault にアクセスするように EKM プロバイダーが構成され、Vault へのアクセスを許可する資格情報が作成されます。前述のオンプレミス文書の手順を見れば、この機能で手順 2 と 3 が自動化されることがわかります。手動でしなければならないことは、Key Vault と鍵を作成することだけです。そこから先は、SQL VM の設定全体が自動化されます。この機能でこの設定が完了したら、T-SQL ステートメントを実行し、通常どおり、データベースやバックアップの暗号化を開始できます。

## AKV 統合の準備
Azure Key Vault 統合を使用し、SQL Server VM を構成するには、いくつかの前提条件があります。

1.	[Azure PowerShell をインストールする](#install-azure-powershell)
2.	[Azure Active Directory を作成する](#create-an-azure-active-directory)
3.	[Key Vault を作成します](#create-a-key-vault)

次のセクションでは、これらの前提条件と、後に PowerShell コマンドレットを実行するために必要な情報について説明します。

### Azure PowerShell をインストールするには
最新の Azure PowerShell SDK がインストールされていることを確認します。詳細については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。

### Azure Active Directory を作成する
最初に、サブスクリプションに [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) を追加する必要があります。特定のユーザーやアプリケーションが Key Vault にアクセスするための許可が与えられるなど、さまざまな利点があります。

次に、アプリケーションを AAD に登録します。これで VM に必要な Key Vault へのアクセス許可を持つサービス プリンシパル アカウントが与えられます。Azure Key Vault の記事の「[アプリケーションを Azure Active Directory に登録する](../key-vault/key-vault-get-started.md#register)」セクションにこれらの手順があります。あるいは、[このブログ投稿](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx)の「**アプリケーションの ID を取得する**」セクションのスクリーンショットで手順を確認できます。これらの手順を完了する前に、後で SQL VM で Azure Key Vault 統合を有効にするときに必要になる次の情報をこの登録中に集める必要があります。

- アプリケーションが追加されたら、**[構成]** タブで **[クライアント ID]** を探します。 ![Azure Active Directory クライアント ID](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-client-id.png)
	
	クライアント ID は後に PowerShell スクリプトの **$spName** (サービス プリンシパル名) パラメーターに割り当てられ、Azure Key Vault 統合を有効にします。 
- また、これらの手順で、鍵を作成するとき、次のスクリーンショットのように、鍵のシークレットをコピーします。この鍵シークレットは後に PowerShell スクリプトの **$spSecret** (サービス プリンシパル シークレット) パラメーターに割り当てられます。 ![Azure Active Directory シークレット](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-sp-secret.png)
- この新しいクライアント ID に権限を与え、アクセス許可 (**encrypt**、**decrypt**、**wrapKey**、**unwrapKey**、**sign**、**verify**) を与える必要があります。これは [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607%28v=azure.98%29.aspx) コマンドレットで行われます。詳細については、「[キーまたはシークレットを使用してアプリケーションを承認する](../key-vault/key-vault-get-started.md#authorize)」を参照してください。

### Key Vault を作成します
Azure Key Vault を使用して VM の暗号化に使用する鍵を保存するには、Key Vault へのアクセス許可が必要です。Key Vault をまだ設定していない場合、「[Azure Key Vault の概要](../key-vault/key-vault-get-started.md)」トピックの手順で作成します。これらの手順を完了する前に、後で SQL VM で Azure Key Vault 統合を有効にするときに必要になるいくつかの情報をこの設定中に集める必要があります。

「鍵の作成」手順に入ったら、返された **vaultUri** プロパティをメモします。これは Key Vault の URL です。この手順の例では、下のように、Key Vault の名前は「ContosoKeyVault」であり、Key Vault URL は https://contosokeyvault.vault.azure.net/ になります。

![Azure Active Directory シークレット](./media/virtual-machines-sql-server-azure-key-vault-integration/new-azurekeyvault.png)
 
Key Vault ID は後に PowerShell スクリプトの **$akvURL** パラメーターに割り当てられ、Azure Key Vault 統合を有効にします。

## AKV 統合の構成
PowerShell を使用し、Azure Key Vault 統合を構成します。次のセクションでは、必要なパラメーターの概要とサンプル PowerShell スクリプトを提供します。

### 入力パラメーター
次の表は、以降のセクションで PowerShell スクリプトを実行するために必要となるパラメーターをまとめたものです。

|パラメーター|説明|例|
|---|---|---|
|**$akvURL**|**Key Vault の URL**|"https://contosokeyvault.vault.azure.net/"|
|**$spName**|**サービス プリンシパル名**|"fde2b411-33d5-4e11-af04eb07b669ccf2"|
|**$spSecret**|**サービス プリンシパル シークレット**|"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="|
|**$credName**|**資格情報名**: AKV 統合により SQL Server 内に資格情報が作成されます。VM に Key Vault にアクセスする許可が与えられます。この資格情報の名前を選択します。|"mycred1"|
|**$vmName**|**仮想マシン名**: 前に作成した SQL VM の名前。|"myvmname"|
|**$serviceName**|**サービス名**: SQL VM に関連付けられているクラウド サービスの名前。|"mycloudservicename"|

### PowerShell で AKV 統合を有効にする
**New-AzureVMSqlServerKeyVaultCredentialConfig** コマンドレットにより、Azure Key Vault 統合機能の構成オブジェクトが作成されます。**Set-AzureVMSqlServerExtension** により、**KeyVaultCredentialSettings** パラメーターでこの統合が構成されます。次の手順では、これらのコマンドを使用する方法を示します。

1. Azure powershell で、最初に、このトピックの前のセクションで説明した特定の値で入力パラメーターを構成します。次のスクリプトは例です。
	
		$akvURL = "https://contosokeyvault.vault.azure.net/"
		$spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
		$spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
		$credName = "mycred1"
		$vmName = "myvmname"
		$serviceName = "mycloudservicename"
2.	次のスクリプトを利用し、AKV 統合を構成し、有効にします。
	
		$secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
		$akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
		Get-AzureVM –ServiceName $serviceName –Name $vmName | Set-AzureVMSqlServerExtension –KeyVaultCredentialSettings $akvs | Update-AzureVM

SQL IaaS Agent Extension により、この新しい構成で SQL VM が更新されます。

## 次のステップ
Azure Key Vault 統合を有効にしたら、SQL VM で SQL Server 暗号化を有効にできます。最初に、Key Vault 内で非対称鍵を作成し、VM の SQL Server 内で対称鍵を作成する必要があります。これでデータベースとバックアップの暗号化を有効にする T-SQL ステートメントを実行できます。

次のような形式の暗号化を活用できます。

- [透過的なデータ暗号化 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
- [暗号化バックアップ](https://msdn.microsoft.com/library/dn449489.aspx)
- [列レベルの暗号化 (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

次の Transact-SQL スクリプトにはこれらの各領域の例があります。

>[AZURE.NOTE]例はいずれも 2 つ前提条件に基づきます。Key Vault からの「**CONTOSO\_KEY**」という名前の非対称鍵と AKV 統合機能により作成された「**Azure\_EKM\_TDE\_cred**」という名前の資格情報です。

### 透過的なデータ暗号化 (TDE)
1. TDE のためにデータベース エンジンで使用される SQL Server を作成し、それに資格情報を追加します。
	
		USE master;
		-- Create a SQL Server login associated with the asymmetric key 
		-- for the Database engine to use when it loads a database 
		-- encrypted by TDE.
		CREATE LOGIN TDE_Login 
		FROM ASYMMETRIC KEY CONTOSO_KEY;
		GO
		
		-- Alter the TDE Login to add the credential for use by the 
		-- Database Engine to access the key vault
		ALTER LOGIN TDE_Login 
		ADD CREDENTIAL Azure_EKM_TDE_cred;
		GO
	
2. TDE に使用されるデータベース暗号化鍵を作成します。
	
		USE ContosoDatabase;
		GO
		
		CREATE DATABASE ENCRYPTION KEY 
		WITH ALGORITHM = AES_128 
		ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
		GO
		
		-- Alter the database to enable transparent data encryption.
		ALTER DATABASE ContosoDatabase 
		SET ENCRYPTION ON;
		GO

### 暗号化バックアップ
1. バックアップを暗号化するためにデータベース エンジンで使用される SQL Server を作成し、それに資格情報を追加します。
	
		USE master;
		-- Create a SQL Server login associated with the asymmetric key 
		-- for the Database engine to use when it is encrypting the backup.
		CREATE LOGIN Backup_Login 
		FROM ASYMMETRIC KEY CONTOSO_KEY;
		GO 
		
		-- Alter the Encrypted Backup Login to add the credential for use by 
		-- the Database Engine to access the key vault
		ALTER LOGIN Backup_Login 
		ADD CREDENTIAL Azure_EKM_Backup_cred ;
		GO
	
2. Key Vault に保存されている非対称鍵で暗号化を指定し、データベースをバックアップします。
	
		USE master;
		BACKUP DATABASE [DATABASE_TO_BACKUP]
		TO DISK = N'[PATH TO BACKUP FILE]' 
		WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD, 
		ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
		GO

### 列レベルの暗号化 (CLE)
このスクリプトにより、Key Vault の非対称鍵で保護される対称鍵が作成され、その対称鍵を利用し、データベースのデータが暗号化されます。

	CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
	WITH ALGORITHM=AES_256
	ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;
	
	DECLARE @DATA VARBINARY(MAX);
	
	--Open the symmetric key for use in this session
	OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY 
	DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;
	
	--Encrypt syntax
	SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));
	
	-- Decrypt syntax
	SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));
	
	--Close the symmetric key
	CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;

## その他のリソース
これらの暗号化機能の使用方法の詳細については、「[SQL Server 暗号化機能で EKM を使用する](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM)」を参照してください。

この記事の手順では、Azure 仮想マシンで SQL Server を既に実行していることを前提としています。実行していない場合、「[Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-provision-sql-server.md)」を参照してください。Azure VM で SQL Server を実行する方法については、「[Azure Virtual Machines における SQL Server の概要](virtual-machines-sql-server-infrastructure-services.md)」を参照してください。

<!---HONumber=Nov15_HO1-->