---
title: "Azure 診断構成スキーマのバージョンの一覧 |Microsoft Docs"
description: "Azure Virtual Machines、VM Scale Sets、Service Fabric、および Cloud Services のパフォーマンス カウンターの収集を構成するために使用します。"
services: multiple
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 8c9c9dea1248205aa6303e11e1166d5d38786c1b
ms.openlocfilehash: 2e1bc45b55cd79af8579a5ddaf43cf0db019c92f


---
# <a name="list-of-azure-diagnostics-versions"></a>Azure 診断のバージョンの一覧
このページでは、Microsoft Azure SDK に付属する Azure 診断スキーマのバージョン一覧を示します。  

> [!NOTE]
> Azure 診断は、Azure Virtual Machines、Virtual Machine Scale Sets、Service Fabric、および Cloud Services からパフォーマンス カウンターやその他の統計情報を収集するために使用するコンポーネントです。  このページは、これらのサービスのいずれかを使用している場合にのみ該当します。
>

Azure 診断は、Azure Monitor、Application Insights、Log Analytics など、他の Microsoft 診断製品と組み合わせて使用します。

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure SDK 診断バージョン配布グラフ  

|Azure SDK のバージョン | Azure 診断のバージョン | モデル|  
|------------------|---------------------------|------|  
|1.x               |1.0                         | プラグイン|  
|2.0 ～ 2.4         |1.0                         |"|  
|2.5               |1.2                         |拡張機能|  
|2.6               |1.3                         |"|  
|2.7               |1.4                         |"|  
|2.8               |1.5                         |"|  

 Azure 診断バージョン 1.0 は最初はプラグイン モデルに付属しており、Azure SDK をインストールすると、その SDK に付属する Azure 診断バージョンを入手できました。  

 SDK 2.5 (診断バージョン 1.2) 以降、Azure 診断は拡張機能モデルに移行しています。 新しい機能を利用するツールは新しい Azure SDK でのみ使用できましたが、診断を使用するクラウド サービスや仮想マシンはすべて、最新バージョンを Azure から直接選択します。  

 たとえば、まだ SDK 2.5 を使用しているすべてのユーザーが、新しい機能を使用しているかどうかに関係なく、診断 1.5 を読み込みます。  

## <a name="azure-diagnostics-schemas-index"></a>Azure 診断スキーマのインデックス  
[診断 1.0 構成スキーマ](azure-diagnostics-schema-1dot0.md)  

[診断 1.2 構成スキーマ](azure-diagnostics-schema-1dot2.md)  

[診断 1.3 ～ 1.5 構成スキーマ](azure-diagnostics-schema-1dot3-to-1dot5.md)  



<!--HONumber=Jan17_HO5-->


