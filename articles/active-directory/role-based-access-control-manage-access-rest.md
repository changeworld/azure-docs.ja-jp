<properties
	pageTitle="REST API を使用したロールベースのアクセス制御の管理"
	description="REST API を使用したロールベースのアクセス制御の管理"
	services="active-directory"
	documentationCenter="na"
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="multiple"
	ms.tgt_pltfrm="rest-api"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/12/2016"
	ms.author="kgremban"/>

# REST API を使用したロールベースのアクセス制御の管理

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

Azure ポータルと Azure Resource Manager API のロールベースのアクセス制御 (RBAC) を使用すると、サブスクリプションとリソースへのアクセスを詳細に管理できます。この機能を使用すると、Active Directory ユーザー、グループ、サービス プリンシパルに特定のスコープで役割を割り当てて、アクセス権を付与できます。


## ロールの割り当てをすべて一覧表示する

指定されたスコープとサブスコープにあるロールの割り当てをすべて一覧表示します。

ロールの割り当てを一覧表示するには、指定されたスコープにおける `Microsoft.Authorization/roleAssignments/read` 操作のアクセス権が必要です。この操作のアクセス権はすべての組み込みロールに付与されています。Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「[Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### 要求

次の URI で **GET** メソッドを使用します。

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&filter={filter}

URI 内の次の項目を置換して要求をカスタマイズします。

*{scope}* は、ロールの割り当てを一覧表示する対象のスコープに置き換えます。各種レベルのスコープを指定する例を次に示します。

| Level | *{Scope}* |
|-------|-----------|
| [サブスクリプション] | /subscriptions/{subscription-id} |
| リソース グループ | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| リソース | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{api-version}* は 2015-07-01 に置き換えます。

*{filter}* には、ロールの割り当て一覧をフィルター処理するために適用する条件を指定します。次の条件がサポートされます。


| 条件 | *{Filter}* | *Views\\\Home\\\AllDates.cshtml* |
|-----------|------------|---------|
| (サブスコープのロールの割り当ては含めずに) 指定したスコープのみを対象にロールの割り当てを一覧表示する。 | `atScope()` | |
| 特定のユーザー、グループ、またはアプリケーションのみを対象にロールの割り当てを一覧表示する。 | `principalId%20eq%20'{objectId}'` | *{objectId}* は、対象となるユーザー、グループ、またはサービス プリンシパルの Azure AD objectId に置き換えます。例: `&filter=principalId%20eq%20'3a477f6a-6739-4b93-84aa-3be3f8c8e7c2'` |
| 特定のユーザーのみを対象にロールの割り当て (そのユーザーが属しているグループに割り当てられているものも含む) を一覧表示する。 | `assignedTo('{objectId}')` | *{objectId}* は、対象となるユーザーの Azure AD objectId に置き換えます。例: `&filter=assignedTo('3a477f6a-6739-4b93-84aa-3be3f8c8e7c2')` |



### 応答

状態コード: 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## ロールの割り当てに関する情報を取得する

ロール割り当て ID によって指定された単一のロールの割り当てに関する情報を取得します。

ロールの割り当てに関する情報を取得するには、`Microsoft.Authorization/roleAssignments/read` 操作のアクセス権が必要です。この操作のアクセス権はすべての組み込みロールに付与されています。Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「[Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### 要求

次の URI で **GET** メソッドを使用します。

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

URI 内の次の項目を置換して要求をカスタマイズします。

*{scope}* は、ロールの割り当てを一覧表示する対象のスコープに置き換えます。各種レベルのスコープを指定する例を次に示します。

| Level | *{Scope}* |
|-------|-----------|
| [サブスクリプション] | /subscriptions/{subscription-id} |
| リソース グループ | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| リソース | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{role-assignment-id}* は、ロールの割り当ての GUID 識別子に置き換えます。

*{api-version}* は 2015-07-01 に置き換えます。

### 応答

状態コード: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## ロールの割り当てを作成する

対象となるプリンシパルとスコープを指定してロールの割り当てを作成し、指定したロールを付与します。

ロールの割り当てを作成するには、`Microsoft.Authorization/roleAssignments/write` 操作のアクセス権が必要です。組み込みロールのうち、この操作のアクセス権が付与されているのは *Owner* と *User Access Administrator* だけです。Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「[Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### 要求

次の URI で **PUT** メソッドを使用します。

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

URI 内の次の項目を置換して要求をカスタマイズします。

*{scope}* は、ロールの割り当てを作成する対象のスコープに置き換えます。親スコープでロールの割り当てを作成すると、同じロールの割り当てが、すべての子スコープに継承されます。各種レベルのスコープを指定する例を次に示します。

| Level | *{Scope}* |
|-------|-----------|
| [サブスクリプション] | /subscriptions/{subscription-id} |
| リソース グループ | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| リソース | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{role-assignment-id}* は、新しい GUID に置き換えます。これが新しいロールの割り当ての GUID 識別子として使用されます。

*{api-version}* は 2015-07-01 に置き換えます。

要求本文には、次の形式で値を指定します。

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| 要素名 | 必須 | 型 | 説明 |
|------------------|----------|--------|-------------|
| roleDefinitionId | はい | String | 割り当てるロールの識別子。識別子の形式: `{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId | はい | String | ロールの割り当て先となる Azure AD プリンシパル (ユーザー、グループ、またはサービス プリンシパル) の objectId。 |

### 応答

状態コード: 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## ロールの割り当てを削除する

指定したスコープにおけるロールの割り当てを削除します。

ロールの割り当てを削除するには、`Microsoft.Authorization/roleAssignments/delete` 操作のアクセス権が必要です。組み込みロールのうち、この操作のアクセス権が付与されているのは *Owner* と *User Access Administrator* だけです。Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「[Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### 要求

次の URI で **DELETE** メソッドを使用します。

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

URI 内の次の項目を置換して要求をカスタマイズします。

*{scope}* は、ロールの割り当てを作成する対象のスコープに置き換えます。各種レベルのスコープを指定する例を次に示します。

| Level | *{Scope}* |
|-------|-----------|
| [サブスクリプション] | /subscriptions/{subscription-id} |
| リソース グループ | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| リソース | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{role-assignment-id}* は、ロール割り当て ID (GUID) に置き換えます。

*{api-version}* は 2015-07-01 に置き換えます。

### 応答

状態コード: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## すべてのロールを一覧表示する

指定したスコープでの割り当てに使用できるすべてのロールを一覧表示します。

ロールを一覧表示するには、指定されたスコープにおける `Microsoft.Authorization/roleDefinitions/read` 操作のアクセス権が必要です。この操作のアクセス権はすべての組み込みロールに付与されています。Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「[Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### 要求

次の URI で **GET** メソッドを使用します。

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&filter={filter}

URI 内の次の項目を置換して要求をカスタマイズします。

*{scope}* は、ロールを一覧表示する対象のスコープに置き換えます。各種レベルのスコープを指定する例を次に示します。

| Level | *{Scope}* |
|-------|-----------|
| [サブスクリプション] | /subscriptions/{subscription-id} |
| リソース グループ | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| リソース | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{api-version}* は 2015-07-01 に置き換えます。

*{filter}* には、ロールの一覧をフィルター処理するために適用する条件を指定します。次の条件がサポートされます。

| 条件 | *{Filter}* | *Views\\\Home\\\AllDates.cshtml* |
|-----------|------------|---------|
| 指定したスコープとそのすべての子スコープでの割り当てに使用できるロールを一覧表示する。 | `atScopeAndBelow()` | |
| 完全な表示名を使用してロールを検索する。 | `roleName%20eq%20'{role-display-name}'` | *{role-display-name}* には、ロールの完全な表示名を URL エンコード形式で指定します。例: `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |



### 応答

状態コード: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## ロールに関する情報を取得する

ロール定義識別子によって指定された単一のロールに関する情報を取得します。単一のロールに関する情報をその表示名を使って取得する方法については、すべてのロールを一覧表示する方法と roleName フィルターに関する説明を参照してください。

ロールに関する情報を取得するには、`Microsoft.Authorization/roleDefinitions/read` 操作のアクセス権が必要です。この操作のアクセス権はすべての組み込みロールに付与されています。Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「[Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### 要求

次の URI で **GET** メソッドを使用します。

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI 内の次の項目を置換して要求をカスタマイズします。

*{scope}* は、ロールの割り当てを一覧表示する対象のスコープに置き換えます。各種レベルのスコープを指定する例を次に示します。

| Level | *{Scope}* |
|-------|-----------|
| [サブスクリプション] | /subscriptions/{subscription-id} |
| リソース グループ | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| リソース | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{role-definition-id}* は、ロールの定義の GUID 識別子に置き換えます。*{api-version}* は 2015-07-01 に置き換えます。

### 応答

状態コード: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## カスタム ロールの作成
カスタム ロールを作成します。

カスタム ロールを作成するには、対応するすべての `AssignableScopes` に対する `Microsoft.Authorization/roleDefinitions/write` 操作のアクセス権が必要です。組み込みロールのうち、この操作のアクセス権が付与されているのは *Owner* と *User Access Administrator* だけです。Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「[Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### 要求

次の URI で **PUT** メソッドを使用します。

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI 内の次の項目を置換して要求をカスタマイズします。

*{scope}* は、カスタム ロールの 1 つ目の *AssignableScope* に置き換えます。各種レベルのスコープを指定する例を次に示します。

| Level | *{Scope}* |
|-------|-----------|
| [サブスクリプション] | /subscriptions/{subscription-id} |
| リソース グループ | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| リソース | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{role-definition-id}* は、新しい GUID に置き換えます。これが新しいカスタム ロールの GUID 識別子として使用されます。

*{api-version}* は 2015-07-01 に置き換えます。

要求本文には、次の形式で値を指定します。

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| 要素名 | 必須 | 型 | 説明 |
|--------------|----------|------|-------------|
| name | はい | String | カスタム ロールの GUID 識別子。 |
| properties.roleName | はい | String | カスタム ロールの表示名。最大 128 文字です。 |
| properties.description | いいえ | String | カスタム ロールの説明。最大 1,024 文字です。 |
| properties.type | はい | String | これを "CustomRole" に設定します。 |
| properties.permissions.actions | はい | String | アクション文字列の配列。カスタム ロールでアクセス権を付与する操作を指定します。 |
| properties.permissions.notActions | いいえ | String | アクション文字列の配列。カスタム ロールで付与するアクセス権の対象外とする操作を指定します。 |
| properties.assignableScopes | はい | String | そのカスタム ロールを使ってアクセス管理を行うことができるスコープの配列。 |

### 応答

状態コード: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## カスタム ロールの更新

カスタム ロールに変更を加えます。

カスタム ロールに変更を加えるには、対応するすべての `AssignableScopes` に対する `Microsoft.Authorization/roleDefinitions/write` 操作のアクセス権が必要です。組み込みロールのうち、この操作のアクセス権が付与されているのは *Owner* と *User Access Administrator* だけです。Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「[Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### 要求

次の URI で **PUT** メソッドを使用します。

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI 内の次の項目を置換して要求をカスタマイズします。

*{scope}* は、カスタム ロールの 1 つ目の *AssignableScope* に置き換えます。各種レベルのスコープを指定する例を次に示します。

| Level | *{Scope}* |
|-------|-----------|
| [サブスクリプション] | /subscriptions/{subscription-id} |
| リソース グループ | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| リソース | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{role-definition-id}* は、更新対象となるカスタム ロールの GUID 識別子に置き換えます。

*{api-version}* は 2015-07-01 に置き換えます。

要求本文には、次の形式で値を指定します。

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| 要素名 | 必須 | 型 | 説明 |
|--------------|----------|------|-------------|
| name | はい | String | 更新するカスタム ロールの GUID 識別子。 |
| properties.roleName | はい | String | 更新するカスタム ロールの表示名。 |
| properties.description | いいえ | String | 更新するカスタム ロールの説明。 |
| properties.type | はい | String | これを "CustomRole" に設定します。 |
| properties.permissions.actions | はい | String | アクション文字列の配列。更新したカスタム ロールでアクセス権を付与する操作を指定します。 |
| properties.permissions.notActions | いいえ | String | アクション文字列の配列。更新したカスタム ロールで付与するアクセス権の対象外とする操作を指定します。 |
| properties.assignableScopes | はい | String | 更新したカスタム ロールを使ってアクセス管理を行うことができるスコープの配列。 |

### 応答

状態コード: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## カスタム ロールの削除

カスタム ロールを削除します。

カスタム ロールを削除するには、対応するすべての `AssignableScopes` に対する `Microsoft.Authorization/roleDefinitions/delete` 操作のアクセス権が必要です。組み込みロールのうち、この操作のアクセス権が付与されているのは *Owner* と *User Access Administrator* だけです。Azure のリソースに対するアクセス管理とロール割り当ての詳細については、「[Azure のロールベースのアクセス制御](role-based-access-control-configure.md)」を参照してください。

### 要求

次の URI で **DELETE** メソッドを使用します。

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

URI 内の次の項目を置換して要求をカスタマイズします。

*{scope}* は、ロールの定義を削除する対象のスコープに置き換えます。各種レベルのスコープを指定する例を次に示します。

| Level | *{Scope}* |
|-------|-----------|
| [サブスクリプション] | /subscriptions/{subscription-id} |
| リソース グループ | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| リソース | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

*{role-definition-id}* は、削除対象となるカスタム ロールの GUID (ロール定義 ID) に置き換えます。

*{api-version}* は 2015-07-01 に置き換えます。

### 応答

状態コード: 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```

<!---HONumber=AcomDC_0413_2016-->