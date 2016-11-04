---
title: Cloud Services ロール構成 XPath チート シート | Microsoft Docs
description: クラウド サービス ロール構成で設定を環境変数として公開するために使用できるさまざまな XPath 設定。
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''

ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2016
ms.author: adegeo

---
# XPath で環境変数としてロール構成設定を公開する
クラウド サービス worker ロールまたは Web ロールのサービス定義ファイルで、ランタイム構成値を環境変数として公開できます。次の XPath 値がサポートされています (これは API 値に対応します)。

これらの XPath 値は、[Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) ライブラリ経由でも使用できます。

## エミュレーターで実行中のアプリ
アプリがエミュレーターで実行されていることを示します。

| 型 | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@emulated" |
| コード |var x = RoleEnvironment.IsEmulated; |

## デプロイ ID
インスタンスのデプロイ ID を取得します。

| 型 | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@id" |
| コード |var deploymentId = RoleEnvironment.DeploymentId; |

## ロール ID
インスタンスの現在のロール ID を取得します。

| 型 | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| コード |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## ドメインの更新
インスタンスの更新ドメインを取得します。

| 型 | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| コード |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## 障害ドメイン
インスタンスの障害ドメインを取得します。

| 型 | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| コード |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## ロール名
インスタンスのロール名を取得します。

| 型 | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| コード |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## 構成設定
指定した構成設定の値を取得します。

| 型 | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| コード |var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## ローカル ストレージ パス
インスタンスのローカル ストレージ パスを取得します。

| 型 | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| コード |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1").RootPath; |

## ローカル ストレージ サイズ
インスタンスのローカル ストレージのサイズを取得します。

| 型 | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| コード |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1").MaximumSizeInMegabytes; |

## エンドポイント プロトコル
インスタンスのエンドポイント プロトコルを取得します。

| 型 | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| コード |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].Protocol; |

## エンドポイント IP
指定したエンドポイントの IP アドレスを取得します。

| 型 | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| コード |var address = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Address |

## エンドポイント ポート
インスタンスのエンドポイント ポートを取得します。

| 型 | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| コード |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Port; |

## 例
[@emulated xpath 値](#app-running-in-emulator) に設定された `TestIsEmulated` という環境変数でスタートアップ タスクを作成する worker ロールの例を次に示します。

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## 次のステップ
[ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) ファイルの詳細を理解します。

[ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) パッケージを作成します。

ロールの[リモート デスクトップ](cloud-services-role-enable-remote-desktop.md)を有効にします。

<!---HONumber=AcomDC_0810_2016-->