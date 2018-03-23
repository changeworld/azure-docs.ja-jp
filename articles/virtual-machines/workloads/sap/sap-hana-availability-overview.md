---
title: Azure VM 上の SAP HANA の可用性 - 概要 | Microsoft Docs
description: SAP HANA on Azure ネイティブ VM の運用
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9b22f89750234a4715d2b7fd2df6ad8740b1d085
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-high-availability-guide-for-azure-virtual-machines"></a>Azure 仮想マシンの SAP HANA 高可用性ガイド
Azure には、Azure VM の SAP HANA など、ミッション クリティカルなデータベースのデプロイを可能にする機能が多数用意されています。 このドキュメントでは、Azure 仮想マシンでホストされている SAP HANA インスタンスの可用性を実現する方法について説明します。 ドキュメントでは、Azure の SAP HANA の可用性を高めるために Azure インフラストラクチャで実装できるシナリオをいくつか紹介します。 

## <a name="prerequisites"></a>前提条件
このガイドは、次のような Azure におけるサービスとしてのインフラストラクチャ (IaaS) の基本事項について理解していることを前提としています。 

- Azure Portal または PowerShell を使って仮想マシンまたは仮想ネットワークをデプロイする方法。
- JSON (JavaScript Object Notation) テンプレートを使うためのオプションなど、Azure クロスプラットフォーム コマンドライン インターフェイス (CLI)。

また、このガイドでは、SAP HANA インスタンスのインストール、および SAP HANA インスタンスの管理と操作について熟知している必要もあります。 特に、SAP HANA データベースのバックアップと復元など、HANA システム レプリケーション関連の設定や操作などです。

次の記事も、Azure の SAP HANA の概要についてわかりやすく説明しています。

- [Azure VM への単一インスタンスの SAP HANA の手動インストール](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Azure VM での SAP HANA システム レプリケーションのセットアップ](sap-hana-high-availability.md)
- [Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

SAP HANA について理解しておく必要がある記事とコンテンツを次に示します。

- [SAP HANA の高可用性](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [FAQ: SAP HANA の高可用性](https://archive.sap.com/documents/docs/DOC-66702)
- [SAP HANA のシステム レプリケーションを実行する方法](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SPS 01 の新機能: 高可用性](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [SAP HANA システム レプリケーションのネットワークに関する推奨事項](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [SAP HANA システム レプリケーション](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA サービスの自動再起動](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [SAP HANA システム レプリケーションの構成](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)


Azure での VM デプロイについて理解することも必要ですが、Azure でユーザーの可用性アーキテクチャの定義を進める前に、「[Azure での Windows 仮想マシンの可用性の管理](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)」を読むこともお勧めします。

## <a name="service-level-agreements-for-different-azure-components"></a>各種 Azure コンポーネントのサービス レベル アグリーメント
Azure には、ネットワーク、ストレージ、VM などのコンポーネントごとに可用性 SLA があります。 これらの SLA ドキュメントはすべて、[Microsoft Azure サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/) ページからご確認いただけます。 「[Virtual Machines の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)」を確認すると、Azure には 2 つの異なる構成に対して 2 つの異なる SLA が用意されていることがわかります。 SLA は次のように定義されています。

- OS ディスクおよびすべてのデータ ディスクについて、[Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) を使用する単一の VM については、99.9% の月間稼働率を確保する
- [Azure 可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)で構成されている複数 (少なくとも 2 つ) の VM については 99.95% の月間稼働率を確保する

可用性の要件と、Azure コンポーネントが提供できる SLA を比較しながら、必要な可用性を確保するために SAP HANA で実装する必要があるシナリオをそれぞれ決めてください。

## <a name="next-steps"></a>次の手順
次のドキュメントに進みます。

- [1 つの Azure リージョン内での SAP HANA の可用性](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Azure リージョンの枠を越えた SAP HANA の可用性](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 















  


