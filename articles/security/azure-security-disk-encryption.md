<properties
   pageTitle="Windows および Linux IaaS VM の Azure Disk Encryption | Microsoft Azure"
   description="このドキュメントでは、Windows および Linux IaaS VM の Microsoft Azure Disk Encryption の概要を示します。"
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="krkhan"/>


#Windows および Linux IaaS VM の Azure ディスク暗号化

Microsoft Azure では、データのプライバシーおよびデータ主権の確保に積極的に取り組んでおり、暗号化キーの暗号化、制御、管理、また、データ アクセスの制御と監査を行うための幅広い先進テクノロジを介して Azure でホストされるデータを制御できます。そのため、Azure の顧客はビジネス ニーズに最適なソリューションを柔軟に選択することができます。このドキュメントでは、"Windows および Linux IaaS VM の Azure Disk Encryption" という新しいテクノロジ ソリューションを紹介します。このソリューションは、組織のセキュリティおよびコンプライアンス コミットメントを満たすためのデータの保護に役立ちます。ここでは、サポートされているシナリオやユーザー エクスペリエンスを含む、Azure Disk Encryption 機能の使用方法に関する詳細なガイダンスを提供します。

**注**: ここに含まれる特定の推奨事項により、データ、ネットワーク、またはコンピューティング リソースの使用量が増え、その結果、ライセンスまたはサブスクリプション コストの追加が必要になる可能性があります。

## 概要

