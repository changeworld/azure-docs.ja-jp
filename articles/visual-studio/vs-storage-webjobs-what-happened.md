---
title: Web ジョブ プロジェクトの変更点 (Visual Studio Azure Storage 接続済みサービス) | Microsoft Docs
description: Visual Studio 接続済みサービスを使用してストレージ アカウントに接続した後の、Azure Web ジョブ プロジェクトの変更点について説明します。
services: storage
author: ghogen
manager: douge
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 673143c2bfb23bda45c4e435c3048ae61fa16902
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256903"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Web ジョブ プロジェクトの変更点 (Visual Studio Azure Storage 接続済みサービス)
## <a name="references-added"></a>リファレンスの追加
Visual Studio プロジェクトで Azure Storage の NuGet パッケージが追加または更新されました。  
このパッケージは、次の .NET 参照を追加します。

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Azure Storage の接続文字列の追加
選択したストレージ アカウントの接続文字列とキーを使用して、プロジェクトの App.config ファイル内の **AzureWebJobsStorage** エントリと **AzureWebJobsDashboard** エントリが更新されました。

詳細については、「 [Azure WebJobs のドキュメント リソース](https://go.microsoft.com/fwlink/?linkid=390226)」を参照してください。

