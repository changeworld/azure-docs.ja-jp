---
title: "Azure Analysis Services への接続に必要なクライアント ライブラリ | Microsoft Docs"
description: "Azure Analysis Services に接続するクライアント アプリケーションとツールに必要なクライアント ライブラリについて説明します。"
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
ms.date: 04/14/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 71f85c483f6b87ac34c003ac3695a85396ca945b
ms.lasthandoff: 04/18/2017


---

# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Azure Analysis Services に接続するためのクライアント ライブラリ

クライアント アプリケーションとツールが Analysis Services サーバーに接続するためには、クライアント ライブラリが必要です。 

Analysis Services では、3 つのクライアント ライブラリが利用されています。 ADOMD.NET と Analysis Services 管理オブジェクト (AMO) は、マネージ クライアント ライブラリです。 Analysis Services OLE DB プロバイダー (MSOLAP DLL) は、ネイティブ クライアント ライブラリです。 通常は、3 つすべてが同時にインストールされます。 Azure Analysis Services では、これらの最新バージョンが必要となります。 

Power BI Desktop や Excel などの Microsoft クライアント アプリケーションでは、3 つのクライアント ライブラリがすべてインストールされます。 ただし、Excel のバージョンや、Excel および Power BI Desktop の新バージョンが毎月更新されるかどうかなどによっては、インストールされたクライアント ライブラリが、Azure Analysis Service で必要な最新バージョンに更新されないことがあります。 これは、カスタム アプリケーションまたは AsCmd、TOM、ADOMD.NET など他のインターフェイスでも同様です。 これらのアプリケーションでは、ライブラリを手動でインストールする必要があります。 手動でインストールするためのクライアント ライブラリは、再頒布可能パッケージとして SQL Server Feature Pack に含まれています。ただし、これらは SQL Server のバージョンに対応しており、最新版ではない可能性があります。  

クライアント接続用のクライアント ライブラリは、Azure Analysis Services サーバーをデータ ソースに接続するために必要なデータ プロバイダーとは異なります。 データソース接続について詳しくは、「[Datasource connections](analysis-services-datasource.md)」(データソース接続) をご覧ください。

## <a name="download-the-latest-preview-client-libraries"></a>最新**プレビュー** クライアント ライブラリのダウンロード  
次のクライアント ライブラリを使用して、最新のバグ修正と更新プログラムを取得します。 Azure Analysis Services プレビューまたは SQL Server vNext Analysis Services に接続するときには、これらのクライアント ライブラリをお勧めします。

[MSOLAP (amd64) プレビュー](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/amd64/SQL_AS_OLEDB.msi)</br>
[MSOLAP (x86) プレビュー](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/x86/SQL_AS_OLEDB.msi)</br>
[AMO プレビュー](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_AMO.msi)</br>
[ADOMD プレビュー](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_ADOMD.msi)</br>

## <a name="download-the-latest-rtm-client-libraries"></a>最新 **RTM** クライアント ライブラリのダウンロード  
運用環境で、完全にリリースおよびサポートされるバージョンが必要な場合は、次のクライアント ライブラリを使用します。

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>次のステップ
[Azure Analysis Services サーバーに接続する](analysis-services-connect.md)。

