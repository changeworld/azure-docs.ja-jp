---
title: VMware ソフトウェアのバージョン
description: Azure VMware Solution でサポートされている VMware ソフトウェアのバージョン。
ms.topic: include
ms.date: 12/29/2020
ms.openlocfilehash: c6ba2904bab6c6f44001cafed1bd4cbdeb786373
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825100"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Azure VMware Solution プライベート クラウドのクラスターで使用されている VMware ソフトウェアの最新のソフトウェア バージョンは次のとおりです。

| ソフトウェア              |    Version   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6.7 U3    | 
| ESXi                  |    6.7 U3    | 
| vSAN                  |    6.7 U3    |
| NSX-T                 |      2.5     |


>[!NOTE]
>NSX-T が NSX のサポートされている唯一のバージョンです。

プライベート クラウド内の新しいクラスターの場合、ソフトウェア バージョンは、現在実行されているものと一致します。 サブスクリプション内の新しいプライベート クラウドの場合、ソフトウェア スタックの最新バージョンがインストールされます。 詳細については、[VMware ソフトウェアの要件](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)に関するページを参照してください。

プライベート クラウド ソフトウェア バンドルのアップグレードは、VMware のソフトウェア バンドルの最新リリースの単一バージョン内でソフトウェアを保持します。 プライベート クラウド ソフトウェアのバージョンは、個々のソフトウェア コンポーネント (ESXi、NSX-T、vCenter、vSAN) の最新バージョンとは異なる場合があります。 Azure VMware Solution プラットフォーム ソフトウェアの一般的なアップグレード ポリシーおよびプロセスについては、[プライベート クラウドの更新とアップグレード](../concepts-upgrades.md)に関する記事に記載されています。

