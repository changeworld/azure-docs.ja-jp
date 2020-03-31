---
title: Azure Service Fabric セントラル シークレット ストア
description: この記事では、Azure Service Fabric のセントラル シークレット ストアを使用する方法について説明します。
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 11fb94a9fba40e6f2474ad64f5eb0c454be28ca0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589166"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Azure Service Fabric のセントラル シークレット ストア 
この記事では、Azure Service Fabric のセントラル シークレットストア (CSS) を使用して Service Fabric アプリケーションでシークレットを作成する方法について説明します。 CSS は、パスワード、トークン、キーなどの機密データを暗号化してメモリに保持するローカル シークレット ストア キャッシュです。

## <a name="enable-central-secrets-store"></a>セントラル シークレット ストアを有効にする
`fabricSettings` の下でクラスター構成に次のスクリプトを追加して、CSS を有効にします。 CSS には、クラスター証明書以外の証明書を使用することをお勧めします。 すべてのノードに暗号化証明書がインストールされていること、および `NetworkService` が証明書の秘密キーの読み取りアクセス許可を持っていることを確認してください。
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>シークレット リソースを宣言する
シークレット リソースは、Resource Manager テンプレートまたは REST API を使用することで作成できます。

### <a name="use-resource-manager"></a>Resource Manager の使用

Resource Manager を使用してシークレット リソースを作成するには、次のテンプレートを使用します。 このテンプレートは `supersecret` シークレット リソースを作成しますが、シークレット リソースの値はまだ設定されていません。


```json
   "resources": [
      {
        "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
          }
        }
      ]
```

### <a name="use-the-rest-api"></a>REST API の使用

REST API を使用して `supersecret` シークレット リソースを作成するために、`https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview` に PUT 要求を出します。 シークレット リソースを作成するには、クラスター証明書または管理者クライアント証明書が必要です。

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>シークレット値を設定する

### <a name="use-the-resource-manager-template"></a>Resource Manager テンプレートを使用する

シークレット値を作成および設定するには、次の Resource Manager を使用します。 このテンプレートは、`supersecret` シークレットリソースのシークレット値をバージョン `ver1` として設定します。
```json
  {
  "parameters": {
  "supersecret": {
      "type": "string",
      "metadata": {
        "description": "supersecret value"
      }
   }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
        }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "supersecret/ver1",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/supersecret"
      ],
      "properties": {
        "value": "[parameters('supersecret')]"
      }
    }
  ],
  ```
### <a name="use-the-rest-api"></a>REST API の使用

REST API を使用してシークレット値を設定するには、次のスクリプトを使用します。
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>シークレット値を確認する
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>アプリケーションでのシークレットの使用

Service Fabric アプリケーションでシークレットを使用するには、次の手順に従います。

1. **settings.xml** ファイルに次のスニペットを含むセクションを追加します。 ここでは、値が {`secretname:version`} の形式であることに注意してください。

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. **ApplicationManifest.xml** にこのセクションをインポートします。
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   環境変数 `SecretPath` は、すべてのシークレットが格納されているディレクトリを指します。 `testsecrets` セクションの下に一覧表示される各パラメーターは、別のファイルに格納されます。 アプリケーションは、次のようにシークレットを使用できるようになりました。
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. コンテナーにシークレットをマウントします。 コンテナー内でシークレットを使用できるようにするために必要な変更は、`<ConfigPackage>` にマウント ポイントを `specify` することだけです。
次のスニペットは、変更された **ApplicationManifest.xml** です。  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   シークレットは、コンテナー内のマウント ポイントで使用可能です。

1. `Type='SecretsStoreRef` を指定することにより、プロセス環境変数にシークレットをバインドできます。 次のスニペットは、**ServiceManifest.xml** で `supersecret` バージョン `ver1` を環境変数 `MySuperSecret` にバインドする方法の例です。

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>次のステップ
アプリケーション マニフェストとサービス セキュリティの詳細については、[こちら](service-fabric-application-and-service-security.md)をご覧ください。
