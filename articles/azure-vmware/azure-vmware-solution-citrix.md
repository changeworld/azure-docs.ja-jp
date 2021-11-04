---
title: Azure VMware Solution に Citrix をデプロイする
description: Azure VMware Solution に VMware Citrix をデプロイする方法について説明します。
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7d3acd7ac8772a3d6e501b43f7277bcb57d8cd08
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017394"
---
# <a name="deploy-citrix-on-azure-vmware-solution"></a>Azure VMware Solution に Citrix をデプロイする

Citrix Virtual Apps and Desktop サービスでは、Azure VMware Solution をサポートしています。 Azure VMware Solution では、Azure インフラストラクチャによって作成された vSphere クラスターを含むクラウド インフラストラクチャを提供します。 Citrix Virtual Apps and Desktop サービスを活用すると、Azure VMware Solution を使用して、オンプレミス環境で vSphere を使用する場合と同じ方法で、[Virtual Delivery Agent (VDA)](https://www.citrix.com/downloads/xendesktop/components/xendesktop-and-xenapp-76-VDA.html) ワークロードをプロビジョニングすることができます。 

[Citrix Virtual Apps and Desktop の詳細情報](https://www.citrix.com/products/citrix-virtual-apps-and-desktops/)

[デプロイ ガイド](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/azure-resource-manager.html#azure-vmware-solution-avs-integration)

[ソリューションの概要](https://www.citrix.com/content/dam/citrix/en_us/documents/solution-brief/citrix-virtual-apps-and-desktop-service-on-azure-vmware-solution.pdf)

**FAQ (Q&A の確認)**
 
- Q. 既存の Citrix デスクトップとアプリを Azure VMware Solution に移行したり、オンプレミスおよび Azure VMware Solution ベースの Citrix ワークロードで構成されるハイブリッド環境を運用したりすることはできますか? 

    A. はい。 現在使用しているのと同じコンピューター イメージ、アプリケーション パッケージ、プロセスを使用できます。 オンプレミスと Azure VMware Solution ベースの環境を移行のためにシームレスにリンクすることができます。

- Q. Azure VMware Solution 内でスタンドアロン環境として Citrix をデプロイできますか? 

    A. はい。 移行することも、ハイブリッド環境を運用することも、スタンドアロンを Azure VMware Solution に直接デプロイすることもできます 

- Q. Azure VMware Solution では PVS と MCS の両方がサポートされていますか? 

    A. はい 

- Q. Azure VMware Solution 上の Citrix では GPU ベースのワークロードはサポートされていますか? 

    A. 現時点ではありません。 ただし、Microsoft Azure 上の Citrix ワークロードでは、そのユース ケースが重要な場合に GPU がサポートされます。 

- Q. Azure VMware Solution は、オンプレミスの Citrix デプロイまたは LTSR でサポートされていますか?  

    A. いいえ。  Azure VMware Solution は、Citrix Virtual Apps and Desktops サービス オファリングでのみサポートされています。  

- Q. サポートに関する連絡先はどこですか? 

    A. お客様は、Citrix サポート www.citrix.com/support にサポートを依頼する必要があります。 

- Q. Azure VMware Solution 上の Citrix と共に、Microsoft の Azure Virtual Desktop の利点を使用できますか?
 
    A. いいえ。 Azure Virtual Desktop の利点は、ネイティブの Microsoft Azure ワークロードにのみ適用されます。 ネイティブの Azure オファリングである Citrix Virtual Apps and Desktops サービスでは、Azure VMware Solution のデプロイと共に Azure Virtual Desktop の特典を適用できます。 

- Q. Azure VMware Solution を使用するために、Citrix Virtual Apps and Desktops サービスを購入する方法は? 
    
    A. Citrix オファリングは、Citrix パートナーからか、Azure Marketplace から直接購入できます。 
