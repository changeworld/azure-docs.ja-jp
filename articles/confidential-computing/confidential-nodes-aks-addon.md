---
title: 機密仮想マシン用 Azure Kubernetes Service プラグイン
description: Azure Kubernetes Service で機密仮想マシン用の Intel SGX デバイス プラグインと Intel SGX クォート ヘルパー デーモン セットを使用する方法。
author: agowdamsft
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: article
ms.date: 11/01/2021
ms.author: amgowda
ms.custom: ignite-fall-2021
ms.openlocfilehash: f4a00e1c70427c6b467a52694340e10109b5f4f5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092761"
---
# <a name="confidential-computing-plugin-for-confidential-vms"></a>機密仮想マシン用のコンフィデンシャル コンピューティング プラグイン

Azure Kubernetes Service (AKS) では、Azure のコンフィデンシャル コンピューティング仮想マシン (VM) 用のプラグインを提供しています。 プラグインの `confcom` はデーモン セットです。 プラグインは、AKS クラスターの Intel Software Guard Extensions (SGX) の機密仮想マシンに対してのみ実行されます。 このプラグインは、AKS クラスター レベルで登録されます。 プラグインを使用して、機密ノードを簡単に管理できます。 開始する前に、[AKS クラスターで プラグインを有効にします](./confidential-enclave-nodes-aks-get-started.md)。

## <a name="intel-sgx-device-plugin-for-aks"></a>AKS 用 Intel SGX デバイス プラグイン

SGX デバイス プラグインでは、エンクレーブ ページ キャッシュ (EPC) メモリ用の Kubernetes デバイス プラグイン インターフェイスを実装します。 このプラグインにより、EPC メモリが実質上 Kubernetes のもう 1 つのリソース タイプとなります。 ユーザーは、他のリソースと同様に EPC の制限を指定できます。 このデバイス プラグインは、スケジューリング機能以外に、機密ワークロード コンテナーへのアクセス許可を SGX デバイス ドライバーに割り当てる働きをします。 [EPC メモリベース デプロイの実装サンプル](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml) (`kubernetes.azure.com/sgx_epc_mem_in_MiB`) を使用できます。

## <a name="psm-with-sgx-quote-helper"></a>SGX クォート ヘルパーを備えた PSM

リモート構成証明を実行するエンクレーブ アプリケーションでは、クォートを生成する必要があります。 クォートにより、アプリケーションの ID と状態、そしてエンクレーブのホスト環境の暗号による証明が得られます。 クォートを生成するには、Intel から提供されている信頼のおける特定のソフトウェア コンポーネントが必要です。このコンポーネントは、SGX Platform Software Components (PSW/DCAP) に含まれています。 PSW は、ノードごとに実行されるデーモン セットとしてパッケージされています。 PSW は、エンクレーブ アプリから構成証明のクォートを要求する場合に使用できます。 AKS によって提供されるサービスを使用すると、PSW とホスト内の他のソフトウェア コンポーネントとの間の互換性を維持しやすくなります。 以下の機能の詳細をお読みください。

リモート構成証明を実行する[エンクレーブ アプリケーション](confidential-computing-enclaves.md)では、生成されたクォートが必要です。 このクォートにより、アプリケーションの ID、状態、実行中の環境の暗号による証明が得られます。 生成するには、Intel の PSW の一部である信頼のおける特定のソフトウェア コンポーネントが必要です。

### <a name="overview"></a>概要

> [!NOTE]
> この機能は、専用の Intel SGX ハードウェアを使用する DCsv2/DCsv3 VM でのみ必要です。 
 
