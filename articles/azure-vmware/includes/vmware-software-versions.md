---
title: VMware ソフトウェアのバージョン
description: Azure VMware Solution でサポートされている VMware ソフトウェアのバージョン。
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: a6441b55bbc6a8f694c50bbf022a6a2ae52d60bf
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106098082"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Azure VMware Solution のプライベート クラウドのクラスターの新しいデプロイで使用される VMware ソフトウェアのバージョンは次のとおりです。

| ソフトウェア              |    Version   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6.7 U3l    | 
| ESXi                  |    6.7 U3l    | 
| vSAN                  |    6.7 U3l    |
| NSX-T <br />**注意:** NSX-T が NSX のサポートされている唯一のバージョンです。               |      3.1.1     |


新しいクラスターが既存のプライベート クラウドに追加され、現在実行中のソフトウェア バージョンが適用されます。 詳細については、[VMware ソフトウェアの要件](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)に関するページを参照してください。

プライベート クラウド ソフトウェア バンドルのアップグレードは、VMware のソフトウェア バンドルの最新リリースの単一バージョン内でソフトウェアを保持します。 プライベート クラウド ソフトウェアのバージョンは、個々のソフトウェア コンポーネント (ESXi、NSX-T、vCenter、vSAN) の最新バージョンとは異なる場合があります。 Azure VMware Solution プラットフォーム ソフトウェアの一般的なアップグレード ポリシーおよびプロセスについては、[プライベート クラウドの更新とアップグレード](../concepts-upgrades.md)に関する記事に記載されています。

