---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 03/12/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: c306c52ff6f76e09312995974013df6840a5a1a1
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
Azure Functions バージョン 2.x のローカル開発では、パッケージは、自動的に[バインド拡張機能として登録](../articles/azure-functions/functions-triggers-bindings.md#local-development-azure-functions-core-tools)されます。 Functions 1.x と 2.x での C# クラス ライブラリの開発では、パッケージは自動的に[プロジェクトにインストール](../articles/azure-functions/functions-triggers-bindings.md#local-c-development-using-visual-studio-or-vs-code)されます。 

ソース コードは、WebJobs SDK バージョン番号の番号付けに従うことに注意してください。WebJobs SDK バージョン 2.x は Azure Functions 1.x に相当するため、Functions 1.x のコードは、リポジトリの v2.x ブランチに配置されます。 マスター ブランチと開発ブランチには Web Jobs 3.x のコードが含まれますが、それは Functions 2.x と同じです。
