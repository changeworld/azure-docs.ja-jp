---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79382074"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

`extensionBundle` では次のプロパティを使用できます。

| プロパティ | 説明 |
| -------- | ----------- |
| id | Microsoft Azure Functions 拡張機能バンドルの名前空間。 |
| version | インストールするバンドルのバージョン。 Functions ランタイムは常に、バージョン範囲 (間隔) で定義された最大許容バージョンを選択します。 上記のバージョン値には、1.0.0 から 2.0.0 未満までのすべてのバンドル バージョンを指定できます。 詳細については、[バージョン範囲を指定する間隔の表記](/nuget/reference/package-versioning#version-ranges)に関する説明を参照してください。 |

バンドルのバージョンは、バンドル内のパッケージの変更に応じて増加します。 メジャー バージョンの変更は、バンドル内のパッケージがバージョンアップされる場合にのみ発生します。 バンドル内のメジャー バージョンの変更は、通常は、Functions ランタイムのメジャー バージョンの変更と同時に発生します。  

既定のバンドルによってインストールされる最新の拡張機能のセットは、この [extensions.json ファイル](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)に列挙されています。
