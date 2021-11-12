---
title: Azure のコンフィデンシャル コンピューティング開発ツール
description: ツールとライブラリを使用し、Intel SGX でのコンフィデンシャル コンピューティング用のアプリケーションを開発する
services: virtual-machines
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5faf8807047d0e73029d27f62af8acaab5e80026
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347653"
---
# <a name="application-enclave-development"></a>アプリケーション エンクレーブの開発 

Azure Confidential Computing を使用すると、Intel Software Guard Extensions (SGX) を実行する仮想マシン (VM) 用のアプリケーション エンクレーブを作成できます。 開発を開始する前に、関連するツールとソフトウェアについて理解しておくことが重要です。

> [!NOTE]
> [Intel SGX VM とエンクレーブの概要](confidential-computing-enclaves.md)に関するページをまだ読んでいない場合は、続行する前にお読みください。

## <a name="application-enclaves"></a>アプリケーション エンクレーブ

アプリケーション エンクレーブは、特定のコードとデータを保護する分離された環境です。 エンクレーブを作成するときは、アプリケーションのどの部分がエンクレーブ内で実行されるかを決定する必要があります。 エンクレーブを作成または管理する場合は、選択したデプロイ スタックに応じた互換性のある SDK とフレームワークを使用してください。 

現在、コンフィデンシャル コンピューティングにはアプリケーション エンクレーブが提供されています。 具体的には、[Intel SGX が有効になっているコンフィデンシャル VM](virtual-machine-solutions-sgx.md) を使用して、アプリケーション エンクレーブでのデプロイと開発を行うことができます。 

## <a name="intel-sgx"></a>Intel SGX

Intel SGX テクノロジを使用すると、CPU 内に格納されるアクセスできないキーを使用して、アプリケーション エンクレーブを暗号化できます (高信頼実行環境)。 エンクレーブ内のコードとデータの暗号化解除は、プロセッサ内部で行われます。 CPU からのアクセスのみが可能です。 このレベルの分離により、使用中のデータが保護され、ハードウェアとソフトウェアの両方の攻撃から保護されます。 詳細については、[Intel SGX の Web サイト](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html)を参照してください。 

Azure には、DC シリーズのさまざまな VM サイズにおいて、仮想化環境での Intel SGX が提供されています。 複数の VM サイズで、さまざまなエンクレーブ ページ キャッシュ (EPC) のサイズを使用できます。 EPC は、その VM 上のエンクレーブのメモリ領域の最大量です。 現時点では、Intel SGX VM は [DCsv2 シリーズ](../virtual-machines/dcv2-series.md)の VM と [DCsv3 および DCdsv3 シリーズ](../virtual-machines/dcv3-series.md)の VM で利用できます。


### <a name="developing-applications"></a>アプリケーションの開発

エンクレーブを使用して構築されたアプリケーションには、2 つのパーティションがあります。 

**ホスト** は、"信頼されていない" コンポーネントです。 エンクレーブ アプリケーションはホスト上で実行されます。 ホストは信頼されていない環境です。 ホストにエンクレーブ コードをデプロイすると、ホストからそのコードにはアクセスできません。

**エンクレーブ** は、"信頼された" コンポーネントです。 アプリケーション コードと、キャッシュされたそのデータおよびメモリはエンクレーブ内で実行されます。 エンクレーブ環境を使用すると、シークレットと機密データが保護されます。 セキュリティで保護された計算がエンクレーブ内で行われるようにします。

![ホストとエンクレーブ パーティションを示すアプリケーションの図。 エンクレーブの内部には、データとアプリケーション コードのコンポーネントがあります。](media/application-development/oe-sdk.png)

エンクレーブおよび分離された環境の利点を活用するには、コンフィデンシャル コンピューティングをサポートするツールを使用します。 さまざまなツールでエンクレーブ アプリケーション開発がサポートされています。 たとえば、次のオープンソース フレームワークを使用できます。 

- [Open Enclave Software Development Kit (OE SDK)](enclave-development-oss.md#oe-sdk)
- [Intel SGX SDK](enclave-development-oss.md#intel-sdk)
- [EGo Software Development Kit](enclave-development-oss.md#ego)
- [Confidential Consortium Framework (CCF)](enclave-development-oss.md#ccf)

アプリケーションを設計するときに、エンクレーブで実行する必要のある部分を特定して決定します。 信頼されたコンポーネントのコードは、アプリケーションの他の部分から分離されています。 エンクレーブが初期化され、コードがメモリに読み込まれると、信頼されていないコンポーネントはそのコードを読み取りまたは変更できなくなります。

## <a name="next-steps"></a>次のステップ 

- [コンフィデンシャル コンピューティング Intel SGX VM をデプロイする](quick-create-portal.md)
- [オープンソース ソフトウェアを使用したアプリケーション開発の開始](enclave-development-oss.md)
