---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 4ce048cafa4c20e3bbdbd8ecf1669748531ee122
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129085"
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
| version | インストールするバンドルのバージョン。 Functions ランタイムは常に、バージョン範囲 (間隔) で定義された最大許容バージョンを選択します。 上記のバージョン値には、1.0.0 から 2.0.0 未満までのすべてのバンドル バージョンを指定できます。 詳細については、[バージョン範囲を指定する間隔の表記](/nuget/reference/package-versioning#version-ranges-and-wildcards)に関する説明を参照してください。 |

バンドルのバージョンは、バンドル内のパッケージの変更に応じて増加します。 メジャー バージョンの変更は、バンドル内のパッケージがバージョンアップされる場合にのみ発生します。 バンドル内のメジャー バージョンの変更は、通常は、Functions ランタイムのメジャー バージョンの変更と同時に発生します。  

既定のバンドルによってインストールされる最新の拡張機能のセットは、この [extensions.json ファイル](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)に列挙されています。