Azure Disk Encryption は、Windows および Linux IaaS 仮想マシン ディスクを暗号化できる新機能です。Azure ディスク暗号化では、Windows の業界標準である [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 機能と Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能を利用して、OS およびデータ ディスクのボリュームの暗号化を提供します。ソリューションは [Azure Key](https://azure.microsoft.com/documentation/services/key-vault/) Vault と統合されています。これは、Key Vault サブスクリプションでディスク暗号化キーとシークレットを制御および管理し、Azure ストレージで保存中の仮想マシン ディスク内のすべてのデータを確実に暗号化する場合に役立ちます。

現在、Windows および Linux IaaS VM の Azure Disk Encryption は、Azure のすべてのパブリック リージョンの Standard VM と Premium Storage を使用する VM で**一般に提供**されています。

### 暗号化のシナリオ

Azure Disk Encryption ソリューションでは、次の顧客シナリオがサポートされています。

- 事前に暗号化した VHD と暗号化キーから作成された新しい IaaS VM での暗号化を有効にする
- Azure ギャラリー イメージから作成された新しい IaaS VM での暗号化を有効にする
- Azure で実行されている既存の IaaS VM での暗号化を有効にする
- Windows IaaS VM での暗号化を無効にする
- Linux IaaS VM でのデータ ドライブの暗号化を無効にする

このソリューションでは、Microsoft Azure で有効になっている場合、IaaS VM で以下がサポートされます。

- Azure Key Vault との統合
- Standard レベルの VM - [A、D、DS、G、GS などの IaaS VM シリーズ](https://azure.microsoft.com/pricing/details/virtual-machines/)
- Windows および Linux IaaS VM での暗号化を有効にする
- Windows IaaS VM での OS およびデータ ドライブの暗号化を無効にする
- Linux IaaS VM でのデータ ドライブの暗号化を無効にする
- Windows クライアント OS を実行している IaaS VM での暗号化を有効にする
- ボリュームのマウント パスでの暗号化を有効にする
- ソフトウェアベースの RAID システムで構成されている Linux VM での暗号化を有効にする
- 記憶域スペースで構成されている Windows VM での暗号化を有効にする
- Azure のすべてのパブリック リージョンがサポートされる

このソリューションでは、このリリースの以下のシナリオ、機能およびテクノロジはサポートされません。

- Basic レベルの IaaS VM
- Linux IaaS VM での OS ドライブの暗号化を無効にする
- 従来の VM の作成方法を使用して作成された IaaS VM
- オンプレミス キー管理サービスとの統合
- このリリースでは、Windows Server 2016 Technical Preview はサポートされない
- Azure Files (Azure ファイル共有)、ネットワーク ファイル共有 (NFS)、ダイナミック ボリューム、ソフトウェアベースの RAID システムで構成されている Windows VM


### 暗号化機能

Azure IaaS VM の Azure Disk Encryption を有効にしてデプロイすると、指定された構成に応じて、以下の機能が有効になります。

- 顧客ストレージに保存中のブート ボリュームを保護するための OS ボリュームの暗号化
- 顧客ストレージに保存中のデータ ボリュームを保護するためのデータ ボリュームの暗号化
- Windows IaaS VM での OS およびデータ ドライブの暗号化を無効にする
- Linux IaaS VM でのデータ ドライブの暗号化を無効にする
- 顧客の Azure Key Vault サブスクリプションの暗号化キーとシークレットの保護
- 暗号化された IaaS VM の暗号化状態のレポート
- IaaS 仮想マシンからのディスク暗号化構成設定の削除

Windows および Linux 用 IaaS VM の Azure Disk Encryption ソリューションには、Windows のディスク暗号化拡張機能、Linux のディスク暗号化拡張機能、ディスク暗号化 PowerShell コマンドレット、ディスク暗号化 CLI コマンドレットおよびディスク暗号化 Azure リソース マネージャー テンプレートが含まれます。Azure Disk Encryption ソリューションは、Windows または Linux OS を実行している IaaS VM でサポートされます。サポートされているオペレーティング システムの詳細については、後述の「前提条件」セクションをご覧ください。

**注:** Azure Disk Encryption による VM ディスクの暗号化による追加料金は発生しません。

### 価値の提案

Azure Disk Encryption の管理ソリューションは、クラウドにおいて次のビジネス ニーズに対応できます。

-   業界標準の暗号化テクノロジを使用して保存中の IaaS VM をセキュリティで保護し、組織のセキュリティおよびコンプライアンス要件に対処する。
-   IaaS VM は顧客管理のキーとポリシーに従ってブートし、Key Vault での使用量を監査することができる。


### 暗号化のワークフロー

Windows および Linux VM に対してディスク暗号化を有効にするために必要な手順の概要を以下に示します。

1. 顧客は、上記暗号化シナリオから暗号化シナリオを選択します。
2. 顧客は、Azure Disk Encryption Resource Manager テンプレート、PS コマンドレット、または CLI コマンドを使用してディスク暗号化を有効にすることを選択し、暗号化構成を指定します。

    - 顧客が暗号化した VHD シナリオの場合、顧客は暗号化した VHD をストレージ アカウントに、暗号化キー マテリアルを Key Vault にアップロードし、暗号化構成を指定して新しい IaaS VM での暗号化を有効にします。
    - Azure ギャラリーから作成された新しい VM と Azure で既に実行されている既存の VM の場合、顧客は、IaaS VM での暗号化を有効にするための暗号化構成を指定します。

3. 顧客は Key Vault からの暗号化キー マテリアル (Windows システムの場合は BitLocker 暗号化キー、Linux の場合はパスフレーズ) の読み取りアクセス権を Azure プラットフォームに付与し、IaaS VM での暗号化を有効にします。
4. 顧客は Key Vault に暗号化キー マテリアルを書き込むための Azure AD アプリケーション ID を指定し、上記シナリオ 2 の IaaS VM での暗号化を有効にします。
5.  Azure では暗号化および Key Vault 構成で VM サービス モデルを更新し、顧客のために暗号化された VM をプロビジョニングします。

![Azure での Microsoft マルウェア対策](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### 暗号化解除のワークフロー

IaaS VM に対するディスク暗号化を無効にするために必要な手順を以下に示します。

1. Azure Disk Encryption Resource Manager テンプレートまたは PS コマンドレットを使用して暗号化解除の構成を指定することで、Azure で実行中の IaaS VM での暗号化を無効に （暗号化解除） します。
2. 暗号化を無効にする手順によって、Windows IaaS VM で実行されている OS またはデータ ボリューム、もしくはその両方の暗号化が無効になります。ただし、Linux で OS ディスクの暗号化を無効にすることは、先に述べたように、サポートされていません。この無効化手順は、Linux VM のデータ ドライブでのみ許可されます。
4. Azure によって VM サービス モデルが更新され、IaaS VM は暗号化解除済みとしてマークされます。VM の内容は保存時に暗号化されなくなります。
5. 暗号化を無効にしても、顧客の Key Vault や暗号化キー マテリアル (Windows システムの場合は BitLocker 暗号化キー、Linux の場合はパスフレーズ) は削除されません。

## 前提条件

概要セクションに記載されているサポート対象シナリオの Azure IaaS VM で Azure Disk Encryption を有効にするための前提条件を以下に示します。

- ユーザーは、サポートされているリージョンにおいて Azure でリソースを作成するための有効なアクティブ Azure サブスクリプションが必要です。
- Azure Disk Encryption は、Windows サーバー SKU (Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2) でサポートされています。このリリースでは、Windows Server 2016 Technical Preview はサポートされません。
- Azure Disk Encryption は、Windows クライアント SKU (Windows 8 クライアントおよび Windows 10 クライアント) でサポートされています。

**注**: Windows Server 2008 R2 の場合、Azure で暗号化を有効にする前に、.Net Framework 4.5 をインストールする必要があります。オプションの更新プログラムである "Windows Server 2008 R2 x64 ベース システム用の Microsoft .NET Framework 4.5.2 ([KB2901983](https://support.microsoft.com/kb/2901983))" をインストールし、Windows 更新プログラムからこれをインストールすることができます。

- Azure Disk Encryption は、Linux サーバー SKU (Ubuntu、CentOS、SUSE、SUSE Linux Enterprise Server (SLES) および Red Hat Enterprise Linux) でサポートされています。

**注**: 現時点では、Linux OS ディスク暗号化は、次の Linux ディストリビューションでサポートされています - RHEL 7.2、CentOS 7.2、Ubuntu 16.04。

- すべてのリソース (例: Key Vault、ストレージ アカウント、VM など) が同じ Azure リージョンとサブスクリプションに属している必要があります。

**注**: Azure Disk Encryption では、Key Vault と VM が同じ Azure リージョンに属している必要があります。それぞれ別のリージョンで構成すると、Azure Disk Encryption 機能を有効にする場合にエラーが発生します。

- Azure Disk Encryption の使用について Azure Key Vault をセットアップして構成する場合は、この記事の「*前提条件*」セクションの「**Azure Disk Encryption の使用に関する Azure Key Vault の設定と構成**」を参照してください。
- Azure Disk Encryption の使用について、Azure Active Directory で Azure AD アプリケーションをセットアップして構成する場合は、この記事の「*前提条件*」セクションの「**Azure Active Directory での Azure AD アプリケーションのセットアップ**」を参照してください。
- Azure AD アプリケーションに関する Key Vault アクセス ポリシーをセットアップして構成する場合は、この記事の「*前提条件*」セクションの「**Azure AD アプリケーションの Key Vault アクセス ポリシーの設定**」を参照してください。
- 事前に暗号化された Windows VHD を準備する場合は、この記事の付録の「**事前に暗号化された Windows VHD の準備**」セクションを参照してください。
- 事前に暗号化された Linux VHD を準備する場合は、この記事の付録の「**事前に暗号化された Linux VHD の準備**」セクションを参照してください。
- Azure プラットフォームは、仮想マシンでの仮想マシン OS ボリュームのブートと暗号化解除で使用できるように、顧客の Azure Key Vault の暗号化キーまたはシークレットにアクセスする必要があります。顧客の Key Vault へのアクセス権を Azure プラットフォームに付与するために、この要件に応じて Key Vault に **enabledForDiskEncryption** プロパティを設定する必要があります。詳細については、この記事の付録の「**Azure ディスク暗号化の使用に関する Azure Key Vault の設定と構成**」セクションを参照してください。
- Key Vault シークレットおよびキー暗号化キー (KEK) の URL のバージョン管理が必要です。Azure では、このバージョン管理制限が適用されます。有効なシークレットおよび KEK URL については、以下の例をご覧ください。
	- 有効なシークレット URL の例: *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
	- 有効な KRK KEK の例: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
- Azure Disk Encryption では、ポート番号を Key Vault シークレットおよび KEK URL の一部として指定することはサポートされません。サポートされる Key Vault URL については、以下の例をご覧ください。
 	- 受け入れられない Key Vault URL: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
	- 受け入れられる Key Vault URL: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
- Azure Disk Encryption 機能を有効にするには、IaaS VM が次のネットワーク エンドポイントの構成要件を満たす必要があります。
	- IaaS VM は、Azure Key Vault に接続するためのトークンを取得するときに、Azure Active Directory エンドポイント [Login.windows.net] に接続できる必要があります。
	- IaaS VM は、暗号化キーを顧客の Key Vault に書き込むために、Azure Key Vault エンドポイントに接続できる必要があります。
	- IaaS VM は、Azure 拡張リポジトリをホストする Azure ストレージ エンドポイントと、VHD ファイルをホストする Azure ストレージ アカウントに接続できる必要があります。

**注:** セキュリティ ポリシーで Azure VM からインターネットへのアクセスが制限されている場合は、接続する必要がある上記 URI を解決し、IP への送信接続を許可するための特定のルールを構成することができます。

- Azure PowerShell SDK の最新バージョンを使用して、Azure Disk Encryption を構成します。[Azure PowerShell リリース](https://github.com/Azure/azure-powershell/releases)の最新バージョンをダウンロードしてください。

**注:** Azure Disk Encryption は、[Azure PowerShell SDK Version 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016) ではサポートされていません。Azure PowerShell 1.1.0 の使用に関連するエラーが発生する場合、「[Azure Disk Encryption Error Related to Azure PowerShell 1.1.0 (Azure PowerShell 1.1.0 に関連する Azure Disk Encryption エラー)](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx)」を参照してください。

- Azure CLI コマンドのいずれかを実行して、Azure サブスクリプションに関連付けるには、まず、以下のように Azure CLI バージョンをインストールする必要があります。
	- Azure CLI をインストールして、Azure サブスクリプションに関連付ける場合は、[Azure CLI のインストールおよび構成方法](../xplat-cli-install.md)に関するページを参照してください。
	- Azure リソース マネージャーでの Mac、Linux、および Windows 用 Azure CLI の使用については、[こちら](azure-cli-arm-commands.md)を参照してください。
- Azure Disk Encryption ソリューションでは、Windows IaaS VM に対して BitLocker 外部キー保護機能を使用します。VM がドメインに参加している場合は、TPM 保護機能を適用するグループ ポリシーをプッシュしないでください。[互換性のある TPM が装備されていない BitLocker を許可する] というグループ ポリシーの詳細については、[この記事](https://technet.microsoft.com/library/ee706521)を参照してください。
- Azure Disk Encryption の前提条件となる、Azure AD アプリケーションを作成し、新しい Key Vault を作成するか既存の Key Vault を設定して、暗号化を有効にする PowerShell スクリプトは、[こちら](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1)にあります。

#### Azure Active Directory での Azure AD アプリケーションのセットアップ

Azure で実行中の VM に対して暗号化を有効にする必要がある場合、Azure Disk Encryption により、暗号化キーが生成され、Key Vault に書き込まれます。Key Vault の暗号化キーを管理するには、Azure AD 認証が必要です。

そのため、Azure AD アプリケーションを作成する必要があります。アプリケーションの詳しい登録手順については、この[ブログ投稿](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)のアプリケーションの ID の取得に関するセクションを参照してください。この投稿には、Key Vault のプロビジョニングおよび構成に関する役立つ例も多数含まれています。認証目的で、クライアント シークレット ベースの認証またはクライアント証明書ベースの Azure AD 認証を使用できます。

##### Azure AD のクライアント シークレット ベースの認証

以降のセクションでは、Azure AD のクライアント シークレット ベースの認証を構成するために必要な手順が示されています。

##### Azure PowerShell を使用して新しい Azure AD アプリを作成する

新しい Azure AD アプリを作成するには、以下の PowerShell コマンドレットを使用します。

    $aadClientSecret = “yourSecret”
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

**注:** $azureAdApplication.ApplicationId は Azure AD ClientID、$aadClientSecret はクライアント シークレットで ADE を有効にするために後で使用する必要があります。Azure AD クライアントのシークレットは適切に保護してください。


##### Azure クラシック デプロイ モデルからの Azure AD のクライアント ID とシークレットのプロビジョニング

Azure AD のクライアント ID とシークレットも、Azure クラシック デプロイ モデル ポータル (https://manage.windowsazure.com) でプロビジョニングできます。このタスクを実行するには、以下の手順に従います。

1\. 次の図に示すように、[Active Directory] タブをクリックします。

![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig3.png)

2\. [アプリケーションの追加] をクリックし、次のようにアプリケーション名を入力します。

![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig4.png)

3\. 矢印ボタンをクリックし、次のようにアプリのプロパティを構成します。

![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig5.png)

4\. 左下隅にあるチェック マークをクリックして終了します。アプリの構成ページが表示されます。次の図に示されているように、Azure AD のクライアント ID がページの下部にあることがわかります。

![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig6.png)

5\. [保存] をクリックして、Azure AD のクライアント シークレットを保存します。[保存] をクリックし、キーのテキスト ボックスのシークレットをメモします。これが Azure AD のクライアント シークレットです。Azure AD のクライアント シークレットは適切に保護する必要があります。

![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig7.png)


**注:** 上記のフローはポータルではサポートされません。

##### 既存のアプリの使用

以下のコマンドを実行するには、Azure AD PowerShell モジュールが必要です ([ここ](https://technet.microsoft.com/library/jj151815.aspx)から取得できます)。

**注:** 以下のコマンドは新しい PowerShell ウィンドウから実行する必要があります。これらのコマンドを実行する場合は、Azure PowerShell や Azure リソース マネージャーのウィンドウを使用しないでください。このように推奨するのは、これらのコマンドレットが MSOnline モジュールまたは Azure AD PowerShell にあるためです。

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### Azure AD の証明書ベースの認証

> [AZURE.NOTE] AAD 証明書ベースの認証は、現時点では Linux VM ではサポートされていません。

以降のセクションには、Azure AD の証明書ベースの認証を構成するために必要な手順が示されています。

##### 新しい Azure AD アプリの作成

新しい Azure AD アプリを作成するには、以下の PowerShell コマンドレットを実行します。

**注:** 以下の `yourpassword` 文字列をセキュリティで保護されたパスワードに置き換え、パスワードを保護してください。

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

この手順が完了したら、.pfx ファイルを Key Vault にアップロードし、VM にその証明書をデプロイするために必要なアクセス ポリシーを有効にします。

##### 既存の Azure AD アプリの使用
既存のアプリの証明書ベースの認証を構成する場合は、次の PowerShell コマンドレットを使用します。必ず、新しい PowerShell ウィンドウから実行してください。

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

この手順が完了したら、.pfx ファイルを Key Vault にアップロードし、VM にその証明書をデプロイするために必要なアクセス ポリシーを有効にします。

##### PFX ファイルを Key Vault にアップロードする
この処理のしくみの詳細については、この[ブログ投稿](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)を参照してください。ただし、このタスクで必要なものは以下の PowerShell コマンドレットだけです。必ず、Azure PowerShell コンソールから実行してください。

**注:** 以下の `yourpassword` 文字列をセキュリティで保護されたパスワードに置き換え、パスワードを保護してください。

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### 既存の VM に Key Vault の証明書をデプロイする
PFX のアップロードが完了したら、以下の手順を使用して、Key Vault の証明書を既存の VM にデプロイします。

    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName


#### Azure AD アプリケーションの Key Vault アクセス ポリシーの設定

Azure AD アプリケーションには、Vault 内のキーまたはシークレットへのアクセス権が必要です。[Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx) コマンドレットで、–ServicePrincipalName パラメーター値としてクライアント ID (アプリケーションの登録時に生成されたもの) を使用して、アプリケーションに権限を付与します。そのいくつかの例については、[このブログ投稿](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)を参照してください。PowerShell を使用してこのタスクを実行する方法の以下の例もご覧ください。

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

**注**: Azure Disk Encryption では、AAD クライアント アプリケーションに対して、アクセス ポリシー ('WrapKey' および 'Set' アクセス許可) を構成する必要があります。

## 用語集

このテクノロジで使用される一般的な用語をいくつか理解するために、参照としての以下の用語表を使用します。

| 用語集 | 定義 |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure AD | Azure AD とは [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) のことです。Azure AD アカウントは、Key Vault のシークレットの認証、格納、および取得を行うための前提条件です。 |
| Azure Key Vault (AKV) | Azure Key Vault は、暗号化キーと機密性の高い情報を安全に保護するために FIPS 検証済みハードウェア セキュリティ モジュールに基づく暗号化キー管理サービスです。詳細については、[Key Vault](https://azure.microsoft.com/services/key-vault/) に関するドキュメントを参照してください。 |
| ARM | Azure リソース マネージャー |
| BitLocker | [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) は、Windows IaaS VM でディスク暗号化を有効にするために使用される業界公認の Windows ボリューム暗号化テクノロジです。 |
| BEK | BitLocker 暗号化キーは、OS ブート ボリュームとデータ ボリュームの暗号化に使用されます。BitLocker キーは、顧客の Azure Key Vault でシークレットとして保護されます。 |
| CLI | [Azure コマンド ライン インターフェイス](../xplat-cli-install.md) |
| DM-Crypt | [DM Crypt](https://en.wikipedia.org/wiki/Dm-crypt) は、Linux IaaS VM でディスク暗号化を有効にするために使用される Linux ベースの透過的なディスク暗号化サブシステムです。 |
| KEK | キー暗号化キーは、必要に応じてシークレットを保護またはラップするために使用される非対称キー (RSA 2048) です。HSM で保護されたキーまたはソフトウェアで保護されたキーを指定できます。詳細については、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) に関するドキュメントを参照してください。 |
| PS コマンドレット | [Azure PowerShell コマンドレット](powershell-install-configure.md) |

### Azure Disk Encryption の使用に関する Azure Key Vault の設定と構成

Azure Disk Encryption では、Azure Key Vault のディスク暗号化キーとシークレットを保護します。Azure Disk Encryption の使用について Key Vault をセットアップするには、以下のセクションのいずれかの手順に従います。

#### 新しい Key Vault の作成
新しい Key Vault を作成するには、以下に示されているオプションのいずれかを使用します。

- [ここ](https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-key-vault/azuredeploy.json)にある "101-Create-KeyVault" Resource Manager テンプレートを使用する。
- Azure PowerShell の [Key Vault](https://msdn.microsoft.com/library/dn868052.aspx) コマンドレットを使用する。
- Azure Resource Manager ポータルを使用する。

**注:** サブスクリプションについて既に Key Vault をセットアップしている場合は、次のセクションに進んでください。

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### キー暗号化キーのプロビジョニング (省略可能)

キー暗号化キー (KEK) を使用して、BitLocker 暗号化キーをラップするためのセキュリティ層を追加する場合は、プロビジョニング プロセスで使用するために Key Vault に KEK を追加する必要があります。Key Vault で新しいキー暗号化キーを作成するには、[Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868048.aspx) コマンドレットを使用します。オンプレミスのキー管理 HSM から KEK をインポートすることもできます。詳細については、[Key Vault のドキュメント](https://azure.microsoft.com/documentation/services/key-vault/)を参照してください。

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

KEK は、Azure Key Vault UX を使用して Azure Resource Manager ポータルから追加できます。

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### Azure プラットフォームからキーとシークレットにアクセスできるように Key Vault のアクセス許可を設定する

Azure プラットフォームは Azure Key Vault の暗号化キーまたはシークレットにアクセスし、VM でのボリュームのブートと暗号化解除に使用できるようにする必要があります。Key Vault にアクセスできるように Azure プラットフォームに権限を付与するには、Key Vault で *enabledForDiskEncryption* プロパティを設定する必要があります。以下の Key Vault PS コマンドレットを使用して、Key Vault に enabledForDiskEncryption プロパティを設定することができます。

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

*enabledForDiskEncryption* プロパティは、https://resources.azure.com で設定することもできます。前述のとおり、Key Vault には *enabledForDiskEncryption* プロパティを設定する必要があります。17 以下の場合、デプロイは失敗します。

AAD アプリケーションのアクセス ポリシーは、Key Vault UX から設定できます。

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

[高度なアクセス ポリシー] で、Key Vault がDisk Encryption に対して有効になっていることを確認します。

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## ディスク暗号化のデプロイ シナリオとユーザー エクスペリエンス

ディスク暗号化を有効にできるシナリオは多数あり、手順はシナリオによって異なる場合があります。以下のセクションでは、これらのシナリオについて詳しく説明します。

### Azure ギャラリーから作成された新しい IaaS VM での暗号化を有効にする

[ここ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)で公開されている Resource Manager テンプレートを使用して、Azure で Azure ギャラリーから作成された新しい IaaS Windows VM でのディスク暗号化を有効にすることができます。Azure クイック スタート テンプレートの [Azure にデプロイ] ボタンをクリックし、パラメーター ブレードに暗号化構成を入力して [OK] をクリックします。サブスクリプション、リソース グループ、リソース グループの場所、法律条項および契約を選択し、[作成] ボタンをクリックして新しい IaaS VM での暗号化を有効にします。

**注:** このテンプレートでは、Windows Server 2012 ギャラリー イメージを使用して、新しい暗号化された Windows VM を作成します。

200 GB の RAID-0 アレイを使用する新しい IaaS RedHat Linux 7.2 VM では、[この](https://aka.ms/fde-rhel) Resource Manager テンプレートを使用してディスク暗号化を有効にできます。テンプレートをデプロイした後、「[実行中の Linux VM での OS ドライブの暗号化](#encrypting-os-drive-on-a-running-linux-vm)」セクションで説明されている `Get-AzureRmVmDiskEncryptionStatus` コマンドレットを使用して、暗号化の状態を確認します。`VMRestartPending` 状態が返る場合は、VM を再起動します。

次の表で、Azure AD クライアント ID を使用して Azure ギャラリーから新しい VM を作成するシナリオで使用される Resource Manager テンプレートのパラメーターの詳細を参照できます。

| パラメーター | 説明|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| adminUserName | 仮想マシンの管理者ユーザー名 |
| adminPassword | 仮想マシンの管理者ユーザー パスワード |
| newStorageAccountName | OS とデータの VHD を格納するためのストレージ アカウントの名前 |
| vmSize | VM のサイズ。現時点では、Standard A、D および G シリーズのみがサポートされています。 |
| virtualNetworkName | VM NIC が属している必要がある VNet の名前。 |
| subnetName | VM NIC が属している必要がある VNet のサブネットの名前。 |
| AADClientID | Key Vault へのシークレットの書き込み権限を持つ Azure AD アプリのクライアント ID |
| AADClientSecret | Key Vault へのシークレットの書き込み権限を持つ Azure AD アプリのクライアント シークレット |
| keyVaultURL | BitLocker キーをアップロードする必要がある Key Vault の URL。コマンドレット (Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI を使用して取得できます。 |
| keyEncryptionKeyURL | 生成された BitLocker キーの暗号化に使用されるキー暗号化キーの URL。これは省略可能です。 |
| keyVaultResourceGroup | Key Vault のリソース グループ |
| vmName | 暗号化操作を実行する VM の名前


**注:** KeyEncryptionKeyURL は省略可能なパラメーターです。独自の KEK を使用して、Key Vault でデータ暗号化キー (パスフレーズ シークレット) の保護を強化することができます。

### 顧客が暗号化した VHD と暗号化キーから作成された新しい IaaS VM での暗号化を有効にする

このシナリオでは、Resource Manager テンプレート、PowerShell コマンドレット、または CLI コマンドを使用して、暗号化を有効にすることができます。以下のセクションでは、Resource Manager テンプレートと CLI コマンドについて詳しく説明します。

次のいずれかのセクションの指示に従って、Azure で使用できる事前に暗号されたイメージを準備します。イメージを作成したら、次のセクションの手順に従って、暗号化された Azure VM を作成できます。

- [事前に暗号化された Windows VHD の準備](#preparing-a-pre-encrypted-windows-vhd)
- [事前に暗号化された Linux VHD の準備](#preparing-a-pre-encrypted-linux-vhd)

#### Resource Manager テンプレートの使用

[ここ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm)で公開されている Resource Manager テンプレートを使用して、顧客が暗号化した VHD でのディスク暗号化を有効にすることができます。Azure クイック スタート テンプレートの [Azure にデプロイ] ボタンをクリックし、パラメーター ブレードに暗号化構成を入力して [OK] をクリックします。サブスクリプション、リソース グループ、リソース グループの場所、法律条項および契約を選択し、[作成] ボタンをクリックして新しい IaaS VM での暗号化を有効にします。

次の表で、顧客が暗号化した VHD シナリオで使用される Resource Manager テンプレートのパラメーターについて詳しく説明します。

| パラメーター | 説明|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| newStorageAccountName | 暗号化された OS vhd を格納するためのストレージ アカウントの名前。このストレージ アカウントは、VM と同じリソース グループおよび同じ場所に既に作成されている必要があります。 |
| osVhdUri | ストレージ アカウントからの OS vhd の URI |
| osType | OS 製品の種類 (Windows/Linux) |
| virtualNetworkName | VM NIC が属している必要がある VNet の名前。これは、VM と同じリソース グループおよび同じ場所に既に作成されている必要があります。 |
| subnetName | VM NIC が属している必要がある VNet のサブネットの名前。 |
| vmSize | VM のサイズ。現時点では、Standard A、D および G シリーズのみがサポートされています。 |
| keyVaultResourceID | ARM で Key Vault リソースを識別する ResourceID。PowerShell コマンドレット (Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId を使用して取得できます。 |
| keyVaultSecretUrl | ​Key Vault でプロビジョニングされたディスク暗号化キーの URL |
| keyVaultKekUrl | 生成されたディスク暗号化キーの暗号化に使用されるキー暗号化キーの URL |
| ​vmName | ​IaaS VM の名前 |


#### PowerShell コマンドレットの使用

[ここ](https://msdn.microsoft.com/library/azure/mt603746.aspx)で公開されている PS コマンドレットを使用して、顧客が暗号化した VHD でディスク暗号化を有効にすることができます。

#### CLI コマンドの使用

CLI コマンドを使用するこのシナリオでは、次の手順に従ってディスク暗号化を有効にします。

1. 以下のように、Key Vault でアクセス ポリシーを設定します。
	- ‘EnabledForDiskEncryption’ フラグを設定します。`azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
	- KeyVault にシークレットを書き込むための Azure AD アプリに対するアクセス許可を設定します。`azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]`
2. 既存/実行中の VM で暗号化を有効にするために、次のように入力します: *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. 暗号化状態を取得します: *"azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json"*
4. 顧客が暗号化した VHD からの新しい VM での暗号化を有効にするには、“azure vm create” コマンドで以下のパラメーターを使用します。
	- disk-encryption-key-vault-id <disk-encryption-key-vault-id>
	- disk-encryption-key-url <disk-encryption-key-url>
	- key-encryption-key-vault-id <key-encryption-key-vault-id>
	- key-encryption-key-url <key-encryption-key-url>


### Azure で既存または実行中の IaaS Windows VM での暗号化を有効にする

このシナリオでは、Resource Manager テンプレート、PowerShell コマンドレット、または CLI コマンドを使用して、暗号化を有効にすることができます。以下のセクションでは、Resource Manager テンプレートと CLI コマンドを使用して有効にする方法について詳しく説明します。

#### Resource Manager テンプレートの使用

[ここ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)で公開されている Resource Manager テンプレートを使用して、Azure で既存/実行中の IaaS Windows VM でのディスク暗号化を有効にすることができます。Azure クイック スタート テンプレートの [Azure にデプロイ] ボタンをクリックし、パラメーター ブレードに暗号化構成を入力して [OK] をクリックします。サブスクリプション、リソース グループ、リソース グループの場所、法律条項および契約を選択し、[作成] ボタンをクリックして既存/実行中の IaaS VM での暗号化を有効にします。

次の表で、Azure AD クライアント ID を使用する既存/実行中の VM シナリオで使用される Resource Manager テンプレートのパラメーターの詳細を参照できます。

| パラメーター | 説明|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID | ​Key Vault へのシークレットの書き込み権限を持つ Azure AD アプリのクライアント ID |
| AADClientSecret | ​Key Vault へのシークレットの書き込み権限を持つ Azure AD アプリのクライアント シークレット |
| KeyVaultName | BitLocker キーのアップロード先となる Key Vault の名前。コマンドレット (Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>).Vaultname を使用して取得できます。 |
| ​ keyEncryptionKeyURL | 生成された BitLocker キーの暗号化に使用されるキー暗号化キーの URL。UseExistingKek ドロップダウンで `nokek` を選択した場合は省略可能です。UseExistingKek ドロップダウンで `kek` を選択した場合は、keyEncryptionKeyURL 値を入力する必要があります。 |
| ​volumeType | ​暗号化操作を実行するボリュームの種類。有効な値は "OS"、"Data"、"All" です。 |
| sequenceVersion | BitLocker 操作のシーケンス バージョン。ディスク暗号化操作が同じ VM で実行されるたびに、このバージョン番号をインクリメントします。 |
| ​vmName | ​暗号化操作を実行する VM の名前


**注:** KeyEncryptionKeyURL は省略可能なパラメーターです。独自の KEK を使用して、Key Vault でデータ暗号化キー (BitLocker 暗号化シークレット) の保護を強化することができます。

#### PowerShell コマンドレットの使用

PS コマンドレットによる Azure ディスク暗号化を使用して暗号化を有効にする方法の詳細については、**Azure PowerShell を使用する Azure ディスク暗号化の探索**に関するブログ投稿の[パート 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) と[パート 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) を参照してください。

#### CLI コマンドの使用

CLI コマンドを使用して、Azure で既存/実行中の IaaS Windows VM での暗号化を有効にするには、次の手順に従います。

1. 以下のように、Key Vault でアクセス ポリシーを設定します。
	- "EnabledForDiskEncryption" フラグを設定します: “azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
	- KeyVault へのシークレットの書き込み権限を Azure AD アプリに設定します: “azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]”
2. 既存/実行中の VM で暗号化を有効にするために、次のように入力します: *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. 暗号化状態を取得します: *"azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json"*
4. 顧客が暗号化した VHD からの新しい VM での暗号化を有効にするには、“azure vm create” コマンドで以下のパラメーターを使用します。
	- disk-encryption-key-vault-id <disk-encryption-key-vault-id>
	- disk-encryption-key-url <disk-encryption-key-url>
	- key-encryption-key-vault-id <key-encryption-key-vault-id>
	- key-encryption-key-url <key-encryption-key-url>


### Azure で既存または実行中の IaaS Linux VM での暗号化を有効にする

[ここ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)で公開されている Resource Manager テンプレートを使用して、Azure で既存または実行中の IaaS Linux VM でのディスク暗号化を有効にすることができます。Azure クイック スタート テンプレートの [Azure にデプロイ] ボタンをクリックし、パラメーター ブレードに暗号化構成を入力して [OK] をクリックします。サブスクリプション、リソース グループ、リソース グループの場所、法律条項および契約を選択し、[作成] ボタンをクリックして既存/実行中の IaaS VM での暗号化を有効にします。

次の表で、Azure AD クライアント ID を使用する既存/実行中の VM シナリオで使用される Resource Manager テンプレートのパラメーターの詳細を参照できます。

| パラメーター | 説明|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​AADClientID | ​Key Vault へのシークレットの書き込み権限を持つ Azure AD アプリのクライアント ID |
| AADClientSecret | ​Key Vault へのシークレットの書き込み権限を持つ Azure AD アプリのクライアント シークレット |
| KeyVaultName | BitLocker キーのアップロード先となる Key Vault の名前。コマンドレット (Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>).Vaultname を使用して取得できます。 |
| ​ keyEncryptionKeyURL | 生成された BitLocker キーの暗号化に使用されるキー暗号化キーの URL。UseExistingKek ドロップダウンで "nokek" を選択した場合は省略可能です。UseExistingKek ドロップダウンで "kek" を選択した場合は、keyEncryptionKeyURL 値を入力する必要があります。 |
| ​volumeType | ​暗号化操作を実行するボリュームの種類。サポートされている有効な値は、"OS"/"All" (RHEL 7.2、CentOS 7.2、および Ubuntu 16.04) と "Data" （それ以外のすべてのディストリビューション）です。 |
| sequenceVersion | BitLocker 操作のシーケンス バージョン。ディスク暗号化操作が同じ VM で実行されるたびに、このバージョン番号をインクリメントします。 |
| ​vmName | ​暗号化操作を実行する VM の名前
| passPhrase | データ暗号化キーとして強力なパスフレーズを入力します。 |                                                                                                                                                                                                                                                      

**注:** KeyEncryptionKeyURL は省略可能なパラメーターです。独自の KEK を使用して、Key Vault でデータ暗号化キー (パスフレーズ シークレット) の保護を強化することができます。

#### CLI コマンド

[ここ](../xplat-cli-install.md)からインストールした CLI コマンドを使用して、顧客が暗号化した VHD でディスク暗号化を有効にすることができます。CLI コマンドを使用して、Azure で既存/実行中の IaaS Linux VM での暗号化を有効にするには、次の手順に従います。

1. 以下のように、Key Vault でアクセス ポリシーを設定します。
	- "EnabledForDiskEncryption" フラグを設定します: “azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true”
	- KeyVault へのシークレットの書き込み権限を Azure AD アプリに設定します: “azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys ["all"] --perms-to-secrets ["all"]”
2. 既存/実行中の VM で暗号化を有効にするために、次のように入力します: *azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId>*
3. 暗号化状態を取得します: “azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json”
4. 顧客が暗号化した VHD からの新しい VM での暗号化を有効にするには、“azure vm create” コマンドで以下のパラメーターを使用します。
	- *disk-encryption-key-vault-id <disk-encryption-key-vault-id>*
	- *disk-encryption-key-url <disk-encryption-key-url>*
	- *key-encryption-key-vault-id <key-encryption-key-vault-id>*
	- *key-encryption-key-url <key-encryption-key-url>*

### 暗号化された IaaS VM の暗号化状態の取得

暗号化状態は、Azure Resource Manager ポータル、[PowerShell コマンドレット](https://msdn.microsoft.com/library/azure/mt622700.aspx)、または CLI コマンドを使用して取得できます。以下のセクションでは、Azure ポータルと CLI コマンドを使用して、暗号化状態を取得する方法を説明します。

#### 暗号化された Windows VM の暗号化状態を、Azure Resource Manager ポータルを使用して取得する

Azure Resource Manager ポータルから IaaS VM の暗号化状態を取得できます。Azure ポータル (https://portal.azure.com/) にログオンし、左側のメニューにある仮想マシン リンクをクリックして、サブスクリプションの仮想マシンの概要ビューを表示します。仮想マシン ビューは、サブスクリプション ドロップダウン リストからサブスクリプション名を選択してフィルター処理できます。仮想マシン ページのメニューの最上部にある列をクリックします。選択列ブレードからディスク暗号化の列を選択し、[更新] をクリックします。次の図に示すように、各 VM の [有効] または [無効] の暗号化状態を表示するディスク暗号化列が表示されます。

![Azure での Microsoft マルウェア対策](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### 暗号化された (Windows/Linux) IaaS VM の暗号化状態を、ディスク暗号化 PS コマンドレットを使用して取得する
ディスク暗号化 PS コマンドレット “Get-AzureRmVMDiskEncryptionStatus” から IaaS VM の暗号化状態を取得できます。VM の暗号化設定を取得するには、Azure PowerShell セッションで以下のように入力します。

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName
    
    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

Get-AzureRmVMDiskEncryptionStatus の出力から、暗号化キーの URL を確認できます。
    
    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMNam
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey
    
    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

OSVolumeEncrypted と DataVolumesEncrypted の設定値は "Encrypted" に設定されています。これは、両方のボリュームが Azure Disk Encryption を使用して暗号化されることを示します。PS コマンドレットによる Azure ディスク暗号化を使用して暗号化を有効にする方法の詳細については、**Azure PowerShell を使用する Azure ディスク暗号化の探索**に関するブログ投稿の[パート 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) と[パート 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) を参照してください。

**注**: Linux VM では、`Get-AzureRmVMDiskEncryptionStatus` コマンドレットによって暗号化状態が報告されるまで、3 ～ 4 分かかります。

#### ディスク暗号化 CLI コマンドから IaaS VM の暗号化状態を取得する

ディスク暗号化 CLI コマンド *azure vm show-disk-encryption-status* から、IaaS VM の暗号化状態を取得できます。VM の暗号化設定を取得するには、Azure CLI セッションで以下のように入力します。

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### 実行中の Windows IaaS VM での暗号化を無効にする

Azure Disk Encryption Resource Manager テンプレートまたは PS コマンドレットを使用して暗号化解除の構成を指定することで、実行中の Windows または Linux IaaS VM でのディスクの暗号化を無効にできます。


##### Windows VM

暗号化を無効にする手順によって、Windows IaaS VM で実行されている OS またはデータ ボリューム、もしくはその両方の暗号化が無効になります。OS ボリュームの暗号化を無効にして、データ ボリュームを暗号化されたままにすることはできません。暗号化を無効にする手順を実行すると、Azure クラシック デプロイ モデルによって VM サービス モデルが更新され、Windows IaaS VM は暗号化解除済みとしてマークされます。VM の内容は保存時に暗号化されなくなります。暗号化を無効にしても、顧客の Key Vault や暗号化キー マテリアル (Windows システムの場合は BitLocker 暗号化キー、Linux の場合はパスフレーズ) は削除されません。

##### Linux VM

暗号化を無効にする手順によって、Linux IaaS VM で実行されているデータ ボリュームの暗号化が無効になります。

**注**: Linux VM では、OS ディスクの暗号化を無効にすることは許可されていません。

##### Resource Manager テンプレートを使用して、Azure で既存/実行中の IaaS VM での暗号化を無効にする

[ここ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)で公開されている ARM テンプレートを使用して、実行中の Windows IaaS VM でのディスク暗号化を無効にすることができます。Azure クイック スタート テンプレートの [Azure にデプロイ] ボタンをクリックし、パラメーター ブレードに暗号化解除の構成を入力して [OK] をクリックします。サブスクリプション、リソース グループ、リソース グループの場所、法律条項および契約を選択し、[作成] ボタンをクリックして新しい IaaS VM での暗号化を有効にします。

Linux VM では、[この](https://aka.ms/decrypt-linuxvm) テンプレートを使用して、暗号化を無効にできます。

実行中の IaaS VM の暗号化を無効にする Resource Manager テンプレートのパラメーターの詳細:

| ​vmName | ​暗号化操作を実行する VM の名前 |
|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ​volumeType | ​暗号化解除操作を実行するボリュームの種類。有効な値は "OS"、"Data"、"All" です。**注:** "Data" ボリュームの暗号化を無効にせずに、実行中の Windows IaaS VM の OS (ブート) ボリュームの暗号化を無効にすることはできません。**注**: Linux VM では、OS ディスクの暗号化を無効にすることは許可されていません。 |
| sequenceVersion | BitLocker 操作のシーケンス バージョン。ディスク暗号化解除操作が同じ VM で実行されるたびに、このバージョン番号をインクリメントします。 |

##### PS コマンドレットを使用して、Azure で既存/実行中の IaaS VM での暗号化を無効にする

PS コマンドレットを使用して無効化する場合、[Disable-AzureRmVMDiskEncryption](https://msdn.microsoft.com/library/azure/mt715776.aspx) コマンドレットで、サービスとしてのインフラストラクチャ (IaaS) 仮想マシンの暗号化を無効にします。このコマンドレットは、Windows VM と Linux VM の両方をサポートします。このコマンドレットにより、暗号化を無効にする拡張機能が仮想マシンにインストールされます。Name パラメーターが指定されていない場合、拡張機能は既定の名前 "AzureDiskEncryption for Windows VMs" で作成されます。

Linux VM では、"AzureDiskEncryptionForLinux" 拡張機能が使用されます。

**注**: このコマンドレットにより、仮想マシンが再起動されます。

## 付録

### サブスクリプションへの接続

先に進む前に、必ず、このドキュメントの「*前提条件*」セクションを確認してください。前提条件がすべて満たされていることを確認したら、以下の手順に従ってサブスクリプションに接続します。

1\. Azure PowerShell セッションを開始し、次のコマンドを使用して Azure アカウントにサインインします。

    Login-AzureRmAccount

2\. 複数のサブスクリプションがあり、使用する特定のサブスクリプションを指定する場合は、次のように入力して自分のアカウントのサブスクリプションを表示します。

    Get-AzureRmSubscription

3\. 使用するサブスクリプションを指定するには、次のように入力します。

    Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>

4\. 構成されているサブスクリプションが正しいことを確認するには、次のように入力します。

    Get-AzureRmSubscription

5\. Azure Disk Encryption コマンドレットがインストールされていることを確認するには、次のように入力します。

    Get-command *diskencryption*

6\. Azure Disk Encryption PowerShell インストールを確認する以下の出力が示されます。

    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                               	      Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     

### 事前に暗号化された Windows VHD の準備
以下のセクションは、Azure IaaS で暗号化された VHD としてデプロイする場合に事前に暗号化された Windows VHD を準備するために必要です。この手順は、Hyper-V または Azure で新しい Windows VM (vhd) を準備し、ブートするために使用します。

#### グループ ポリシーを更新して非 TPM で OS を保護できるようにする
Local Computer Policy \\Computer Configuration\\Administrative Templates\\Windows Components の下にある、BitLocker ドライブ暗号化という BitLocker グループ ポリシー設定を構成する必要があります。以下の図に示すように、*[オペレーティング システムのドライブ]、[スタートアップ時に追加の認証を要求する]、[互換性のある TPM が装備されていない BitLocker を許可する]* の順に選択して、この設定を変更します。

![Azure での Microsoft マルウェア対策](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### BitLocker 機能コンポーネントのインストール
Windows Server 2012 以降の場合は、次のコマンドを使用します。

    dism /online /Enable-Feature /all /FeatureName:Bitlocker /quiet /norestart

Windows Server 2008 R2 の場合は、次のコマンドを使用します。

    ServerManagerCmd -install BitLockers

#### `bdehdcfg` を使用して BitLocker の OS ボリュームを準備する

OS のパーティションを圧縮して、BitLocker のコンピューターを準備するには、次のコマンドを実行します。

    bdehdcfg -target c: shrink -quiet

#### BitLocker を使用して OS ボリュームを保護する
[`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) コマンドで、外部キー保護機能を使用するブート ボリュームでの暗号化を有効にし、外部ドライブまたはボリュームに外部キー (.bek ファイル) を配置します。暗号化は、次回のリブート後にシステム/ブート ボリュームで有効になります。

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

**注:** BitLocker を使用して外部キーを取得する場合は、別個のデータ/リソース vhd を使用して VM を準備する必要があります。

#### 実行中の Linux VM での OS ドライブの暗号化

実行中の Linux VM での OS のドライブの暗号化は、次のディストリビューションでサポートされます。

- RHEL 7.2
- CentOS 7.2
- Ubuntu 16.04

OS ディスクを暗号化するための前提条件:

- Azure Resource Manager ポータルで Azure のギャラリー イメージから VM を作成する必要があります。
- 少なくとも 4 GB の RAM を持つAzure VM (推奨するサイズは 7 GB)。
- (RHEL および CentOS) VM で SELinux を [無効](https://access.redhat.com/documentation/ja-JP/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux)にする必要があります。SELinux を無効にした後、VM を少なくとも 1 回再起動する必要があります。


##### 手順

1\. 上記に示したディストリビューションのいずれかを使用して VM を作成します。

CentOS 7.2 では、OS ディスクの暗号化は、特別なイメージを使用してサポートされます。このイメージを使用するには、VM を作成するときに、SKU として "7.2n" を指定します。

    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"

2\. ニーズに応じて VM を構成します。すべてのドライブ (OS およびデータ) を暗号化する場合は、/etc/fstab にデータ ドライブを指定してマウントできるようにする必要があります。

> [AZURE.NOTE] /etc/fstab にデータ ドライブを指定するには、ブロック デバイス名 (/dev/sdb1 など) ではなく、UUID=... を使用する必要があります。暗号化中に、ドライブの順序は、VM 上で変更されます。VM がブロック デバイスの特定の順序に依存している場合、暗号化後のマウントは失敗します。

3\. SSH セッションをログアウトします。

4\. OS を暗号化するには、[暗号化を有効にする](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure)ときに、volumeType として "All" または "OS" を指定します。

> [AZURE.NOTE] `systemd` サービスとして実行されているすべてのユーザー スペースのプロセスは、`SIGKILL` によって強制終了されます。VM を再起動する必要があります。実行中の VM で OS ディスクの暗号化を有効にする場合は、VM のダウンタイムを予定しておいてください。

5\. [次のセクション](#monitoring-os-encryption-progress)の指示に従って、暗号化の進行状況を定期的に監視します。

6\. Get-AzureRmVmDiskEncryptionStatus で "VMRestartPending" が表示されたら、ログオンするか、ポータル/PowerShell/CLI を使用して VM を再起動します。

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName
    
    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, please reboot the VM

*再起動する前*に、VM の[ブート診断](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) を保存することをお勧めします。

#### OS 暗号化の進行状況の監視

OS 暗号化の進行状況を監視するには、次の 3 つの方法があります。

1\. Get-AzureRmVmDiskEncryptionStatus コマンドレットを使用して ProgressMessage フィールドを調べる。
 
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started

VM が "OS disk encryption started (OS ディスクの暗号化が開始されました)" 状態に達するまで、Premium ストレージを使用する VM でおよそ 40 ～ 50 分かかります。

WALinuxAgent での [問題 #388](https://github.com/Azure/WALinuxAgent/issues/388) により、一部のディストリビューションでは、`OsVolumeEncrypted` と `DataVolumesEncrypted` は `Unknown` と表示されます。WALinuxAgent バージョン 2.1.5 以降では、この問題は自動的に修正されます。出力に `Unknown` が含まれている場合は、Azure Resource Viewer を使用して、ディスクの暗号化状態を確認できます。

[Azure Resource Viewer](https://resources.azure.com/) に移動し、左側の選択パネルで次の階層を展開します。

~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

InstanceView で、画面を下にスクロールして、ドライブの暗号化の状態を表示します。

![VM インスタンス ビュー](./media/azure-security-disk-encryption/vm-instanceview.png)

2\. [ブート診断](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)を調べる。ADE 拡張機能のメッセージには、頭に `[AzureDiskEncryption]` が付きます。

3\. SSH を使用して VM にログオンし、拡張機能ログを取得する。

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

OS 暗号化の進行中は、VM にログオンしないでください。したがって、このログは、他の 2 つの方法が失敗した場合のみコピーしてください。

#### 事前に暗号化された Linux VHD の準備

##### Ubuntu 16

###### ディストリビューションのインストール中に暗号化を構成します。

1\. ディスクをパーティション分割するときに [Configure encrypted volumes] を選択します。

![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2\. 暗号化してはならない独立したブート ドライブを作成します。ルート ドライブを暗号化します。

![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3\. パスフレーズを指定します。これは、KeyVault にアップロードするパスフレーズです。

![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4\. パーティション分割を終了します。

![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5\. VM を起動すると、パスフレーズの入力を求められます。手順 3 で指定したパスフレーズを使用します。

![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6\. [こちらの手順](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/)に従って、VM を Azure にアップロードするための準備をします。最後の手順 (VM のプロビジョニング解除) はまだ実行しないでください。

###### Azure で使用する暗号化を構成する

1\. 以下のスクリプトのコンテンツを使用して、/usr/local/sbin/azure\_crypt\_key.sh の下にファイルを作成します。KeyFileName に注意してください。これは、Azure によって書き込まれるパスフレーズ ファイル名です。

    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do
        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi


2\. */etc/crypttab* の crypt 構成を変更します。次のようになります。

    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh

3\. Windows で *azure\_crypt\_key.sh* を編集し、Linux にコピーする場合は、必ず、*dos2unix /usr/local/sbin/azure\_crypt\_key.sh* を実行してください。

4\. 実行可能アクセス許可をスクリプトに追加します。

    chmod +x /usr/local/sbin/azure_crypt_key.sh

4\. */etc/initramfs-tools/modules* を編集し、次の行を追加します。

    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1

5\. `update-initramfs -u -k all` を実行して initramfs を更新して、`keyscript` を有効にします。6. 次に、VM をプロビジョニング解除できます。

![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

7\. 次の手順に進み、Azure に [VHD をアップロードします](#upload-encrypted-vhd-to-an-azure-storage-account)。

##### openSUSE 13.2

###### ディストリビューションのインストール中に暗号化を構成します。

1\. ディスクをパーティション分割するときに [Encrypt Volume Group] を選択します。パスフレーズを指定します。これは、KeyVault にアップロードするパスフレーズです。

![openSUSE 13.2 のセットアップ](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2\. パスフレーズを使用して VM を起動します。

![openSUSE 13.2 のセットアップ](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3\. [こちらの手順](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131)に従って、VM を Azure にアップロードするための準備をします。最後の手順 (VM のプロビジョニング解除) はまだ実行しないでください。

###### Azure で使用する暗号化を構成する

1\. /etc/dracut.conf を編集し、次の行を追加します。

    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"

2\. ファイル “/usr/lib/dracut/modules.d/90crypt/module-setup.sh” の末尾付近にある以下の行をコメント アウトします。

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3\. “/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh” ファイルの先頭に次の行を追加し、

    DRACUT_SYSTEMD=0

出現する次のすべての行を

    if [ -z "$DRACUT_SYSTEMD" ]; then

次のように変更します。

    if [ 1 ]; then

4\. /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh を編集し、これを “# Open LUKS device” の後に追加します。

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done


5\. “/usr/sbin/dracut -f -v” を実行して initrd を更新します。

6\. 次に、VM をプロビジョニング解除し、Azure に [VHD をアップロード](#upload-encrypted-vhd-to-an-azure-storage-account)できます。

##### CentOS 7

###### ディストリビューションのインストール中に暗号化を構成します。

1\. ディスクをパーティション分割するときに [Encrypt my data] を選択します。

![CentOS 7 のセットアップ](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2\. ルート パーティションで [Encrypt] が選択されていることを確認します。

![CentOS 7 のセットアップ](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3\. パスフレーズを指定します。これは、KeyVault にアップロードするパスフレーズです。

![CentOS 7 のセットアップ](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4\. VM を起動すると、パスフレーズの入力を求められます。手順 3 で指定したパスフレーズを使用します。

![CentOS 7 のセットアップ](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5\. [こちらの手順](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70)に従って、VM を Azure にアップロードするための準備をします。最後の手順 (VM のプロビジョニング解除) はまだ実行しないでください。

6\. 次に、VM をプロビジョニング解除し、Azure に [VHD をアップロード](#upload-encrypted-vhd-to-an-azure-storage-account)できます。

###### Azure で使用する暗号化を構成する

1\. /etc/dracut.conf を編集し、次の行を追加します。

    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"

2\. ファイル “/usr/lib/dracut/modules.d/90crypt/module-setup.sh” の末尾付近にある以下の行をコメント アウトします。

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3\. “/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh” ファイルの先頭に次の行を追加し、

    DRACUT_SYSTEMD=0

出現する次のすべての行を

    if [ -z "$DRACUT_SYSTEMD" ]; then

to

    if [ 1 ]; then

4\. /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh を編集し、これを “# Open LUKS device” の後に追加します。

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done


5\. “/usr/sbin/dracut -f -v” を実行して initrd を更新します。

![CentOS 7 のセットアップ](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### 暗号化された VHD を Azure ストレージ アカウントにアップロードする
BitLocker 暗号化または DM-Crypt 暗号化を有効にしたら、ローカルで暗号化された VHD をストレージ アカウントにアップロードする必要があります。

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### Key Vault に事前に暗号化された VM のディスク暗号化シークレットをアップロードする
前に取得したディスク暗号化シークレットを、Key Vault にシークレットとしてアップロードする必要があります。Key Vault では、ディスク暗号化だけではなく、AAD クライアントに対するアクセス許可も有効になっている必要があります。


    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $KeyVault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### KEK で暗号化されないディスク暗号化シークレット
[Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) を使用して、Key Vault でシークレットをプロビジョニングします。Windows 仮想マシンの場合は、bek ファイルが base64 文字列としてエンコードされてから、Set-AzureKeyVaultSecret コマンドレットを使用して Key Vault にアップロードされます。Linux の場合は、パスフレーズが base64 文字列としてエンコードされてから、Key Vault にアップロードされます。さらに、Key Vault でのシークレットの作成時に、以下のタグが設定されていることを確認してください。

    # This is the passphrase that was provided for encryption during distro install
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

`$secretUrl` は、[KEK を使用せずに OS ディスクをアタッチする](#without-using-a-kek)手順で使用します。

#### KEK で暗号化されるディスク暗号化シークレット

シークレットは、Key Vault にアップロードする前に、必要に応じてキー暗号化キーで暗号化できます。ラップ [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) で、まず、キー暗号化キーを使用してシークレットを暗号化します。このラップ操作の出力は base64 URL エンコードされた文字列であり、[Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) コマンドレットを使用してシークレットとしてアップロードされます。

    # This is the passphrase that was provided for encryption during distro install
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

`$KeyEncryptionKey` と `$secretUrl` は、[KEK を使用して OS ディスクをアタッチする](#using-a-kek)手順で使用します。

### OS ディスクをアタッチするときにシークレット URL を指定する

#### KEK を使用しない

OS ディスクをアタッチするときに、`$secretUrl` を渡す必要があります。この URL は、[「KEK で暗号化されないディスク暗号化シークレット」](#disk-encryption-secret-not-encrypted-with-a-kek)セクションで生成されました。

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### KEK を使用する

OS ディスクをアタッチするときに、`$KeyEncryptionKey` と `$secretUrl` を渡す必要があります。この URL は、「[KEK で暗号化されるディスク暗号化シークレット](#disk-encryption-secret-encrypted-with-a-kek)」セクションで生成されました。

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## このガイドのダウンロード
このガイドは [TechNet ギャラリー](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)からダウンロードできます。


## BLOB の詳細
[Azure PowerShell を使用する Azure ディスク暗号化の探索](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)

[Azure PowerShell を使用する Azure ディスク暗号化の探索 - パート 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)

<!---HONumber=AcomDC_0928_2016-->