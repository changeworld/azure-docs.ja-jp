<properties 
pageTitle="Azure Cloud Services のロールでのリモート デスクトップ接続の有効化"
	description="Azure クラウド サービス アプリケーションを構成してリモート デスクトップ接続を許可する方法"
	services="cloud-services"
	documentationCenter=""
	authors="sbtron"
	manager="timlt"
	editor=""/>
<tags 
ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2015"
	ms.author="saurabh"/>

# Azure Cloud Services のロールでのリモート デスクトップ接続の有効化

>[AZURE.SELECTOR]
- [Azure Portal](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](https://msdn.microsoft.com/library/gg443832.aspx)


リモート デスクトップを使用して、Azure で実行されているロールのデスクトップにアクセスできます。リモート デスクトップ接続を使用すると、アプリケーションの実行中にそのアプリケーションの問題のトラブルシューティングや診断を行うことができます。

サービス定義にリモート デスクトップ モジュールを含めることで開発時にロールのリモート デスクトップ接続を有効にするか、リモート デスクトップ拡張機能を使用してリモート デスクトップを有効にすることができます。アプリケーションのデプロイ後でもアプリケーションを再デプロイすることなくリモート デスクトップを有効にできるため、リモート デスクトップ拡張機能を使用する方法が推奨されます。


## ポータルからリモート デスクトップを構成する
ポータルでは、アプリケーションのデプロイ後でもリモート デスクトップを有効化できるように、リモート デスクトップ拡張機能の方法を使用します。クラウド サービスの **[構成]** ページでは、リモート デスクトップの有効化、仮想マシンへの接続に使用するローカル管理者アカウントや認証に使用する証明書の変更、および有効期限の設定を行うことができます。


1. **[クラウド サービス]** をクリックし、クラウド サービスの名前をクリックして、**[構成]** をクリックします。

2. **[リモート]** をクリックします。
    
    ![クラウド サービス リモート](./media/cloud-services-role-enable-remote-desktop/CloudServices_Remote.png)
    
    > [AZURE.WARNING]初めてリモート デスクトップを有効にして [OK] (チェックマーク) をクリックしたときは、すべてのロール インスタンスが再起動されます。再起動を防止するには、パスワードの暗号化に使用した証明書がロールにインストールされている必要があります。再起動を防止するには、[クラウド サービスの証明書をアップロード](cloud-services-how-to-create-deploy/#how-to-upload-a-certificate-for-a-cloud-service)して、このダイアログに戻ります。
    

3. **[ロール]** で、更新するロールを選択します。すべてのロールの場合は、**[すべて]** をクリックします。

4. 必要に応じて次の変更を行います。
    
    - リモート デスクトップを有効にするには、**[リモート デスクトップを有効にする]** チェック ボックスをオンにします。リモート デスクトップを無効にするには、チェック ボックスをオフにします。
    
    - ロール インスタンスへのリモート デスクトップ接続で使用するアカウントを作成します。
    
    - 既存のアカウントのパスワードを更新します。
    
    - 認証に使用するために、アップロードした証明書を選択するか (**[証明書]** ページの **[アップロード]** を使用して証明書をアップロードします)、新しい証明書を作成します。
    
    - リモート デスクトップ構成の有効期限を変更します。

5. 構成の更新が完了したら、**[OK]** (チェックマーク) をクリックします。


## ロール インスタンスへのリモート接続
ロールでリモート デスクトップが有効化されると、各種ツールを使用してロール インスタンスにリモート接続できるようになります。

ポータルからロール インスタンスに接続するには:
    
  1.   **[インスタンス]** をクリックして、**[インスタンス]** ページを開きます。
  2.   リモート デスクトップが構成されたロール インスタンスを選択します。
  3.   **[接続]** をクリックし、指示に従ってデスクトップを開きます。 
  4.   **[開く]**、**[接続]** の順にクリックして、リモート デスクトップ接続を開始します。 


### Visual Studio を使用してロール インスタンスにリモート接続する

Visual Studio のサーバー エクスプローラーで次の操作を行います。

1. **Azure\\Cloud Services\\[クラウド サービス名]** ノードを展開します。
2. **[ステージング]** か **[運用]** のいずれかを展開します。
3. 個々のロールを展開します。
4. ロール インスタンスの 1 つを右クリックして **[リモート デスクトップを使用して接続]** をクリックし、ユーザー名とパスワードを入力します。 

![サーバー エクスプローラーとリモート デスクトップ](./media/cloud-services-role-enable-remote-desktop/ServerExplorer_RemoteDesktop.png)


### PowerShell を使用して、RDP ファイルを取得する
[Get-azureremotedesktopfile](https://msdn.microsoft.com/library/azure/dn495261.aspx) コマンドレットを使用して RDP ファイルを取得できます。リモート デスクトップ接続で RDP ファイルを使用して、クラウド サービスにアクセスできます。

### プログラムを使用して、サービス管理 REST API から RDP ファイルをダウンロードする
[Download RDP File](https://msdn.microsoft.com/library/jj157183.aspx) REST 操作を使用して、RDP ファイルをダウンロードできます。



## サービス定義ファイルでリモート デスクトップを構成するには

この方法を使用すると、開発時にアプリケーションのリモート デスクトップを有効にすることができます。この方法では、サービス構成ファイルに暗号化パスワードを保存する必要があり、リモート デスクトップ構成を更新した場合はアプリケーションを再デプロイする必要があります。こうした欠点を回避するには、前述のリモート デスクトップ拡張機能ベースの方法を使用してください。

サービス定義ファイルの方法では、Visual Studio を使用して[リモート デスクトップ接続を有効にできます](https://msdn.microsoft.com/library/gg443832.aspx)。次の手順では、リモート デスクトップを有効にするためにサービス モデル ファイルに加える必要のある変更について説明します。こうした変更は、発行時に Visual Studio で自動的に行われます。

### サービス モデルでの接続をセットアップする 
**Imports** 要素を使用して、[ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) ファイルに **RemoteAccess** モジュール と **RemoteForwarder** モジュールをインポートします。

サービス定義ファイルは、`<Imports>` 要素が追加された次の例のようになっている必要があります。

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```
[ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) ファイルは次の例のようになっている必要があります (`<ConfigurationSettings>` 要素と `<Certificates>` 要素に注意してください)。指定した証明書は、[クラウド サービスにアップロードする](cloud-services-how-to-create-deploy/#how-to-upload-a-certificate-for-a-cloud-service)必要があります。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```


## その他のリソース

[クラウド サービスの構成方法](cloud-services-how-to-configure.md)

<!---HONumber=September15_HO1-->