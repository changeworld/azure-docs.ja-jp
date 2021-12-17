---
title: Azure Confidential Computing でオープンソース ソリューションを使用してアプリケーション エンクレーブを開発する
description: ツールを使用して Azure Confidential Computing 用の Intel SGX アプリケーションを開発する方法について説明します。
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 71be688b3f69860468ce6c638e484caae0ba72f5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290212"
---
# <a name="open-source-solutions-to-build-enclave-applications"></a>エンクレーブ アプリケーションを構築するためのオープンソース ソリューション

この記事では、アプリケーション エンクレーブを使用するアプリケーションを構築するためのオープンソース ソリューションについて説明します。 読む前に、[エンクレーブ アプリケーション](application-development.md)の概念に関するページを必ずお読みください。 

## <a name="intel-sgx-compatible-tools"></a>Intel SGX 互換ツール
Azure では、[Intel Software Guard Extensions (SGX) が有効になっている機密仮想マシン](virtual-machine-solutions-sgx.md)を介してアプリケーション エンクレーブを提供しています。 Intel SGX 仮想マシンをデプロイした後、アプリケーションを "エンクレーブ対応" にするための特別なツールが必要です。 これにより、コードの信頼された部分と信頼されていない部分の両方を持つアプリケーションを構築できます。 

たとえば、次のオープンソース フレームワークを使用できます。 

- [Open Enclave (OE) Software Development Kit (SDK)](#oe-sdk)
- [EGo SDK](#ego)
- [Intel SGX SDK](#intel-sdk)
- [Confidential Consortium Framework (CCF)](#ccf)

新しいアプリケーション コードを記述しない場合は、[機密コンテナーのイネーブラー](confidential-containers.md)を使用してコンテナー化されたアプリケーションをラップできます。

### <a name="open-enclave-software-development-kit-oe-sdk"></a>Open Enclave Software Development Kit (OE SDK) <a id="oe-sdk"></a>

エンクレーブ内で実行されるコードを記述するには、ご利用のプロバイダーによってサポートされているライブラリまたはフレームワークを使用します。 [Open Enclave SDK](https://github.com/openenclave/openenclave) (OE SDK) は、コンフィデンシャル コンピューティングに対応したさまざまなハードウェアに対する抽象化を可能にするオープンソースの SDK です。 

OE SDK は、あらゆる CSP のハードウェアに対する単一の抽象化レイヤーとして構築されています。 Azure Confidential Computing 仮想マシン上で OE SDK を使用して、エンクレーブ上にアプリケーションを作成、実行することができます。 Open Enclave リポジトリは、Microsoft によって保守されています。

### <a name="ego-software-development-kit"></a>EGo Software Development Kit <a id="ego"></a>

[EGo](https://ego.dev/) は、Go プログラミング言語で記述されたアプリケーションをエンクレーブ内で実行できるようにするオープンソース SDK です。 EGo は OE SDK の上に構築され、構成証明と封印用のエンクレーブ内 Go ライブラリが付属しています。 既存の多くの Go アプリケーションは、変更せずに EGo 上で実行されます。  

### <a name="intel-sgx-software-development-kit"></a>Intel SGX Software Development Kit <a id="intel-sdk"></a>
[Intel SGX SDK](https://01.org/intel-softwareguard-extensions) は、Intel の SGX チームによって開発および保守されています。 SDK は、ソフトウェア開発者が C/C++ で Intel SGX 対応アプリケーションを作成およびデバッグできるツールのコレクションです。

### <a name="confidential-consortium-framework-ccf"></a>Confidential Consortium Framework (CCF) <a id="ccf"></a>

[Confidential Consortium Framework](https://www.microsoft.com/research/project/confidential-consortium-framework/) ([CCF](https://www.microsoft.com/research/project/confidential-consortium-framework/)) は、分散ブロックチェーン フレームワークの一例です。 CCF は、Azure Confidential Computing を基盤として構築されています。 Microsoft Research が指揮を執るこのフレームワークは、高信頼実行環境 (TEE) の機能を使用して、構成証明用のリモート エンクレーブのネットワークを作成します。 ノードは Azure Intel SGX 仮想マシン上で動作でき、エンクレーブ インフラストラクチャを活用することができます。 ブロックチェーンのユーザーは、構成証明プロトコルを通じて 1 つの CCF ノードの整合性を検証し、実質上、ネットワーク全体を検証することができます。

CCF では、すべてのネットワーク ノードにわたってレプリケートされるキー値ストアに記録された変更によって分散台帳が成り立っています。 それらの各ノードでは、ブロックチェーンのユーザーが TLS 経由でトリガーできるトランザクション エンジンが稼働します。 エンドポイントをトリガーすると、キー値ストアが変化します。 暗号化された変更は、分散台帳に記録される前に、複数のノードの合意を得て合意形成されなければなりません。

## <a name="next-steps"></a>次のステップ

- [コンフィデンシャル コンピューティング Intel SGX 仮想マシンをデプロイする](quick-create-portal.md)
