---
title: Batch のレンダリング アプリケーション
description: 事前インストールされている Batch のレンダリング アプリケーション
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 28acd1b7275694d38a52f14d2b2c32b79cc8183e
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40036808"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>VM イメージをレンダリングするために事前インストールされているアプリケーション

Azure Batch では任意のレンダリング アプリケーションを使用できます。 ただし、Azure Marketplace の VM イメージは、事前インストールされている一般的なアプリケーションで使用します。

該当する場合、事前インストールされているレンダリング アプリケーションを従量課金制ライセンスで使用できます。 Batch のプールが作成されたら、必要なアプリケーションを指定し、1 分ごとに VM とアプリケーションの両方の料金が発生します。 アプリケーションの料金は、[Azure Batch の料金ページ](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)で確認できます。

一部のアプリケーションは Windows でのみサポートされますが、ほとんどは Windows と Linux の両方でサポートされます。

## <a name="applications-on-centos-7-rendering-nodes"></a>CentOS 7 レンダリング ノード上のアプリケーション

* Autodesk Maya I/O 2017 Update 5 (cut 201708032230)
* Autodesk Maya I/O 2018 Update 2 (cut 201711281015)
* Autodesk Arnold for Maya 2017 (Arnold バージョン 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya 2018 (Arnold バージョン 5.0.1.4) MtoA-2.1.0.3-2018
* Chaos Group V-Ray for Maya 2017 (バージョン 3.60.04)
* Chaos Group V-Ray for Maya 2018 (バージョン 3.60.04)
* Blender (2.68)

## <a name="applications-on-windows-server-2016-rendering-nodes"></a>Windows Server 2016 レンダリング ノード上のアプリケーション

* Autodesk Maya I/O 2017 Update 5 (バージョン 17.4.5459)
* Autodesk Maya I/O 2018 Update 3 (バージョン 18.3.0.7040)  
* Autodesk 3ds Max I/O 2019 Update 1 (バージョン 21.10.1314)
* Autodesk 3ds Max I/O 2018 Update 4 (バージョン 20.4.0.4254)
* Autodesk Arnold for Maya (Arnold バージョン 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya (Arnold バージョン 5.0.1.4) MtoA-2.0.2.3-2018
* Autodesk Arnold for 3ds Max (Arnold バージョン 5.0.2.4)(バージョン 1.2.926)
* Chaos Group V-Ray for Maya (バージョン 3.52.03)
* Chaos Group V-Ray for 3ds Max (バージョン 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>次の手順

レンダリング VM イメージを使用するには、プール作成時にプールの構成で指定する必要があります。詳しくは、[Batch プールのレンダリング機能](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools)に関するページをご覧ください。