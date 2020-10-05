---
title: Azure のエンクレーブの構成証明
description: 構成証明を利用し、コンフィデンシャル コンピューティングの信頼されている環境が安全であることを確認する方法について説明します
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/22/2020
ms.author: JenCook
ms.openlocfilehash: 70a17aacde67744eae74ca263200f2c65fbd300a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90993501"
---
# <a name="attesting-sgx-enclaves"></a>SGX エンクレーブの構成証明

Azure のコンフィデンシャル コンピューティングからは、コードまたはデータの一部を分離できる ntel SGX ベースの仮想マシンが与えられます。 このような[エンクレーブ](confidential-computing-enclaves.md)を使用するときは、信頼された環境が本当に安全であるかを検証しなければなりません。 この検証が構成証明のプロセスです。 

## <a name="overview"></a>概要 

証明書利用者は、構成証明を通じて、ソフトウェアが (1) エンクレーブで実行されていること、また (2) エンクレーブが最新かつ安全であることについて、より強い確信を得ることができます。 たとえばエンクレーブは、基になるハードウェアに対し、エンクレーブが確かにそのプラットフォーム上に存在することの証明を含んだ資格情報を生成するよう要求します。 そのレポートを別のエンクレーブに渡し、そこで、レポートが同じプラットフォーム上で生成されたことを確認できます。

![エンクレーブのコードを構成証明する](media/attestation/attestation.png)



構成証明は、システムのソフトウェアや半導体に適合した安全な構成証明サービスを使用して実装する必要があります。 使用できるサービスの例

- [Microsoft Azure Attestation (プレビュー)](https://docs.microsoft.com/azure/attestation/overview) または
- [Intel の構成証明およびプロビジョニング サービス](https://software.intel.com/sgx/attestation-services)


いずれも Azure コンフィデンシャル コンピューティング Intel SGX インフラストラクチャと互換性があります。 

## <a name="next-steps"></a>次のステップ
[エンクレーブ対応アプリ向け Microsoft Azure Attestation サンプル](https://docs.microsoft.com/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/)をお試しください。