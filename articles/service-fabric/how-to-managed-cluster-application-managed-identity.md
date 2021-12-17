---
title: Microsoft Azure Service Fabric マネージド クラスター上でマネージド ID を持つアプリケーションを構成して使用する
description: Azure Resource Manager (ARM) テンプレートでデプロイされた Microsoft Azure Service Fabric マネージド クラスター上で、マネージド ID を持つアプリケーションを構成して使用する方法について説明します。
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: e3fcae9a77a4fb559d8109dd2cdd1b2a9be77b73
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615260"
---
# <a name="deploy-an-application-with-managed-identity-to-a-service-fabric-managed-cluster"></a>マネージド ID を使用してアプリケーションを Microsoft Azure Service Fabric マネージド クラスターにデプロイする

マネージド ID を持つ Service Fabric アプリケーションをデプロイするには、Azure Resource Manager からアプリケーションをデプロイする必要があり、通常は Azure Resource Manager テンプレートを使用します。 Service Fabric アプリケーションを Azure Resource Manager を介してデプロイする方法の詳細については、「[Azure Resource Manager を使用してアプリケーションを管理対象クラスターにデプロイする](how-to-managed-cluster-app-deployment-template.md)」を参照してください。

> [!NOTE] 
> 
> Azure リソースとしてデプロイされていないアプリケーションは、マネージド ID を持つことが **できません**。 
>
> マネージド ID による Service Fabric アプリケーションのデプロイは、マネージド クラスターの API バージョン `"2021-05-01"` でサポートされています。

マネージド クラスター テンプレートのサンプルは、こちらの [Service Fabric マネージド クラスター テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)で入手できます。

## <a name="managed-identity-support-in-service-fabric-managed-cluster"></a>Service Fabric マネージド クラスターでのマネージド ID サポート

Service Fabric アプリケーションが [Azure リソース用のマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) で構成され、クラスターにデプロイされると、Service Fabric マネージド クラスターで *マネージド ID トークン サービス* の自動構成がトリガーされます。 このサービスは、マネージド ID を使用して Service Fabric アプリケーションの認証を実行し、アクセス トークンを代理で取得します。 サービスが有効になると、Service Fabric Explorer の左側のウィンドウの **[システム]** セクションに表示され、**fabric:/System/ManagedIdentityTokenService** という名前で実行されます。

>[!NOTE]
>マネージド ID を使用したアプリケーションのデプロイが初めて行われる場合は、クラスター構成の変更が自動的に行われるため、1 回のデプロイ時間が長くなると予想する必要があります。 ゾーン クラスターの場合の 15 分から、ゾーンにまたがるクラスターの場合の 45 分までの時間を予想する必要があります。 他に実行されているデプロイがある場合は、それらのデプロイが先に完了するの待ってから、MITS 構成が実行されます。

アプリケーション リソースでは SystemAssigned および UserAssigned の両方の割り当てがサポートされ、割り当ては次のスニペットに示すように実行できます。

```json
{
  "type": "Microsoft.ServiceFabric/managedclusters/applications",
  "apiVersion": "2021-05-01",
  "identity": {
    "type": "SystemAssigned",
    "userAssignedIdentities": {}
  },
}

```
[完全な JSON リファレンス](/azure/templates/microsoft.servicefabric/2021-05-01/managedclusters/applications?tabs=json)

## <a name="user-assigned-identity"></a>ユーザー割り当て ID

ユーザー割り当て ID を持つアプリケーションを有効にするには、まず、**userAssigned** 型と参照されるユーザー割り当て ID でアプリケーション リソースに **ID** プロパティを追加します。 次に、**application** リソースの **properties** セクション内に **managedIdentities** セクションを追加します。このセクションには、ユーザー割り当て ID ごとのフレンドリ名と principalId のマッピングの一覧が含まれます。 ユーザー割り当て ID について詳しくは、[ユーザー割り当てマネージド ID の作成、一覧表示、削除](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)に関する記事をご覧ください。

### <a name="application-template"></a>アプリケーション テンプレート

