---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 2eb82eb8b4a64cbbfef91539f46ba084aa73f207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205702"
---
次の方法を使用して、推奨される開発環境にサポートを追加します。

| 開発環境  | アプリケーションの種類      | サポートを追加するには |
|--------------------------|-----------------------|----------------|
| Visual Studio            | C# クラス ライブラリ      | [NuGet パッケージをインストールする](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | [コアツール](../articles/azure-functions/functions-run-local.md)に基づく | [拡張機能バンドルを登録する](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>[Azure Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) をインストールすることをお勧めします。 |
| その他のエディター/IDE     | [コアツール](../articles/azure-functions/functions-run-local.md)に基づく | [拡張機能バンドルを登録する](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure portal             | ポータルでのみオンライン | バインドを追加するときにインストールする<br /><br /> 関数アプリを再発行せずに、既存のバインディング拡張機能を更新するには、[拡張機能の更新](../articles/azure-functions/install-update-binding-extensions-manual.md)に関するページを参照してください。 |
