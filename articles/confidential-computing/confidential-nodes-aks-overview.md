---
title: Azure Kubernetes Service (AKS) のコンフィデンシャル コンピューティング ノード (パブリック プレビュー)
description: AKS のコンフィデンシャル コンピューティング ノード
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: ae3090689f9999c9ea6aa65447dadbdd7b0b2026
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "90998464"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service-public-preview"></a>Azure Kubernetes Service のコンフィデンシャル コンピューティング ノード (パブリック プレビュー)

[Azure Confidential Computing](overview.md) を使用すると、機密データをその使用中に保護することができます。 このデータは、基になるインフラストラクチャによって、他のアプリケーションや管理者、クラウド プロバイダーから保護されます。 

## <a name="overview"></a>概要

Azure Kubernetes Service (AKS) は、Intel SGX を基盤とする [DCsv2 コンフィデンシャル コンピューティング ノード](confidential-computing-enclaves.md)の追加をサポートします。 これらのノードは、メモリのプライベート領域の割り当てをユーザーレベルのコードに許可することで、ハードウェアベースの信頼できる実行環境 (TEE: Trusted Execution Environment ) 内で機密のワークロードを実行します。 これらのプライベート メモリ領域は、エンクレーブと呼ばれています。 エンクレーブは、コードとデータを、より高い特権で実行されるプロセスから保護するように設計されています。 SGX の実行モデルは、ゲスト OS とハイパーバイザーという中間レイヤーを排除します。 これによって、特殊なメモリ ブロックを暗号化したまま、コンテナー アプリケーションを直接 CPU 上で実行することができます。 


![SGX ノードの概要](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>AKS の機密ノードの機能

- SGX の信頼できる実行環境 (TEE) によってハードウェア ベース、プロセス レベルでコンテナーを分離 
- 異種ノード プール クラスター (機密ノード プールと非機密ノード プールの混在)
- Encrypted Page Cache (EPC: 暗号化されたページ キャッシュ) メモリベースのポッド スケジューリング
- プレインストールされた SGX DCAP ドライバー
- プレインストールされた Intel FSGS パッチ
- CPU 消費量に基づくポッドの水平自動スケーリングとクラスターの自動スケーリング
- AKS デーモンセットによるアウトプロセス構成証明ヘルパー
- Ubuntu 18.04 Gen 2 VM ワーカー ノードによる Linux コンテナーのサポート

## <a name="aks-provided-daemon-sets"></a>AKS によって提供されるデーモン セット

#### <a name="sgx-device-plugin"></a>SGX デバイス プラグイン <a id="sgx-plugin"></a>

SGX デバイス プラグインは、EPC メモリ用の Kubernetes デバイス プラグイン インターフェイスを実装します。 EPC メモリは、実質上、このプラグインによって、Kubernetes における追加のリソース タイプとなります。 ユーザーは、他のリソースと同様、このリソースに対する制限を指定できます。 このデバイス プラグインは、スケジューリング機能以外に、機密ワークロード コンテナーへのアクセス許可を SGX デバイス ドライバーに割り当てる働きをします。 EPC メモリベース デプロイの実装サンプル (`kubernetes.azure.com/sgx_epc_mem_in_MiB`) は、[こちら](https://github.com/azure-samples/confidential-computing/blob/main/containersamples/helloworld/helloworld.yaml)でご覧いただけます。

#### <a name="sgx-quote-helper-service"></a>SGX クォート ヘルパー サービス <a id="sgx-quote"></a>

リモート構成証明を実行するエンクレーブ アプリケーションは、クォート (QUOTE) を生成する必要があります。 この QUOTE によって、アプリケーションの ID と状態、そしてエンクレーブが実行されている環境の暗号による証明が得られます。 QUOTE を生成するためには、Intel から提供されている信頼のおける特定のソフトウェア コンポーネントが必要です。このコンポーネントは、SGX Platform Software Components (PSW および DCAP) に含まれています。 PSW は、ノードごとに実行されるデーモン セットとしてパッケージされています。 エンクレーブ アプリから構成証明の QUOTE を要求するときに利用できます。 AKS によって提供されるサービスを使用することで、PSW とホスト内の他のソフトウェア コンポーネントとの間の互換性が維持しやすくなります。 その使用法と機能については、[詳細情報](confidential-nodes-out-of-proc-attestation.md)をご覧ください。

## <a name="programming--application-models"></a>プログラミングとアプリケーション モデル

### <a name="confidential-containers"></a>機密コンテナー

[機密コンテナー](confidential-containers.md)では、既存のプログラムと**一般的なプログラミング言語**ランタイムの大半 (Python、Node、Java など)、そして、その既存のライブラリ依存関係が、ソースコードの変更や再コンパイルなしで実行されます。 機密性を最短で確保するモデルであり、オープン ソース プロジェクトと Azure のパートナーによって実現されています。 安全なエンクレーブ内で動作するようにあらかじめ作成されているコンテナー イメージを機密コンテナーと呼んでいます。

### <a name="enclave-aware-containers"></a>エンクレーブ対応コンテナー

AKS では、機密ノード上で動作して、SDK やフレームワークを通じて提供される**特殊な命令セット**を利用するようにプログラムされたアプリケーションがサポートされます。 このアプリケーション モデルは、最も基盤となる Trusted Computing Base (TCB) と共に、ほとんどの制御をアプリケーションに提供します。 エンクレーブ対応コンテナーについての[詳細情報](enclave-aware-containers.md)をご覧ください。

## <a name="next-steps"></a>次の手順

[コンフィデンシャル コンピューティング ノードを使用して AKS クラスターをデプロイする](./confidential-nodes-aks-get-started.md)

[機密コンテナーのクイック スタート サンプル](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU リスト](https://docs.microsoft.com/azure/virtual-machines/dcv2-series)

<!-- LINKS - external -->
[Azure Attestation]: https://docs.microsoft.com/en-us/azure/attestation/


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions