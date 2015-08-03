<properties 
pageTitle="Azure Cloud Services のロールにリモート デスクトップ接続をセットアップする" 
description="Azure クラウド サービス アプリケーションを構成してリモート デスクトップ接続を許可する方法" 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="07/06/2015" 
ms.author="adegeo"/>

# Azure ロールのリモート デスクトップ接続をセットアップする
アプリケーションを実行しているクラウド サービスの作成後は、そのアプリケーションのロール インスタンスにリモートでアクセスして、設定を構成したり、トラブルシューティングを実行できます。クラウド サービスがリモート デスクトップ用に構成されている必要があります。

* リモート デスクトップ認証を有効にするには、証明書が必要ですか。 証明書を[作成](cloud-services-certs-create.md)して、以下のように構成します。
* クラウド サービス用のリモート デスクトップのセットアップが既にありますか。 クラウド サービスに[接続](#remote-into-role-instances)します。

## Web ロールか Worker ロールのリモート デスクトップ接続をセットアップする
Web ロールか Worker ロールのリモート デスクトップ接続を有効にするには、アプリケーションのサービス モデルでの接続をセットアップするか、インスタンスの実行後に Azure の管理ポータルを使用して接続を設定します。

### サービス モデルでの接続をセットアップする
**インポート**要素は、**RemoteAccess** モジュールと **RemoteForwarder** モジュールをサービス モデルにインポートサービス定義ファイルに追加する必要があります。Visual Studio を使用して Azure プロジェクトを作成する場合は、サービス モデルのファイルが作成されます。

このサービス モデルは、[ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) ファイルと [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) ファイルから構成されます。クラウド サービスのアプリケーションがデプロイメント用に準備されると、定義ファイルはロール バイナリとパッケージ化されます。ServiceConfiguration.cscfg ファイルは、アプリケーション パッケージを使用してデプロイされ、Azure は、アプリケーションの実行方法を決定するために使用します。

プロジェクトの作成後に Visual Studio を使用して、[リモート デスクトップ接続を有効にすることができます](https://msdn.microsoft.com/library/gg443832.aspx)。

接続の構成後は、サービス定義ファイルは、`<Imports>` 要素が追加された次の例のようになっている必要があります。

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

サービス構成ファイルは、接続の設定時に指定した値の次の例のようになっている必要があります。`<ConfigurationSettings>` 要素と `<Certificates>` 要素に注意してください。

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

アプリケーションを[パッケージ化し](cloud-services-model-and-package.md#cspkg)、[発行](cloud-services-how-to-create-deploy-portal.md)したら、**[すべてのロールのリモート デスクトップを有効にする]** チェック ボックスをオンにします。[この](https://msdn.microsoft.com/library/ff683672.aspx)記事では、Visual Studio とクラウド サービスの使用に関連する一般的なタスクのリストを記載しています。

### インスタンスを実行する接続をセットアップする
クラウド サービスの [構成] ページで、リモート デスクトップ接続の設定を有効化したり、変更できます。詳細については、「[ロール インスタンスへのリモート アクセスを構成する](cloud-services-how-to-configure.md)」をご覧ください。




## ロール インスタンスへのリモート接続
Web ロールか Worker ロールのインスタンスにアクセスするには、リモート デスクトップ プロトコル (RDP) ファイルを使用する必要があります。管理ポータルからファイルをダウンロードするか、プログラムでファイルを取得できます。

### 管理ポータルで RDP ファイルをダウンロードする

次の手順を使用して、管理ポータルから RDP ファイルを取得し、ファイルを使用してリモート デスクトップ接続でインスタンスに接続します。

1.  [インスタンス] ページで、インスタンスを選択し、コマンド バーの **[接続]** をクリックします。
2.  **[保存]** をクリックして、ローカル コンピューターにリモート デスクトップ プロトコル ファイルを保存します。
3.  **[リモート デスクトップ接続]** を開いて、**[オプションの表示]**、**[開く]** の順にクリックします。
4.  RDP ファイルを保存した場所を参照し、ファイルを選択し、**[開く]**、**[接続]** の順にクリックします。手順に従って、接続を完了します。

### PowerShell を使用して、RDP ファイルを取得する
[Get-azureremotedesktopfile](https://msdn.microsoft.com/library/azure/dn495261.aspx) コマンドレットを使用して RDP ファイルを取得できます。

### Visual Studio を使用して、RDP ファイルをダウンロードする
Visual Studio でサーバー エクスプローラーを使用して、リモート デスクトップ接続を作成できます。

1.  サーバー エクスプローラーで、**Azure\Cloud services \ [cloud service name]** ノードを展開します。
2.  **[ステージング]** か **[運用]** のいずれかを展開します。
3.  個々のロールを展開します。
4.  ロール インスタンスの 1 つを右クリックして **[リモート デスクトップを使用して接続]** をクリックして、ユーザー名とパスワードを入力します。

### プログラムを使用して、サービス管理 REST API から RDP ファイルをダウンロードする
[RDP ファイルのダウンロード](https://msdn.microsoft.com/library/jj157183.aspx) REST 操作を 使用して RDP ファイルをダウンロードします。リモート デスクトップ接続で RDP ファイルを使用して、クラウド サービスにアクセスできます。

## 次のステップ
アプリケーションを[パッケージ化](cloud-services-model-and-package.md)するか、[アップロード (デプロイ)](cloud-services-how-to-create-deploy-portal.md) する必要があります。

<!---HONumber=July15_HO4-->