Intel では、クォートの生成に関して 2 つの構成証明モードをサポートしています。 種類の選択方法については、[構成証明の種類の違い](#attestation-type-differences)に関するページを参照してください。

- **インプロセス**: 信頼のおけるソフトウェア コンポーネントをエンクレーブ アプリケーションのプロセス内でホストします。

- **アウトプロセス**: 信頼のおけるソフトウェア コンポーネントをエンクレーブ アプリケーションの外でホストします。
 
Open Enclave SDK を使用して作成された SGX アプリケーションには、既定でインプロセス構成証明モードが使用されます。 SGX ベースのアプリケーションでは、アウトプロセスが許可され、追加のホスティングが必要です。 これらのアプリケーションは、Architectural Enclave Service Manager (AESM) などの必要なコンポーネントをアプリケーションの外部に公開する必要があります。

この機能を使用することを強くお勧めします。 この機能により、Intel プラットフォームの更新中や DCAP ドライバーの更新中のエンクレーブ アプリのアップタイムが向上します。

### <a name="attestation-type-differences"></a>構成証明の種類の違い

コンテナー化されたアプリケーションごとに PSW のクォート生成コンポーネントを更新する必要がない。

アウトプロセスでは、コンテナーの所有者がそのコンテナー内の更新を管理する必要がありません。 コンテナーの所有者は、コンテナーの外部にある一元化されたサービスを呼び出すプロバイダー インターフェイスを利用できます。 このサービスは、プロバイダーによって更新および管理されます。

アウトプロセスでは、古い PSW コンポーネントに起因する失敗を懸念する必要がありません。 クォートの生成には、信頼のおけるソフトウェア コンポーネント、Quoting Enclave (QE) と Provisioning Certificate Enclave (PCE) が関係します。どちらも信頼済みのコンピューティング ベース (TCB) に含まれています。 これらのソフトウェア コンポーネントは、構成証明の要件を維持するために最新の状態になっている必要があります。 プロバイダーが、これらのコンポーネントの更新を管理します。 コンテナー内にある古くなった信頼のおけるソフトウェア コンポーネント起因する構成証明の失敗に対処する必要はありません。

アウトプロセスでは、EPC メモリの使用率が高くなります。 インプロセス構成証明モードでは、リモート構成証明のために、各エンクレーブ アプリケーションが QE と PCE のコピーをインスタンス化します。 アウトプロセスでは、それらのエンクレーブがコンテナーでホストされないため、コンテナーのクォータからはエンクレーブのメモリが消費されません。

カーネルの適用に対する保護機能も用意されています。 Intel SGX ドライバーを Linux カーネルにアップストリームすると、より高い特権がエンクレーブに付与されます。 この特権によってエンクレーブは PCE を呼び出すことができ、インプロセス モードで実行されているエンクレーブ アプリケーションが中断されます。 既定では、エンクレーブは、この権限を受け取りません。 この特権をエンクレーブ アプリケーションに与えるためには、アプリケーションのインストール プロセスに変更を加える必要があります。 アウトプロセス要求を処理するサービスのプロバイダーは、この特権を使用してサービスをインストールします。

PSW および DCAP との下位互換性をチェックする必要はありません。 PSW のクォート生成コンポーネントに対する更新は、プロバイダーによって下位互換性が確認されます。 このステップでは、互換性の問題を前もって処理し、機密ワークロードの更新をデプロイする前に解決します。

### <a name="out-of-proc-attestation-for-confidential-workloads"></a>機密ワークロードのアウトプロセス構成証明

機密ワークロードのアウトプロセス構成証明モデルは機密ワークロードで機能します。 クォートの要求側とクォートの生成側は別々に実行されますが、同じ物理マシン上で実行されます。 クォートの生成は一元的に行われ、そこですべてのエンティティからのクォート要求が処理されます。 インターフェイスを適切に定義し、クォートを要求するどのエンティティからも検出できるようにします。

![クォートの要求側とクォートの生成インターフェイスの図。](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

この抽象モデルは、機密ワークロードのシナリオを対象としたものです。 このモデルでは、既に使用可能な AESM サービスを利用しています。 AESM はコンテナー化され、Kubernetes クラスター全体にデーモンセットとしてデプロイされます。 AESM サービス コンテナーの 1 つのインスタンスがポッドにラップされ、各エージェント ノードにデプロイされることが Kubernetes によって保証されます。 新しい SGX クォート デーモンセットは、`sgx-device-plugin` デーモンセットに依存します。AESM サービス コンテナーは、QE と PCE のエンクレーブを起動するための EPC メモリを `sgx-device-plugin` に要求するためです。

アウトプロセスのクォート生成を使用するには、作成時に環境変数 `SGX_AESM_ADDR=1` を設定して、それぞれのコンテナーでオプトインする必要があります。 コンテナーには、既定の Unix ドメイン ソケットに要求を誘導する役割を果たす `libsgx-quote-ex` パッケージが含まれている必要があります。

アプリケーションでは従来どおり、インプロセスの構成証明を使用することもできます。 ただし、アプリケーション内でインプロセスとアウトプロセスを同時に使用することはできません。 アウトプロセス インフラストラクチャは既定で利用でき、リソースを消費します。

### <a name="sample-implementation"></a>実装例

以下の Docker ファイルは、Open Enclave ベースのアプリケーションのサンプルです。 `SGX_AESM_ADDR=1` 環境変数を Docker ファイルで設定します。 または、デプロイ ファイルで変数を設定します。 Docker ファイルとデプロイ YAML の詳細については、以下のサンプルに従ってください。 

> [!Note] 
> アウトプロセス構成証明を正しく機能させるためには、アプリケーション コンテナーに Intel の **libsgx-quote-ex** パッケージを含める必要があります。
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for detail
FROM ubuntu:18.04 as sgx_base
RUN apt-get update && apt-get install -y \
    wget \
    gnupg

# Add the repository to sources, and add the key to the list of
# trusted keys used by the apt to authenticate packages
RUN echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
# Add Microsoft repo for az-dcap-client
RUN echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | tee /etc/apt/sources.list.d/msprod.list \
    && wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

FROM sgx_base as sgx_sample
RUN apt-get update && apt-get install -y \
    clang-7 \
    libssl-dev \
    gdb \
    libprotobuf10 \
    libsgx-dcap-ql \
    libsgx-quote-ex \
    az-dcap-client \
    open-enclave
WORKDIR /opt/openenclave/share/openenclave/samples/remote_attestation
RUN . /opt/openenclave/share/openenclave/openenclaverc \
    && make build
# this sets the flag for out of proc attestation mode. alternatively you can set this flag on the deployment files
ENV SGX_AESM_ADDR=1 

CMD make run
```
代わりに、デプロイ YAML ファイルのアウトプロセス構成証明モードを次のように設定します。

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
spec:
  template:
    spec:
      containers:
      - name: sgxtest
        image: <registry>/<repository>:<version>
        env:
        - name: SGX_AESM_ADDR
          value: 1
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10
        volumeMounts:
        - name: var-run-aesmd
          mountPath: /var/run/aesmd
      restartPolicy: "Never"
      volumes:
      - name: var-run-aesmd
        hostPath:
          path: /var/run/aesmd
```

## <a name="next-steps"></a>次の手順

- [AKS で機密ノード (DCsv2/DCsv3 シリーズ) を設定する](./confidential-enclave-nodes-aks-get-started.md)
- [機密コンテナーのクイック スタート サンプル](https://github.com/Azure-Samples/confidential-container-samples)
- [DCsv2 SKU リスト](../virtual-machines/dcv2-series.md)
- [DCSv3 SKU の一覧](../virtual-machines/dcv3-series.md)
- [Azure Attestation](../attestation/index.yml)
- [Azure 上の Intel SGX 機密仮想マシン](/confidential-computing/virtual-machine-solutions-sgx.md)
