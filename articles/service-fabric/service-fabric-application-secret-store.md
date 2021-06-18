---
title: Azure Service Fabric セントラル シークレット サービス
description: この記事では、Azure Service Fabric のセントラル シークレット サービスの使用方法を説明します
author: amenarde
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: anmenard
ms.openlocfilehash: 1c920631d671ad2af9fa2e723fc98bf96462b007
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950104"
---
# <a name="central-secret-service-in-azure-service-fabric"></a>Azure Service Fabric のセントラル シークレット サービス 
セントラル シークレット サービス (CSS、別名セントラル シークレット ストア) は、クラスター内でシークレットを保護するための、Service Fabric のシステム サービスです。 CSS により SF アプリケーションでシークレットを管理しやすくなり、パラメーターの暗号化に頼る必要がなくなります。

セントラル シークレット サービスは、複製したシークレットをクラスター内部で管理する、堅牢な保管場所です。シークレットは、顧客が提供する暗号化証明書よって暗号化され CSS に保存されます。 CSS では、シークレット管理のためのクライアント API を用意しており、クラスターまたはクラスター管理者ユーザーとして認証を受けた人物または組織がこれを利用できます。 Service Fabric のランタイム アプリケーション モデルは CSS との連携が可能で、アプリケーションのパラメーターを CSS のシークレットへの参照として宣言できます。 

[Azure に展開された Service Fabric アプリケーションの Azure マネージド ID](concepts-managed-identity.md) とともに、CSS は、[KeyVault シークレット URI](service-fabric-keyvault-references.md) として宣言されたアプリケーションのシークレットのプロビジョニングにも必要です。

