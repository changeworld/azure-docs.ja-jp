---
title: "SAP 用の Microsoft Azure の認定 | Microsoft Docs"
description: "現在の構成と、Azure プラットフォーム上の SAP の認定の最新の一覧です。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2017
ms.author: rclaus
ms.custom: 
ms.openlocfilehash: 865fa54c908481b3f4c211f12293538c617b6129
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2017
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Microsoft Azure で実行されているSAP の認定と構成

SAP および Microsoft は、お客様に対して相互にメリットを持つ強力なパートナーシップの中で互いに連携している長い歴史があります。 Microsoft では、定期的にプラットフォームを更新し、Microsoft Azure が SAP のワークロードを実行する最適なプラットフォームであることを保証するための新しい認定の詳細を SAP に提出しています。 次の表に、サポート対象の構成と増加する認定資格の一覧を示します。 

## <a name="sap-hana-certifications"></a>SAP HANA 認定
参照:

- SAP HANA のサポートに関する HANA L インスタンスついて記載している「[SAP Note 2316233 - SAP HANA on Microsoft Azure (Large Instances)](https://launchpad.support.sap.com/#/notes/2316233)」(SAP Note 2316233 - SAP HANA on Microsoft Azure (L インスタンス))。
- ネイティブ Azure VM の SAP HANA サポートに関する「[SAP HANA certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Amazon%20Web%20Services%2CMicrosoft%20Azure)」(SAP HANA 認定の IaaS プラットフォーム)。

| SAP 製品 | サポート対象 OS | Azure のサービス |
| --- | --- | --- |
| SAP HANA Developer Edition (SQLODBC、ODBO (Windows のみ)、ODBC、JDBC ドライバー、HANA Studio、HANA データベースで構成される HANA クライアント ソフトウェアを含む) | Red Hat Enterprise Linux、SUSE Linux Enterprise | D シリーズ VM ファミリ |
| Business One on HANA | SUSE Linux Enterprise | DS14_v2 |
| SAP S/4 HANA |Red Hat Enterprise Linux、SUSE Linux Enterprise | GS5、SAP HANA on Azure (L インスタンス) 用に制御された可用性 |
| Suite on HANA、OLTP | Red Hat Enterprise Linux、SUSE Linux Enterprise | 非運用シナリオでの単一ノード デプロイメント用 GS5、SAP HANA on Azure (L インスタンス) |
| HANA Enterprise for BW、OLAP | Red Hat Enterprise Linux、SUSE Linux Enterprise | 単一ノード デプロイメント用 GS5、SAP HANA on Azure (L インスタンス) |
| SAP BW/4 HANA | Red Hat Enterprise Linux、SUSE Linux Enterprise | 単一ノード デプロイメント用 GS5、SAP HANA on Azure (L インスタンス) |

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver 認定
以下の SAP 製品に関して Microsoft Azure は認定を受けており、マイクロソフトと SAP から完全サポートを受けることができます。
参照:

- SAP TREX、SAP LiveCache、SAP Content Server を含むすべての SAP NetWeaver ベース アプリケーションに関する「[1928533 - SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533)」(1928533 - Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類)。 SAP HANA を除くすべてのデータベース。


| SAP 製品 | ゲスト OS | RDBMS | 仮想マシンの種類 |
| --- | --- | --- | --- |
| SAP Business Suite ソフトウェア |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux、Oracle Linux |SQL Server、Oracle (Windows および Oracle Linux のみ)、DB2、SAP ASE |A5 から A11、D11 から D14、DS11 から DS14、DS11_v2 から DS15_v2、GS1 から GS5、M シリーズ |
| SAP Business All-in-One |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux |SQL Server、Oracle (Windows および Oracle Linux のみ)、DB2、SAP ASE |A5 から A11、D11 から D14、DS11 から DS14、DS11_v2 から DS15_v2、GS1 から GS5、M シリーズ |
| SAP BusinessObjects BI |Windows |該当なし |A5 から A11、D11 から D14、DS11 から DS14、DS11_v2 から DS15_v2、GS1 から GS5、M シリーズ |
| SAP NetWeaver |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux |SQL Server、Oracle (Windows および Oracle Linux のみ)、DB2、SAP ASE |A5 から A11、D11 から D14、DS11 から DS14、DS11_v2 から DS15_v2、GS1 から GS5、M シリーズ |

## <a name="other-sap-workload-supported-on-azure"></a>Azure でサポートされているその他の SAP ワークロード

| SAP 製品 | ゲスト OS | RDBMS | 仮想マシンの種類 |
| --- | --- | --- | --- |
| SAP Business One on SQL Server | Windows  | SQL Server | すべての種類の NetWeaver 認定 VM |
| SAP BPC 10.01 MS SP08 | Windows | | すべての種類の NetWeaver 認定 VM<br /> SAP Note #2451795 |
| SAP ビジネス オブジェクト BI プラットフォーム | Windows | | SAP Note #2145537 |
| SAP Data Services 4.2 | | | SAP Note #2288344 |
| SAP Hybris Commerce Platform 5.x および 6.x | Windows | SQL Server、Oracle | すべての種類の NetWeaver 認定 VM<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
