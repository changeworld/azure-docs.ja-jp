---
title: ASP.NET プロジェクトの変更点 | Microsoft Docs
description: Visual Studio 接続済みサービスを使用して ASP.NET プロジェクトに Azure Storage を追加した後の変更点について説明します。
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 8f858dc5e3b28f5ea83af295e3dffe2c12fafc53
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2018
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>ASP.NET プロジェクトの変更点 (Visual Studio Azure Storage 接続済みサービス)
## <a name="references-added"></a>リファレンスの追加
Visual Studio プロジェクトに Azure Storage の NuGet パッケージが追加されました。  
このパッケージは、次の .NET 参照を追加します。

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Azure Storage の接続文字列の追加
選択されたストレージ アカウントの接続文字列とキーを使用して、プロジェクトの web.config ファイル内に要素が作成されました。

詳細については、「 [ASP.NET](http://www.asp.net)」を参照してください。

