---
title: Azure Managed Application のビュー定義の成果物のリファレンス
description: この記事は、ビュー定義の成果物に対するリファレンスです。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: e60f26fe0a7144d768bac020d62c61cb92594914
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68336653"
---
# <a name="reference-view-definition-artifact"></a>リファレンス: ビュー定義アーティファクト

この記事は、Azure Managed Applications の *viewDefinition.json* 成果物のリファレンスです。 ビュー構成の作成の詳細については、[ビュー定義の成果物](concepts-view-definition.md)に関する記事を参照してください。

## <a name="view-definition"></a>定義の表示

次の JSON は、Azure Managed Applications 用の *viewDefinition.json* ファイルの例を示しています。

```json
{
  "views": [{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  },
  {
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
        "parameters": {
          "steps": [{
            "name": "add",
            "label": "Add user",
            "elements": [{
              "name": "name",
              "label": "User's Full Name",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a full user name.",
              "constraints": { "required": true }
            },
            {
              "name": "location",
              "label": "User's Location",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a Location.",
              "constraints": { "required": true }
            }]
          }],
          "outputs": {
            "name": "[steps('add').name]",
            "properties": {
              "FullName": "[steps('add').name]",
              "Location": "[steps('add').location]"
            }
          }
        }
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }]
}
```

## <a name="next-steps"></a>次の手順

- [チュートリアル:カスタム アクションとリソースを備えたマネージド アプリケーションを作成する](tutorial-create-managed-app-with-custom-provider.md)
- [リファレンス: ユーザー インターフェイス要素の成果物](reference-createuidefinition-artifact.md)
- [リファレンス: デプロイ テンプレートの成果物](reference-main-template-artifact.md)