---
title: コンフィデンシャル コンピューティング用のコンテナー オファリングを選択する
description: セキュリティ、分離、開発者のニーズに合わせて適切な機密コンテナー オファリングを選択する方法。
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: amgowda
ms.custom: ignite-fall-2021
ms.openlocfilehash: 158e3e5a8191bc8e193ff9dab803feed7da2cc8a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017525"
---
# <a name="choosing-confidential-container-offerings"></a>機密コンテナー オファリングの選択

Azure Confidential Computing では、複数の種類の機密コンテナーが提供されます。 これらのコンテナーを使用して、データの整合性と機密性、およびコードの整合性をサポートできます。

機密コンテナーは、暗号化によるコード保護にも役立ちます。 ハードウェア ベースの保証と信頼のハードウェア ルートを作成できます。 また、機密コンテナーを使用して攻撃の対象となる領域を少なくすることができます。

## <a name="enclaves-confidential-containers"></a>エンクレーブ機密コンテナー

エンクレーブを使用して機密コンテナーをデプロイできます。 コンテナー デプロイのこの方法は、より小規模なトラステッド コンピューティング ベース (TCB) で、最も強力なセキュリティとコンピューティングの分離を実現します。 ハードウェア ベースの高信頼実行環境 (TEE) で実行される Intel Software Guard Extensions (SGX) に基づく機密コンテナーを使用できます。 これらのコンテナーは、既存のコンテナー アプリのリフト アンド シフトをサポートします。 もう 1 つのオプションは、エンクレーブ対応のカスタム アプリを構築する方法です。

Azure Kubernetes Service (AKS) には、2 つのプログラミングおよびデプロイ モデルがあります。 

**変更されていないコンテナー** は、OSS プロジェクトの Azure Partner エコシステムを通じて、Intel SGX 上のより高水準なプログラミング言語をサポートします。 詳細については、[変更されていないコンテナーのデプロイ フローとサンプル](./confidential-containers.md)を参照してください。

**エンクレーブ対応コンテナー** では、カスタムの Intel SGX プログラミング モデルを使用します。 詳細については、[エンクレーブ対応コンテナーのデプロイ フローとサンプル](./enclave-aware-containers.md)を参照してください。 

![Intel SGX を使用したエンクレーブ機密コンテナーの図。分離とセキュリティの境界を示しています。](./media/confidential-containers/confidential-container-intel-sgx.png)

## <a name="learn-more"></a>詳細情報

- [Azure 上の Intel SGX 機密仮想マシン](./virtual-machine-solutions-sgx.md)
- [Azure 上の機密コンテナー](./confidential-containers.md)。
