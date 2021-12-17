---
title: SGX エンクレーブを使用して構築する - Azure Virtual Machines
description: コンフィデンシャル コンピューティング ワークロードを可能にする Intel SGX ハードウェアについて説明します。
author: stempesta
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3ac8c9ddced2fd37c829277c925b8953e7c71151
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310017"
---
# <a name="build-with-sgx-enclaves"></a>SGX エンクレーブを使用して構築する 

Azure コンフィデンシャル コンピューティングでは、[DCsv2 シリーズ](../virtual-machines/dcv2-series.md)と [DCsv3/DCdsv3 シリーズ](../virtual-machines/dcv3-series.md)*の仮想マシン (VM) が提供されています。 これらの VM には、[Intel® Software Guard Extensions (SGX)](https://intel.com/sgx) が装備されています。 

Intel SGX テクノロジを使用すると、お客様はデータを保護するエンクレーブを作成し、CPU でデータが処理されている間もデータの暗号化を維持できます。 オペレーティング システム (OS) とハイパーバイザーは、データにアクセスできません。 物理的にアクセスできるデータ センターの管理者も、データにはアクセスできません。

## <a name="enclaves-concept"></a>エンクレーブの概念

エンクレーブは、ハードウェアのプロセッサやメモリの、セキュリティで保護された部分です。 デバッガーを使用しても、エンクレーブ内のデータやコードを見ることはできません。 信頼されていないコードによってエンクレーブ メモリ内のコンテンツの変更が試みられた場合、SGX により環境が無効にされて、操作は拒否されます。 これらの固有の機能は、シークレットが暗号化されていない状態でアクセスできるようになるのを防ぐのに役立ちます。  

![エンクレーブでセキュリティ保護されたデータを示す VM モデルの図。](media/overview/hardware-backed-enclave.png)

エンクレーブは、セキュリティで保護されたロックボックスと考えることができます。 暗号化されたコードとデータをロックボックスに格納します。 外側からは何も見えません。 エンクレーブには、データを解読するためのキーを提供します。 データを返送する前に、エンクレーブによってデータが処理されて再暗号化されます。

各エンクレーブには、設定されたサイズの暗号化されたページ キャッシュ (EPC) があります。 EPC によって、エンクレーブが保持できるメモリの量が決まります。 [DCsv2 シリーズ](../virtual-machines/dcv2-series.md)の VM では、最大 168 MiB が保持されます。 [DCsv3/DCdsv3 シリーズ](../virtual-machines/dcv3-series.md)*の VM では、メモリを集中的に使用するワークロードにために最大 256 GB が保持されます。

> [!NOTE]
> \* DCsv3 と DCdsv3 は、2021 年 11 月 1 日現在、**パブリック プレビュー** 段階です。

詳しくは、[ハードウェア ベースの信頼されたエンクレーブを使用して Intel SGX VM をデプロイする方法](virtual-machine-solutions-sgx.md)に関するページをご覧ください。

## <a name="developing-for-enclaves"></a>エンクレーブ向けの開発

[エンクレーブで実行されるアプリケーションの開発には、さまざまなソフトウェア ツール](application-development.md)を使用できます。 これらのツールは、エンクレーブ内のコードとデータの一部をシールドするのに役立ちます。 信頼できる環境の外部にいるユーザーが、これらのツールでデータを表示または変更できないことを確認してください。

## <a name="next-steps"></a>次の手順
- [DCsv2 または DCsv3/DCdsv3 シリーズの仮想マシンをデプロイする](quick-create-portal.md)
- OE SDK を使用して、[エンクレーブ対応アプリケーションを開発する](application-development.md)
