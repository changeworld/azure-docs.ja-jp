<properties 
	pageTitle="Azure Cloud Services - サービス定義とサービス構成 - XML 証明書" 
	description="サービス定義ファイルと構成ファイル内に証明書を構成する方法について説明します。" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/24/2015"
	ms.author="adegeo"/>



# 証明書用のサービス定義と構成を構成する

Web または Worker ロールを実行している Virtual Machines は、それらに関連付けられた証明書を持つことができます。ポータルに証明書をアップロードした後、証明書用のサービス定義 (.csdef) ファイルとサービス構成 (.cscfg) ファイルを構成する必要があります。

Virtual Machines は、インストール後に証明書の秘密キーにアクセスできます。このため、引き上げられたアクセス許可でプロセスへのアクセスを制限できます。

## サービス定義の例

サービス定義内に定義された証明書の例を次に示します。

```xml
<ServiceDefinition name="WindowsAzureProject4" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="MyWokerRole"> <!-- or <WebRole name="MyWebRole" vmsize="Small"> -->
    <ConfigurationSettings>
      ...
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="MySSLCert" storeLocation="LocalMachine" storeName="My" permissionLevel="elevated" />
    </Certificates>
  </WorkerRole>
</ServiceDefinition>
```

### アクセス許可
アクセス許可 (`permisionLevel`属性) は、次のいずれかに設定できます。

| アクセス許可の値 | 説明 |
| ----------------  | ----------- |
| limitedOrElevated | **(既定)** すべてのロール プロセスが秘密キーにアクセスできます。 |
| elevated | 引き上げられたプロセスだけが秘密キーにアクセスできます。|

## サービス構成の例

サービス構成内に定義された証明書の例を次に示します。

```xml
<Role name="MyWokerRole">
...
    <Certificates>
        <Certificate name="MySSLCert" 
            thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
            thumbprintAlgorithm="sha1" />
    </Certificates>
...
</Role>
```

対応する `name` 属性に注意してください。

## 次のステップ
[サービス定義 XML](https://msdn.microsoft.com/library/azure/ee758711.aspx) スキーマと [サービス構成 XML](https://msdn.microsoft.com/library/azure/ee758710.aspx) スキーマを確認します。

<!---HONumber=July15_HO5-->