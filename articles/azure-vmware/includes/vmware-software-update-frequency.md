---
title: VMware ソフトウェアの更新頻度
description: Azure VMware Solution でサポートされている VMware ソフトウェアの更新頻度。
ms.topic: include
ms.date: 03/22/2021
ms.openlocfilehash: a3290ed704b493fae3e86223857ff9f1c5617f1a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869865"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

Microsoft は、Azure VMware Solution プライベート クラウド内の VMware ソフトウェア (ESXi、vCenter、PSC、NXS) のライフサイクル管理を担います。

プライベート クラウド ソフトウェアは、VMware からのソフトウェア バンドルのリリースに合わせたスケジュールでアップグレードされます。 プライベート クラウドでは、アップグレードのためのダウンタイムは必要ありません。

プライベート クラウド ソフトウェア バンドルのアップグレードは、VMware のソフトウェア バンドルの最新リリースの単一バージョン内でソフトウェアを保持します。 プライベート クラウド ソフトウェアのバージョンは、個々のソフトウェア コンポーネント (ESXi、NSX-T、vCenter、vSAN) の最新バージョンとは異なる場合があります。

ソフトウェア更新の内容:

- **パッチ** - VMware によってリリースされたセキュリティ パッチまたはバグ修正プログラム
- **更新** - VMware スタック コンポーネントのマイナー バージョン変更
- **更新** - VMware スタック コンポーネントのメジャー バージョン変更

VMware から重要なセキュリティ パッチが利用できるようになると、Microsoft で直ちにそのテストが行われます。

次回スケジュールされている更新プログラムがデプロイされるまでは、対応する修正プログラムをインストールする代わりに、ドキュメントに記載されている VMware の回避策が実装されます。 
