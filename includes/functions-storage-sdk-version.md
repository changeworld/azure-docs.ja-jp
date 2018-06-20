---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 97c56b07833f7d93541bb0b3747889f5a50a8203
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34675277"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Functions 1.x での Azure Storage SDK のバージョン

Functions 1.x では、Storage トリガーとバインドは、Azure Storage SDK のバージョン 7.2.1 ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet パッケージ) を使用します。 別のバージョンの Storage SDK を参照し、関数シグネチャで Storage SDK の型にバインドする場合は、Functions ランタイムがその型にバインドできないことを報告することがあります。 解決するには、プロジェクトで [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) を参照していることを確認します。