ユーザー割り当て ID を持つアプリケーションを有効にするには、まず、**userAssigned** 型と参照されるユーザー割り当て ID でアプリケーション リソースに **identity** プロパティを追加し、次にユーザー割り当て ID ごとのフレンドリ名と principalId のマッピングの一覧を格納する **managedIdentities** オブジェクトを **properties** セクション内に追加します。

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters/applications",
  "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[parameters('applicationVersion')]",
    "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
  ],
  "identity": {
    "type" : "userAssigned",
    "userAssignedIdentities": {
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
    }
  },
  "properties": {
    "version": "[parameters('applicationVersion')]",
    "parameters": {
    },
    "managedIdentities": [
      {
        "name" : "[parameters('userAssignedIdentityName')]",
        "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
      }
    ]
  }
}
```

上の例では、アプリケーションのマネージド ID のフレンドリ名として、ユーザー割り当て ID のリソース名が使用されています。 次の例では、実際のフレンドリ名が "AdminUser" であるものとしています。

### <a name="application-package"></a>アプリケーション パッケージ

1. Azure Resource Manager テンプレートの `managedIdentities` セクションに定義されている ID ごとに、アプリケーション マニフェストの **Principals** セクションの下に `<ManagedIdentity>` タグを追加します。 `Name` 属性は、`managedIdentities` セクションに定義されている `name` プロパティと一致する必要があります。

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. **ServiceManifestImport** セクションで、マネージド ID を使用するサービスに **IdentityBindingPolicy** を追加します。 このポリシーによって、後でサービス マニフェストに追加する必要があるサービス固有の ID 名に `AdminUser` ID がマップされます。

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. サービス マニフェストを更新して、**Resources** セクション内に、アプリケーション マニフェストの `IdentityBindingPolicy` 内の `ServiceIdentityRef` と一致する名前で **ManagedIdentity** を追加します。

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

### <a name="application-template"></a>アプリケーション テンプレート

システム割り当てのマネージド ID を持つアプリケーションを有効にするには、以下の例に示すように、**systemAssigned** の種類で、**identity** プロパティをアプリケーション リソースに追加します。

```json
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
このプロパティは、Azure Resource Manager、および Managed Identity プロバイダーと Service Fabric Resource プロバイダーのそれぞれに対して、このリソースが暗黙的な (`system assigned`) マネージド ID を持つことを宣言します。

### <a name="application-and-service-package"></a>アプリケーションとサービス パッケージ

1. 以下に示すように、アプリケーション マニフェストを更新して、1 つのエントリを含む **ManagedIdentity** 要素を **Principals** セクションに追加します。

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    これにより、アプリケーションを構成するサービスへの今後の割り当てについては、リソースとしてアプリケーションに割り当てられた ID がフレンドリ名にマップされます。 

2. 以下に示すように、マネージド ID を割り当てるサービスに対応する **ServiceManifestImport** セクションに **IdentityBindingPolicy** 要素を追加します。

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    この要素は、アプリケーションの ID をサービスに割り当てます。この割り当てがないと、サービスはアプリケーションの ID にアクセスできなくなります。 上記のスニペットで、`SystemAssigned` ID (予約済みキーワード) は、フレンドリ名 `WebAdmin` でサービスの定義にマップされています。

3. サービス マニフェストを更新して、アプリケーション マニフェストの `IdentityBindingPolicy` 定義の `ServiceIdentityRef` 設定の値に一致する名前を使用して、**ManagedIdentity** 要素を **Resources** セクション内に追加します。

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    これは、前述の ID からサービスへのマッピングと同等ですが、サービス定義の観点からのものになります。 ID は、アプリケーション マニフェストで宣言されているように、ここではフレンドリ名 (`WebAdmin`) で参照されます。

## <a name="next-steps"></a>次のステップ
* [Service Fabric アプリケーションのマネージド ID に、Service Fabric マネージド クラスター上の Azure リソースへのアクセス権を付与する](how-to-managed-cluster-grant-access-other-resources.md)
* [サービス コードから Service Fabric アプリケーションのマネージド ID を活用する](how-to-managed-identity-service-fabric-app-code.md)
