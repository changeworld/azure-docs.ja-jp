---
title: デプロイ モデルの選択
description: デプロイ モデルの選択
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/04/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8ed7f4b88e38581bea6eaf1a19ce945858031f1d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347691"
---
# <a name="confidential-computing-deployment-models"></a>コンフィデンシャル コンピューティング デプロイ モデル

Azure Confidential Computing では、複数のデプロイ モデルがサポートされています。 これらの異なるモデルでは、最新のクラウド コンピューティングに対するさまざまな顧客セキュリティ要件をサポートしています。

## <a name="infrastructure-as-a-service-iaas"></a>サービスとしてのインフラストラクチャ (IaaS)

サービスとしてのインフラストラクチャ (IaaS) では、コンフィデンシャル コンピューティングで機密仮想マシン (VM) を使用できます。 [Intel Software Guard Extensions (SGX) アプリケーション エンクレーブ](confidential-computing-enclaves.md)または AMD SEV-SNP テクノロジに基づく VM を使用できます。

## <a name="platform-as-a-service-paas"></a>サービスとしてのプラットフォーム (PaaS)

サービスとしてのプラットフォーム (PaaS) では、コンフィデンシャル コンピューティングで[機密コンテナー](confidential-containers.md)を使用できます。 このオファリングには、Azure Kubernetes Service (AKS) のエンクレーブ対応コンテナーが含まれています。

適切なデプロイ モデルの選択は、多くの要因によって異なります。 レガシ アプリケーション、オペレーティング システム機能、オンプレミス ネットワークからの移行の存在を考慮する必要がある場合があります。

VM を使用することには依然として多くの理由がありますが、コンテナーは最新の IT の多くのソフトウェア環境に対して柔軟性を高めます。 

コンテナーでは、次のアプリをサポートできます。

- 複数のクラウドで実行します。
- マイクロサービスに接続します。
- さまざまなプログラミング言語とフレームワークを使用します。
- 継続的インテグレーションと継続的配置 (CI/CD) の実装を含む、自動化と Azure Pipeline を使用します。

コンテナーでは、Azure クラウドの弾力性を適用することで、アプリケーションの移植性が向上し、リソースの使用状況が向上します。

通常、次の場合は、機密 VM にソリューションをデプロイできます。

- 変更またはコンテナー化できないレガシ アプリケーションがあります。 ただし、データの処理中に、メモリ内のデータを保護する必要があります。
- 1 つのインフラストラクチャで異なるオペレーティング システム (OS) を必要とする複数のアプリケーションを実行しています。
- すべての OS リソースを含め、コンピューティング環境全体をエミュレートする必要があります。
- 既存の VM をオンプレミスから Azure に移行しています。

次の場合は、機密コンテナー ベースのアプローチを選択できます。

- コストとリソースの割り当てについて不安があります。 ただし、独自のアプリとデータセットをデプロイするには、よりアジャイルなプラットフォームが必要です。
- 最新のクラウドネイティブ ソリューションを構築しています。 また、ソース コードとデプロイ プロセスのフル コントロールがあります。
- マルチクラウドのサポートが必要です。

どちらのオプションも、Azure サービスの最高のセキュリティ レベルが提供されます。 次のように、[機密 VM](#confidential-vms-on-amd-sev-snp) と[機密コンテナー](#secure-enclaves-on-intel-sgx)のセキュリティ対策にはいくつかの違いがあります。

### <a name="confidential-vms-on-amd-sev-snp"></a>AMD SEV-SNP 上の機密 VM

**AMD SEV-SNP 上の機密 VM** では、ホスト管理者による未承認のアクセスから VM 全体をハードウェアで暗号化して保護することができます。 このレベルには、通常、クラウド サービス プロバイダー (CSP) が管理するハイパーバイザーが含まれます。 この種類の機密 VM を使用して、VM 内で実行されるデータとコードに CSP がアクセスすることを防ぐことができます。

VM 管理者、または VM 内で実行されているその他のアプリやサービスは、保護された境界を超えて動作します。 これらのユーザーとサービスは、VM 内のデータとコードにアクセスできます。

AMD SEV-SNP テクノロジを使用すると、ハイパーバイザーから VM を分離できます。 ハードウェア ベースのメモリ整合性保護により、悪意のあるハイパーバイザー ベースの攻撃を防ぐことができます。 SEV-SNP モデルでは、AMD Secure Processor と VM を信頼します。 このモデルは、他のハードウェアおよびソフトウェア コンポーネントは信頼しません。 信頼されていないコンポーネントには、BIOS とホスト システム上のハイパーバイザーが含まれます。

:::image type="content" source="media/confidential-computing-deployment-models/amd-sev-snp-vm.png" alt-text="信頼された、および信頼されていないコンポーネントを定義する AMD SEV-SNP VM アーキテクチャの図。":::

### <a name="secure-enclaves-on-intel-sgx"></a>Intel SGX でのセキュア エンクレーブ

**Intel SGX でのセキュア エンクレーブ** では、ハードウェア ベースの暗号化を使用して VM 内のメモリ空間を保護します。 アプリケーション エンクレーブのセキュリティ境界は、AMD SEV-SNP 上の機密 VM よりも制限されています。 Intel SGX の場合、セキュリティ境界は VM 内のメモリの一部に適用されます。 Intel SGX を利用した VM 内で実行されているユーザー、アプリ、サービスは、エンクレーブ内で実行中のデータとコードにアクセスできません。

Intel SGX では、アプリケーションの分離によって使用中のデータを保護することができます。 開発者は、選択したコードとデータを変更から保護することで、アプリケーションをセキュリティ強化されたエンクレーブまたは信頼された実行モジュールにパーティション分割して、アプリケーションのセキュリティを強化できます。 エンクレーブの外部のエンティティは、どのアクセス許可レベルでも、エンクレーブ メモリを読み書きすることはできません。 ハイパーバイザーまたはオペレーティング システムでは、通常の OS レベルの呼び出しを介してこのアクセスを取得できません。 エンクレーブ関数を呼び出す場合は、Intel SGX CPU で新しい一連の手順で行う必要があります。 このプロセスには、いくつかの保護チェックが含まれます。

:::image type="content" source="media/confidential-computing-deployment-models/intel-sgx-enclave.png" alt-text="アプリ エンクレーブ内のセキュリティで保護された情報を示す Intel SGX エンクレーブ アーキテクチャの図。":::

## <a name="next-steps"></a>次のステップ

- [機密コンテナーについて学習する](confidential-containers.md)
- [コンフィデンシャル コンピューティング エンクレーブについて学習する](confidential-computing-enclaves.md)
