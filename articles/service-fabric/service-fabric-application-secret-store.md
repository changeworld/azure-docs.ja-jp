---
title: Service Fabric のシークレット ストア
description: この記事では、Service Fabric のシークレット ストアを使用する方法について説明します。
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 16608d9eaf12fc9abc535ef316d7b5e8b74a8b37
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457512"
---
#  <a name="service-fabric-secrets-store"></a>Service Fabric のシークレット ストア
この記事では、Service Fabric のシークレットストア (CSS) を使用して Service Fabric アプリケーションでシークレットを作成および使用する方法について説明します。 CSS は、パスワード、トークン、キーなどの機密データを暗号化してメモリに保持するために使用されるローカル シークレット ストア キャッシュです。

## <a name="enabling-secrets-store"></a>シークレット ストアの有効化
 `fabricSettings` の下でクラスター構成に以下を追加して、CSS を有効にします。 CSS には、クラスター証明書とは異なる証明書を使用することをお勧めします。 すべてのノードに暗号化証明書がインストールされていること、および `NetworkService` が証明書の秘密キーの読み取りアクセス許可を持っていることを確認してください。
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
## <a name="declare-secret-resource"></a>シークレット リソースを宣言する
シークレット リソースを作成するには、Resource Manager テンプレートを使用するか、REST API を使用します。

* Resource Manager テンプレートの使用
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
上記のテンプレートは `supersecret` シークレット リソースを作成しますが、シークレット リソースの値はまだ設定されていません。

* REST API の使用

シークレット リソースを作成するために、`supersecret` は `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview` に PUT 要求を出します。 シークレットを作成するには、クラスター証明書または管理者クライアント証明書が必要です。

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-secret-value"></a>シークレット値を設定する
* Resource Manager テンプレートの使用

次の Resource Manager テンプレートは、バージョン `ver1` を使用してシークレット `supersecret` の値を作成して設定します。
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
* REST API の使用

```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="using-the-secret-in-your-application"></a>アプリケーションでのシークレットの使用

1.  settings.xml ファイルに次の内容を含むセクションを追加します。 ここで、Value の形式は {`secretname:version`} であることに注意してください

```xml
  <Section Name="testsecrets">
   <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
  </Section>
```
2. 次に、ApplicationManifest.xml にこのセクションをインポートします
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

環境変数 "SecretPath" は、すべてのシークレットが格納されているディレクトリを指します。 セクション `testsecrets` の下に一覧表示される各パラメーターは、別のファイルに格納されます。 アプリケーションは、次に示すようにシークレットを使用できるようになりました
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
3. コンテナーへのシークレットのマウント

コンテナー内でシークレットを使用できるようにするために必要な変更は、`<ConfigPackage>` に MountPoint を指定することだけです。
変更された ApplicationManifest.xml の例を次に示します  

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
シークレットは、コンテナー内のマウント ポイントで使用可能になります。

4. 環境変数へのシークレットのバインド 

Type="SecretsStoreRef" を指定することにより、プロセス環境変数にシークレットをバインドできます。 ServiceManifest.xml で `supersecret` バージョン `ver1` を 環境変数 `MySuperSecret` にバインドする方法の例を次に示します。

```xml
<EnvironmentVariables>
  <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
</EnvironmentVariables>
```
## <a name="next-steps"></a>次のステップ
アプリケーション マニフェストとサービス セキュリティの詳細については、[こちら](service-fabric-application-and-service-security.md)をご覧ください。
