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
ms.openlocfilehash: f8733ef907b8f31ace7ea72f705ba1b37d1adece
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180965"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Functions 1.x での Azure Storage SDK のバージョン

Functions 1.x では、Storage トリガーとバインドは、Azure Storage SDK のバージョン 7.2.1 ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet パッケージ) を使用します。 別のバージョンの Storage SDK を参照し、関数シグネチャで Storage SDK の型にバインドする場合は、Functions ランタイムがその型にバインドできないことを報告することがあります。 解決するには、プロジェクトで [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) を参照していることを確認します。
