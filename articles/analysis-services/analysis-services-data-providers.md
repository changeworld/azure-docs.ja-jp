---
title: "Azure Analysis Services へのクライアント接続に必要なデータ プロバイダー | Microsoft Docs"
description: "クライアントが Azure Analysis Services に接続するために必要なデータ プロバイダーについて説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/20/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 4dbd2c87d648f6923772b070fdfe7379de22e6c5
ms.openlocfilehash: e1adeaca702de3bad1c2e572f89ade9eba753173


---

# <a name="data-providers-for-connecting-to-azure-analysis-services"></a>Azure Analysis Services に接続するためのデータ プロバイダー

データ プロバイダーはクライアント ライブラリとも呼ばれ、クライアント アプリケーションが Analysis Services サーバーに接続するために必要です。 

Analysis Services では&3; つのデータ プロバイダーが利用されます。 ADOMD.NET と Analysis Services Management Objects (AMO) はマネージ データ プロバイダーです。 Analysis Services OLE DB プロバイダー (MSOLAP DLL) は、ネイティブ データ プロバイダーです。 通常、3 つのプロバイダーがすべて同時にインストールされます。 Azure Analysis Services では最新バージョンのデータ プロバイダーが必要です。 

Power BI Desktop や Excel などの Microsoft クライアント アプリケーションでは&3; つのデータ プロバイダーがすべてインストールされます。 ただし、Excel のバージョンや、Excel および Power BI Desktop の新バージョンが毎月更新されるかどうかなどによっては、インストールされたデータ プロバイダーが、Azure Analysis Service で必要な最新バージョンに更新されないことがあります。 これは、カスタム アプリケーションまたは AsCmd、TOM、ADOMD.NET など他のインターフェイスでも同様です。 これらのアプリケーションでは、プロバイダーを手動でインストールする必要があります。 手動でインストールするためのデータ プロバイダーは、再頒布可能パッケージとして SQL Server Feature Pack に含まれます。ただし、これらは SQL Server バージョンに対応しており、最新版ではない可能性があります。  

クライアント接続用のデータ プロバイダーは、Azure Analysis Services サーバーをデータ ソースに接続するために必要なデータ プロバイダーとは異なります。 データソース接続について詳しくは、「[Datasource connections](analysis-services-datasource.md)」(データソース接続) をご覧ください。
 
## <a name="download-the-latest-data-providers"></a>最新データ プロバイダーのダウンロード  

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>次のステップ
最新のデータ プロバイダーがインストールされていると、クライアント アプリケーションはサーバーに接続する準備ができています。 接続について詳しくは、「[Get data from Azure Analysis Services](analysis-services-connect.md)」(Azure Analysis Services からデータを取得する) をご覧ください。



<!--HONumber=Jan17_HO1-->


