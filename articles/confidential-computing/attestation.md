---
title: SGX エンクレーブの構成証明
description: 構成証明を使用してコンフィデンシャル コンピューティングの SGX エンクレーブがセキュリティで保護されていることを確認します。
services: virtual-machines
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2020
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 03a389facebbfcb242cf63afd0a8fcca6ad7b670
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347672"
---
# <a name="attestation-for-sgx-enclaves"></a>SGX エンクレーブの構成証明

Azure のコンフィデンシャル コンピューティングからは、コードまたはデータの一部を分離できる Intel SGX ベースの仮想マシンが与えられます。 このような[エンクレーブ](confidential-computing-enclaves.md)を使用するときは、信頼された環境が本当に安全であることを確認および検証できます。 この検証が構成証明のプロセスです。 

## <a name="overview"></a>概要 

証明書利用者は、構成証明を通じて、ソフトウェアの以下の状態について、より強い確信を得ることができます。

1. エンクレーブで実行中
1. 最新の状態
1. セキュリティで保護

たとえば、あるエンクレーブでは、基になるハードウェアに対し資格情報を生成するよう要求できます。 この資格情報には、エンクレーブが確かにそのプラットフォーム上に存在することの証明が含まれています。 2 つ目のエンクレーブでは、レポートが同じプラットフォームで生成されたことを受信して確認できます。

![データとアプリケーション コードを保持するエンクレーブとのクライアントの安全な交換を示す構成証明プロセスの図。](media/attestation/attestation.png)

構成証明は、システムのソフトウェアや半導体に適合した安全な構成証明サービスを使用して実装します。 たとえば、次のように入力します。

- [Microsoft Azure Attestation](../attestation/overview.md) 
- [Intel の構成証明およびプロビジョニング サービス](https://software.intel.com/sgx/attestation-services)


どちらのサービスも Azure コンフィデンシャル コンピューティング Intel SGX DCsv2 シリーズの VM と互換性があります。 DCsv3 シリーズと DCdsv3 シリーズの VM は、Intel 構成証明サービスと互換性がありません。 

## <a name="next-step"></a>次のステップ

> [!div class="nextstepaction"]
> [エンクレーブ対応アプリ向け Microsoft Azure Attestation サンプル](/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/)