セントラル シークレット サービスは、Azure Key Vault など、外部のシークレット管理専用サービスを置き換えるためのものではありません。 

  > [!NOTE] 
  > Windows 認証を使用するようにクラスターを構成している場合、`EncryptionCertificateThumbprint` を誤って宣言している場合、または対応する証明書をインストールできていない場合、バージョン [7.1. CU3](service-fabric-versions.md#service-fabric-version-name-and-number-reference) 以前の SF のクラスターで CSS をアクティブ化しようとすると、アクティブ化に失敗し、それ以降ずっと CSS の異常が残る場合があります。 いずれの場合も、クラスターの SF ランタイムを 7.1 以降のバージョンにアップグレードすることをお勧めします。 7\.1.CU3 にアクセスしてください。 
  
## <a name="enable-central-secrets-service"></a>セントラル シークレット サービスを有効にする
セントラル シークレット サービスを有効にするには、下の説明に従ってクラスターの構成を変更します。 クラスターの証明書とは異なる暗号化証明書を使用することをお勧めします。 この証明書はすべてのノードにインストールする必要があります。
```json
{ 
    "fabricSettings": [
        {
            "name":  "CentralSecretService",
            "parameters":  [
                {
                    "name":  "DeployedState",
                    "value":  "enabled"
                },
                {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "1"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                }
            ]
        }
    ]
}
```
  > [!NOTE] 
  > CSS を有効または無効にする際は、Service Fabric [バージョン 8.0](service-fabric-versions.md#service-fabric-version-name-and-number-reference) で導入された構成設定項目の DeployedState を使用することを推奨します。以前のバージョンでは、現在廃止されている IsEnabled という構成設定項目によってこの機能を実装していました。

## <a name="central-secret-service-secret-model"></a>セントラル シークレット サービスのシークレット モデル
セントラル シークレット サービス API では、シークレット リソースとシークレット バージョンの 2 種類のデータを扱います。 シークレット リソースの種類は、特定の目的に使用する単一のシークレットのバージョンのファミリーを表し、たとえば接続文字列、パスワード、エンドポイントの証明書などがあります。 シークレット リソースのオブジェクトの内容は、シークレット、種類、内容の種類、説明に関するメタデータです。 シークレット バージョンの種類は、関連付けられた情報の特定のインスタンスを表し、その内容はシークレットのプレーンテキスト (暗号化済み) です。上の例に加えて、シークレット バージョンの内容には、現在のパスワードや月末まで有効な証明書のオブジェクトなどがあります。これらのシークレットを更新すると、新しいシークレット バージョンが作成されます (そして CSS に追加されます)。

モデルの作成では、CSS の実装において次のルールが適用されます。

- シークレット リソースには 0 以上のバージョンを設定できる
- すべてのシークレット バージョンは特定のシークレット リソースの子であり、バージョンには親リソースを 1 つだけ含むことができる
- 個別のシークレット バージョンは、同じシークレットの他のバージョンに影響を与えずに削除できる
- シークレット リソースを削除すると、そのリソースのすべてのバージョンが削除される  
- シークレット バージョンの値は変更できない
    
シークレット リソース用の REST 管理 API はすべて[ここ](/rest/api/servicefabric/sfclient-index-meshsecrets)で公開されています。シークレット バージョン用の API は[ここ](/rest/api/servicefabric/sfclient-index-meshsecretvalues)で公開されています。

### <a name="declare-a-secret-resource"></a>シークレット リソースを宣言する
REST API を使用して、シークレット リソースを作成できます。

  > [!NOTE] 
  > クラスターで HttpGateway の証明書がないまま Windows 認証を使用している場合、保護されていない HTTP チャネルで REST 要求を送信します。 このチャネルで TLS を有効にするには、クラスターの定義で HTTP ゲートウェイ サーバーの証明書を指定します。

REST API を使用して `supersecret` シークレット リソースを作成するために、`https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview` に PUT 要求を出します。 シークレット リソースを作成するには、クラスターの証明書または管理クライアントの証明書を使用して認証を行う必要があります。
```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

### <a name="set-the-secret-value"></a>シークレット値を設定する
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
アプリケーションでは、シークレットを環境変数として宣言する、またはシークレットをプレーンテキストとしてシリアル化する場所のパスを指定することにより、CSS のシークレットを使用できます。 この手順で CSS のシークレットを参照できます。

1. **settings.xml** ファイルに次のスニペットを含むセクションを追加します。 ここでは、値が {`secretname:version`} の形式であることに注意してください。
```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
```

2. **ApplicationManifest.xml** にこのセクションをインポートします。
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
   
例 1: シークレットをコンテナーにマウントする。 コンテナー内でシークレットを使用できるようにするために必要な変更は、`<ConfigPackage>` にマウント ポイントを `specify` することだけです。
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

例 2: `Type='SecretsStoreRef` を指定して、処理の環境変数にシークレットをバインドする。 次のスニペットは、**ServiceManifest.xml** で `supersecret` バージョン `ver1` を環境変数 `MySuperSecret` にバインドする方法の例です。

```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
```

環境変数 `SecretPath` は、すべてのシークレットが格納されているディレクトリを指します。 `testsecrets` セクションの下に一覧表示される各パラメーターは、別のファイルに格納されます。 アプリケーションは、次のようにシークレットを使用できるようになりました。
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
   
## <a name="rotating-the-central-secret-service-encryption-certificate"></a>セントラル シークレット サービス暗号化証明書をローテーションする
証明書は、有効期間を過ぎた後も、復号に関しては引き続き有効です。 現時点では、ローテーションした後も過去の暗号化証明書を使用できる状態で残しておき、ロックアウトのリスクを減らすことをお勧めします。 CSS の暗号化証明書のローテーションは、次の手順で実行する必要があります。

1. 新しい証明書をクラスターの各ノードに配置します。 現時点では、前の暗号化証明書を破棄せず、使える状態で残しておいてください。
2. クラスター構成の `EncryptionCertificateThumbprint` の値を新しい証明書の SHA-1 サムプリントに変更して更新します。
更新が完了すると、引き続いて、CSS の既存の内容を新しい暗号化証明書により再暗号化します。 これ以降 CSS に追加するすべてのシークレットは、直接新しい暗号化証明書により暗号化します。 すべてのシークレットを新しい証明書に移行する処理は非同期で行うため、前の暗号化証明書をすべてのノードにインストールしたままにし、CSS でそれを利用できる状態にしておくことが重要です。

## <a name="removing-central-secret-service-from-your-cluster"></a>セントラル シークレット サービスをクラスターから削除する
セントラル シークレット サービスをクラスターから安全に削除するには、2 回に分けて更新を行う必要があります。 最初の更新で CSS の機能を無効にし、2 回目の更新でクラスターの定義からサービスを削除します。このとき CSS の内容は完全に削除されます。 この 2 段階の手順により、サービスを誤って削除することを防ぎ、CSS に依存する機能が CSS の削除中に孤立しないようにします。 この機能は バージョン 8.0 以降の SF で使用できます。

### <a name="step-1-update-css-deployedstate-to-removing"></a>ステップ 1: CSS の DeployedState を removing に更新する
クラスターの定義で `"IsEnabled" = "true"` または `"DeployedState" = "enabled"` を次のように更新します
```json
{
    "name":  "DeployedState",
    "value":  "removing"
}
```
セントラル シークレット サービスの展開状態が `Removing` に変わった後は、直接 REST 呼び出しを行う場合も、SecretStoreRefs または KeyVaultReferences を使用するサービスをアクティブ化することで呼び出しを行う場合も、すべてのシークレット API 呼び出しを拒否します。 クラスターのアプリケーションおよび構成要素のうち、この時点で引き続き CSS に依存しているものは、すべて警告状態になります。 そうなった場合、`Removing` への展開状態の更新をロールバックする必要があります。既に更新が完了している場合は、新たに更新を行って CSS を DeployedState = `Enabled` の状態に戻す必要があります。 展開状態が `Removing` であるときにセントラル シークレット サービスが要求を受け取った場合、HTTP コード 401 (未認証) が返り、サービスが警告状態になります。

### <a name="step-2-update-css-deployedstate-to-disabled"></a>ステップ 2: CSS の DeployedState を disabled に更新する
クラスターの定義で `"DeployedState" = "removing"` を次のように変更します
```json
{
    "name":  "DeployedState",
    "value":  "disabled"
}
```
これ以降クラスターではセントラル シークレット サービスを実行せず、システム サービスのリストにも表示されなくなります。 CSS の内容は完全に失われます。 

## <a name="next-steps"></a>次のステップ

- アプリケーション マニフェストとサービス セキュリティの詳細については、[こちら](service-fabric-application-and-service-security.md)をご覧ください。
- [Service Fabric アプリケーションのマネージド ID](concepts-managed-identity.md) に関する記事をご覧ください。
- [KeyVaultReferences](service-fabric-keyvault-references.md) で CSS の機能を拡張する