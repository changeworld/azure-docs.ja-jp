---
title: "Visual Studio の Azure Stream Analytics ツールのインストール手順 | Microsoft Docs"
description: "Visual Studio の Azure Stream Analytics ツールのインストール手順"
keywords: Visual Studio
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/19/2017
ms.author: sujie
ms.openlocfilehash: 88abf40cefaca150c67e3a1068006fb0fa254305
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="installation-instructions-for-stream-analytics-tools-for-visual-studio"></a>Visual Studio の Stream Analytics ツールのインストール手順
Stream Analytics ツールでは、Visual Studio 2017、2015、2013 がサポートされるようになりました。 このドキュメントでは、ツールのインストールとアンインストールの方法を説明します。

[Visual Studio の Stream Analytics ツールの使用方法](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio)

## <a name="install"></a>インストール
### <a name="visual-studio-2017"></a>Visual Studio 2017
* [Visual Studio 2017 (15.3 以降)](https://www.visualstudio.com/) をダウンロードします。 サポートされるエディションは Enterprise (Ultimate/Premium)、Professional、Community です。Express エディションはサポートされません。 
* Stream Analytics ツールは、Visual Studio 2017 の [Azure の開発] および [データの保存と処理] ワークロードの一部です。 Visual Studio のインストールの一環として、この 2 つのワークロードのいずれかを有効にします。

![Visual Studio の Stream Analytics ツールのインストール 1](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)
![Visual Studio の Stream Analytics ツールのインストール 2](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013、2015
* Visual Studio 2015、Visual Studio 2013 Update 4 をインストールします。 サポートされるエディションは Enterprise (Ultimate/Premium)、Professional、Community です。Express エディションはサポートされません。 
* [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx) を使用して、Microsoft Azure SDK for .NET バージョン 2.7.1 以上をインストールします。
* [Visual Studio の Azure Stream Analytics ツール](http://aka.ms/asatoolsvs)をインストールします。



## <a name="update"></a>更新

### <a name="visual-studio-2017"></a>Visual Studio 2017
新しいバージョンの通知が Visual Studio の通知に表示されます。 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013、2015
インストールされている Visual Studio の Stream Analytics ツールによって新しいバージョンが自動的に検査され、ポップアップ ウィンドウの指示に従って最新バージョンをインストールできます。 


## <a name="uninstall"></a>アンインストール

### <a name="visual-studio-2017"></a>Visual Studio 2017
Visual Studio インストーラーをダブルクリックし、**[変更]** を選択します。 **[データの保存と処理]** または **[Azure の開発]** ワークロードから、**[Azure Data Lake および Stream Analytics ツール]** をオフにします。

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013、2015
コントロール パネルに移動し、'Microsoft Azure Data Lake and Stream Analytics tools for Visual Studio' をアンインストールします。





