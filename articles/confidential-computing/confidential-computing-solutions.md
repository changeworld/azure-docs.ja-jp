---
title: Azure の機密性の高いソリューションの構築
description: Azure Confidential Computing でソリューションを構築する方法について説明します
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5d29f214f4ee50f672b47fe0b346f13e8b8fd49c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290250"
---
# <a name="building-confidential-computing-solutions"></a>コンフィデンシャル コンピューティング ソリューションの構築

Azure Confidential Computing では、機密性の高いソリューションを構築するためのさまざまなオプションを提供します。 オプションの範囲は、既存のアプリケーションの "リフト アンド シフト" シナリオを有効にすることから、セキュリティのさまざまな機能を完全に制御することに至ります。 これらの機能には、アクセス レベルの制御が含まれます。 ホスト プロバイダーまたはゲスト オペレーターのアクセス レベルをデータとコードに設定します。 また、クラウドで実行されているワークロードの整合性を損なう可能性のある他のルートキットまたはマルウェアのアクセスを制御することもできます。

## <a name="solutions"></a>解決策

セキュア エンクレーブや機密性の高い仮想マシンなどのテクノロジによって、お客様は、機密性の高いソリューションを構築する際に採用するアプローチを選択できます。

- ソース コードにアクセスできない既存のアプリケーションでは、Azure Confidential Computing プラットフォームへのオンボードを容易にするために、AMD SEV-SNP テクノロジに基づく機密性の高い VM の恩恵を受けることがあります。
- 任意の信頼ベクトルから保護するための専用コードを含む高度なワークロードでは、セキュリティで保護されたアプリケーション エンクレーブ テクノロジのメリットが得られる可能性があります。 現在 Azure では、Intel SGX に基づいて VM でアプリケーション エンクレーブを提供しています。 Intel SGX では、ハードウェアで暗号化されたメモリ領域で実行されるデータとコードを保護します。 通常、これらのアプリケーションでは、オープンソース フレームワークを使用して取得される、構成証明済みのセキュア エンクレーブとの通信が必要です。
- Azure Kubernetes Service で有効になっている[機密コンテナー](confidential-containers.md)で実行されているコンテナー化されたソリューションは、バランスの取れた機密性のアプローチを求めているお客様に適している可能性があります。 このようなシナリオでは、限定的な変更で既存のアプリをパッケージ化してコンテナーにデプロイしながら、クラウド サービス プロバイダーと管理者からの完全なセキュリティの分離を実現できます。

![機密性の高いコンピューティング範囲のスクリーンショット。ほとんどのセキュリティ コントロールでより簡単なオプションが示されています。](media/confidential-computing-solutions/spectrum.png)

## <a name="learn-more"></a>詳細情報

エンクレーブおよび隔離された環境の利点を使用するには、コンフィデンシャル コンピューティングをサポートするツールを使用する必要があります。 エンクレーブ アプリケーション開発をサポートするさまざまなツールが存在します。 詳細については、[エンクレーブ アプリケーション開発](application-development.md)に関するページを参照してください。 

仮想マシンの Intel SGX エンクレーブ アプリケーション用ソリューションを構築するための[オープンソース ツール](enclave-development-oss.md)について説明します。

[機密コンテナー用のパートナーとオープンソース ツール](confidential-containers.md)を使用します。 これらのツールの一部を Azure Kubernetes ワークロードに使用することもできます。

## <a name="next-steps"></a>次のステップ

- [アプリケーション エンクレーブの開発について学習する](application-development.md)
