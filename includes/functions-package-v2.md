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
ms.openlocfilehash: bcc55156ca1d03614a4ff9767d6cf3f2603c06ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027533"
---
次の方法を使用して、推奨される開発環境にサポートを追加します。

| 開発環境  | アプリケーションの種類      | サポートを追加するには |
|--------------------------|-----------------------|----------------|
| Visual Studio            | C# クラス ライブラリ      | [NuGet パッケージをインストールする](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | [コアツール](../articles/azure-functions/functions-run-local.md)に基づく | [拡張機能バンドルを登録する](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>[Azure Tools 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) をインストールすることをお勧めします。 |
| その他のエディター/IDE     | [コアツール](../articles/azure-functions/functions-run-local.md)に基づく | [拡張機能バンドルを登録する](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure portal             | ポータルでのみオンライン | バインドを追加するときにインストールする<br /><br /> 関数アプリを再発行せずに、既存のバインディング拡張機能を更新するには、[拡張機能の更新](../articles/azure-functions/functions-bindings-register.md)に関するページを参照してください。 |