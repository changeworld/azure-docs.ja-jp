---
title: ASP.NET 5 プロジェクトの変更点 (Visual Studio 接続済みサービス) | Microsoft Docs
description: Visual Studio 接続済みサービスを使用して Visual Studio ASP.NET 5 プロジェクトの Azure ストレージ アカウントに接続した後の変更点について説明します。
services: storage
author: ghogen
manager: douge
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 0e38e570b5ecb2a19af943e0dde96e2ed66f3507
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42146613"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>ASP.NET 5 プロジェクトの変更点 (Visual Studio Azure Storage 接続済みサービス)
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

また、NuGet パッケージ **Microsoft.Framework.Configuration.Json** が追加されました。

## <a name="connection-string-for-azure-storage-added"></a>Azure Storage の接続文字列の追加
選択されたストレージ アカウントの接続文字列とキーを使用して、プロジェクトの config.json ファイル内に要素が作成されました。

詳細については、「 [ASP.NET 5](http://www.asp.net/vnext)」を参照してください。

