---
title: SAP 用の Microsoft Azure の認定 | Microsoft Docs
description: 現在の構成と、Azure プラットフォーム上の SAP の認定の最新の一覧です。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: f2d342558e83c54e101e0ff9128611da9bec1e77
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656960"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Microsoft Azure で実行されているSAP の認定と構成

SAP および Microsoft は、お客様に対して相互にメリットを持つ強力なパートナーシップの中で互いに連携している長い歴史があります。 Microsoft では、定期的にプラットフォームを更新し、Microsoft Azure が SAP のワークロードを実行する最適なプラットフォームであることを保証するための新しい認定の詳細を SAP に提出しています。 次の表に、Azure がサポートする構成と、増加する SAP 認定資格の一覧を示します。 

## <a name="sap-hana-certifications"></a>SAP HANA 認定
参照:

- ネイティブな Azure VM と HANA L インスタンスの SAP HANA によるサポートに関する「[SAP HANA certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)」(SAP HANA 認定の IaaS プラットフォーム)。

| SAP 製品 | サポート対象 OS | Azure のサービス |
| --- | --- | --- |
| SAP HANA Developer Edition (SQLODBC、ODBO (Windows のみ)、ODBC、JDBC ドライバー、HANA Studio、HANA データベースで構成される HANA クライアント ソフトウェアを含む) | Red Hat Enterprise Linux、SUSE Linux Enterprise | D シリーズ VM ファミリ |
| Business One on HANA | SUSE Linux Enterprise | DS14_v2 |
| SAP S/4 HANA | Red Hat Enterprise Linux、SUSE Linux Enterprise | GS5、M64s、M64ms、M128s、M128ms、SAP HANA on Azure (L インスタンス) 用に制御された可用性 |
| Suite on HANA、OLTP | Red Hat Enterprise Linux、SUSE Linux Enterprise | 非運用シナリオ、M64s、M64ms、M128s、M128ms、SAP HANA on Azure (L インスタンス) 用の GS5 |
| HANA Enterprise for BW、OLAP | Red Hat Enterprise Linux、SUSE Linux Enterprise | GS5、M64s、M64ms、M128s、M128ms、SAP HANA on Azure (L インスタンス) |
| SAP BW/4 HANA | Red Hat Enterprise Linux、SUSE Linux Enterprise | GS5、M64s、M64ms、M128s、M128ms、SAP HANA on Azure (L インスタンス) |

今のところ、すべての Azure VM が SAP HANA のスケールアップに認定されています。

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver 認定
以下の SAP 製品に関して Microsoft Azure は認定を受けており、マイクロソフトと SAP から完全サポートを受けることができます。
参照:

- SAP TREX、SAP LiveCache、SAP Content Server を含むすべての SAP NetWeaver ベース アプリケーションに関する「[1928533 - SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533)」(1928533 - Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類)。 SAP HANA を除くすべてのデータベース。


| SAP 製品 | ゲスト OS | RDBMS | 仮想マシンの種類 |
| --- | --- | --- | --- |
| SAP Business Suite ソフトウェア | Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux、Oracle Linux |SQL Server、Oracle (Windows および Oracle Linux のみ)、DB2、SAP ASE |A5 から A11、D11 から D14、DS11 から DS14、DS11_v2 から DS15_v2、GS1 から GS5、D2s_v3 から D64s_v3、E2s_v3 から E64s_v3、M64s から M128ms |
| SAP Business All-in-One | Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux、Oracle Linux |SQL Server、Oracle (Windows および Oracle Linux のみ)、DB2、SAP ASE |A5 から A11、D11 から D14、DS11 から DS14、DS11_v2 から DS15_v2、GS1 から GS5、D2s_v3 から D64s_v3、E2s_v3 から E64s_v3、M64s から M128ms |
| SAP BusinessObjects BI | Windows |該当なし |A5 から A11、D11 から D14、DS11 から DS14、DS11_v2 から DS15_v2、GS1 から GS5、D2s_v3 から D64s_v3、E2s_v3 から E64s_v3、M64s から M128ms |
| SAP NetWeaver | Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux、Oracle Linux |SQL Server、Oracle (Windows および Oracle Linux のみ)、DB2、SAP ASE |A5 から A11、D11 から D14、DS11 から DS14、DS11_v2 から DS15_v2、GS1 から GS5、D2s_v3 から D64s_v3、E2s_v3 から E64s_v3、M64s から M128ms |

## <a name="other-sap-workload-supported-on-azure"></a>Azure でサポートされているその他の SAP ワークロード

| SAP 製品 | ゲスト OS | RDBMS | 仮想マシンの種類 |
| --- | --- | --- | --- |
| SAP Business One on SQL Server | Windows  | SQL Server | すべての種類の NetWeaver 認定 VM |
| SAP BPC 10.01 MS SP08 | Windows および Linux | | すべての種類の NetWeaver 認定 VM<br /> SAP Note #2451795 |
| SAP ビジネス オブジェクト BI プラットフォーム | Windows および Linux | | SAP Note #2145537 |
| SAP Data Services 4.2 | | | SAP Note #2288344 |
| SAP Hybris Commerce Platform 5.x および 6.x | Windows | SQL Server、Oracle | すべての種類の NetWeaver 認定 VM<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
