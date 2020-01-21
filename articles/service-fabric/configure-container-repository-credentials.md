---
title: Azure Service Fabric - コンテナー リポジトリの資格情報を構成する
description: コンテナー レジストリからイメージをダウンロードするためのリポジトリ資格情報を構成します
author: arya
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: arya
ms.openlocfilehash: 25fe3c69b19d397137d1e1802e941e0433a1b160
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351671"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>アプリケーションでコンテナー イメージをダウンロードするためのリポジトリ資格情報を構成する

コンテナー レジストリの認証は、ApplicationManifest.xml ファイルの `ContainerHostPolicies` に `RepositoryCredentials` を追加することによって構成します。 myregistry.azurecr.io コンテナー レジストリのアカウントとパスワードを追加することで、サービスがコンテナー イメージをリポジトリからダウンロードできるようになります。

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

リポジトリのパスワードは、クラスターのすべてのノードにデプロイされる暗号化証明書を使用して暗号化することをお勧めします。 Service Fabric がサービス パッケージをクラスターにデプロイするときに、その暗号化証明書を使って暗号テキストが解読されます。 Invoke-ServiceFabricEncryptText コマンドレットを使ってパスワードの暗号テキストを作成し、ApplicationManifest.xml ファイルに追加してください。
証明書と暗号化セマンティクスの詳細については、[シークレットの管理](service-fabric-application-secret-management.md)に関するページを参照してください。

## <a name="configure-cluster-wide-credentials"></a>クラスター全体の資格情報を構成する

Service Fabric では、アプリケーションから既定のリポジトリ資格情報として使用できる、クラスター全体の資格情報を構成できます。

この機能は、`true` または `false` の値を指定した `UseDefaultRepositoryCredentials` 属性を ApplicationManifest.xml の `ContainerHostPolicies` に追加することで、有効または無効にすることができます。

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Service Fabric では、`Hosting` セクションの ClusterManifest に指定できる、既定のリポジトリ資格情報が使用されます。  `UseDefaultRepositoryCredentials` が `true` の場合、Service Fabric で ClusterManifest から次の値が読み取られます。

* DefaultContainerRepositoryAccountName (文字列)
* DefaultContainerRepositoryPassword (文字列)
* IsDefaultContainerRepositoryPasswordEncrypted (ブール値)
* DefaultContainerRepositoryPasswordType (文字列) --- 6.4 ランタイム以降でサポートされています

ClusterManifestTemplate.json ファイルの `Hosting` セクション内に追加できる内容の例を次に示します。 `Hosting` セクションは、クラスターの作成時、または構成をアップグレードするときに後で追加できます。 詳細については、[Service Fabric クラスター設定の変更](service-fabric-cluster-fabric-settings.md)と [Service Fabric アプリケーションのシークレット管理](service-fabric-application-secret-management.md)に関するページを参照してください。

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="leveraging-the-managed-identity-of-the-virtual-machine-scale-set-by-using-managed-identity-service-msi"></a>マネージド ID サービス (MSI) を使用して仮想マシン スケール セットのマネージド ID を活用する

Service Fabric では、お使いのコンテナーのイメージをダウンロードするための資格情報としてのトークンの使用をサポートしています。  この機能は、基になる仮想マシン スケール セットのマネージド ID を活用してレジストリに対する認証を行うため、ユーザー資格情報を管理する必要がなくなります。  MSI の詳細については、[マネージド サービス ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) に関するページを参照してください。  この機能を使用するには、次の手順を実行する必要があります。

1.  VM に対してシステム割り当てマネージド ID が有効になっていることを確認します (以下のスクリーンショットを参照してください)

    ![仮想マシン スケール セット ID を作成する](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2.  その後、レジストリからイメージをプルするアクセス許可または読み取るアクセス許可を VM(SS) に付与します。  次に示すように、Azure ブレードを介して ACR のアクセス制御 (IAM) に移動し、VM(SS) に適切なアクセス許可を付与します。

    ![VM プリンシパルを ACR に追加する](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3.  上記の手順が完了したら、applicationmanifest.xml ファイルを変更します。  "ContainerHostPolicies" というラベルのタグを見つけ、属性 `‘UseTokenAuthenticationCredentials=”true”` を追加します。

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > `UseTokenAuthenticationCredentials` を true にし、さらにフラグ `UseDefaultRepositoryCredentials` を true に設定すると、デプロイ中にエラーが発生します。

## <a name="next-steps"></a>次のステップ

* [コンテナー レジストリ認証](/azure/container-registry/container-registry-authentication)の詳細を確認します。
