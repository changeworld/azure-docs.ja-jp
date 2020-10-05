---
title: Azure Kubernetes Service (AKS) の機密コンテナー
description: 機密コンテナーでの変更されていないコンテナーのサポートについて説明します。
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 9/22/2020
ms.author: amgowda
ms.service: container-service
ms.openlocfilehash: 528b843e24e1d63e4822c253b3636ef490e8fe8e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90993493"
---
# <a name="confidential-containers"></a>機密コンテナー

## <a name="overview"></a>概要

開発者が**既存の Docker アプリケーション (新規または既存)** を取り込み、機密性の高いコンピューティング ノードのサポートを使用して Azure Kubernetes Service (AKS) で安全に実行できるようにします。

機密コンテナーを使用して次のものを保護できます。

- データの整合性 (data integrity) 
- データの機密性
- コードの整合性
- 暗号化によるコンテナー コードの保護
- ハードウェアベースの保証
- 既存のアプリを実行できるようにする
- 信頼のハードウェア ルートを作成する

ハードウェア ベースの信頼できる実行環境 (TEE) は、信頼できるコンピューティング ベース (TCB) コンポーネントからハードウェアとソフトウェアの測定を通じて強力な保証を提供するために使用される重要なコンポーネントです。 これらの測定値の検証は、予期される計算の検証に役立ち、コンテナー アプリの改ざんが検証されます。

機密コンテナーを使用すると、**Python、Java、Node JS などで開発されたカスタム アプリケーションや、NGINX、Redis Cache、MemCache などのパッケージ化されたソフトウェアア プリケーション**を、変更せずに AKS で実行できます。

機密コンテナーは、コンテナーの機密性に対する最速のパスであり、暗号化によるコンテナーの保護が含まれ、ビジネス ロジックをまったく、またはほとんど変更せずに、リフト アンド シフトを可能にします。

![機密コンテナーの変換](./media/confidential-containers/conf-con-deploy-process.jpg)


## <a name="confidential-container-enablers"></a>機密コンテナーのイネーブラー

既存の Docker コンテナーを実行するには、機密コンピューティング ノード上のアプリケーションに、特別な CPU 命令セットを利用するためのアブストラクション レイヤーまたは SGX ソフトウェアが必要です。 また、SGX ソフトウェアを使用すると、機密アプリケーション コードを保護し、ゲスト OS、ホスト OS、またはハイパーバイザーを削除するための CPU への直接実行を作成できます。 この保護によって、オペレーティング システムまたはハイパーバイザー レイヤーでの全体的な攻撃の危険と脆弱性が減ります。

機密コンテナーは、AKS で完全にサポートされており、Azure パートナーおよびオープンソース ソフトウェア (OSS) プロジェクトによって有効にされます。 開発者は、機能、Azure サービスへの統合、ツールのサポートに基づいて、ソフトウェア プロバイダーを選択できます。

## <a name="partner-enablers"></a>パートナー イネーブラー
> [!NOTE]
> 以下のソリューションは Azure パートナーから提供されており、ライセンス料金が発生する場合があります。 パートナー ソフトウェアの使用条件を個別に確認してください。 

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) では、開発者はポータルと CLI ベースのエクスペリエンスを選択し、コンテナー化されたアプリケーションを取り込んで、アプリケーションを変更または再コンパイルすることなく、それらを SGX 対応の機密コンテナーに変換できます。 Fortanix により、既存のアプリケーション、新しいエンクレーブ ネイティブ アプリケーション、事前パッケージ済みアプリケーションなど、幅広いアプリケーションのセットを実行して管理するための柔軟性が提供されます。 ユーザーは、[Enclave Manager](https://em.fortanix.com/) UI または [REST API](https://www.fortanix.com/api/em/) を使用し、Azure Kubernetes Service の[クイック スタート](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) ガイドに従って、機密コンテナーを作成できます。

![Fortanix のデプロイ プロセス](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain)

[SCONE](https://scontain.com/index.html?lang=en) では、証明書、キー、シークレットを生成できるセキュリティ ポリシーがサポートされ、アプリケーションの構成証明済みサービスにのみ表示されることが保証されます。 このようにして、アプリケーションのサービスでは TLS を解して相互に自動的に構成証明が行われ、アプリケーションや TLS を変更する必要はありません。 これについては、次の簡単な Flask アプリケーションのヘルプを参照してください: https://sconedocs.github.io/flask_demo/  

SCONE では、アプリケーションを変更したりアプリケーションを再コンパイルしたりすることなく、既存のほとんどのバイナリをエンクレーブ内で実行されるアプリケーションに変換できます。 また、SCONE では、データ ファイルと Python コード ファイルの両方を暗号化することで、Python などの解釈された言語も保護されます。 SCONE のセキュリティ ポリシーにより、不正なアクセス、変更、ロールバックから暗号化されたファイルを保護することができます。 既存の Python アプリケーションを "SCONE 対応にする" 方法については、[こちら](https://sconedocs.github.io/sconify_image/)で説明されています

![Scontain フロー](./media/confidential-containers/scone-workflow.png)

AKS を使用した機密コンピューティング ノードへの Scone のデプロイは、完全にサポートされ統合されています。 https://sconedocs.github.io/aks/ のサンプル アプリケーションで始めてください

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) では、変更されていないコンテナーを AKS で実行できるようにする、SGX プラットフォーム ソフトウェアが提供されます。 **今後**の機能とユーザー フローの詳細については、[こちら](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)を参照してください。

サンプル Redis Cache と Python カスタム アプリケーションについては[こちら](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)を参照してください

![Anjuna プロセス](./media/confidential-containers/anjuna-process-flow.png)

## <a name="oss-enablers"></a>OSS イネーブラー 
> [!NOTE]
> 以下のソリューションは、オープンソース プロジェクトを通じて提供されており、Azure Confidential Computing (ACC) や Microsoft に直接関連するものではありません。  

### <a name="graphene"></a>Graphene

[Graphene](https://grapheneproject.io/) は軽量のゲスト OS であり、最小限のホスト要件で単一の Linux アプリケーションを実行するように設計されています。 Graphene では、完全な OS の実行に匹敵する利点がある分離された環境でアプリケーションを実行でき、既存の Docker コンテナー アプリケーションを Graphene Shielded Containers (GSC) に変換するための優れたツールがサポートされています。

AKS のサンプル アプリケーションとデプロイについては、[こちら](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)を参照してください

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) は、Intel SGX 用のメモリセーフでマルチプロセス ライブラリの OS (LibOS) です。 ソース コードをほとんど、またはまったく変更することなく、レガシ アプリケーションを SGX で実行できます。 Occlum では、ユーザー ワークロードの機密性が透過的に保護されながら、既存の Docker アプリケーションに簡単にリフト アンド シフトできます。

Occlum では AKS デプロイがサポートされています。 さまざまなサンプル アプリでのデプロイの手順については、[こちら](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)を参照してください


## <a name="confidential-containers-demo"></a>機密コンテナーのデモ
機密コンテナーを使用した機密医療のデモをご覧ください。 サンプルは[こちら](https://github.com/Azure-Samples/confidential-container-samples/blob/main/confidential-healthcare-scone-confinf-onnx/README.md)から入手できます。 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>お問い合わせ

実装について質問がある場合、またはイネーブラーになる場合。 acconaks@microsoft.com に電子メールを送信する

## <a name="reference-links"></a>参照リンク

[Microsoft Azure Attestation](../attestation/overview.md)

[DCsv2 仮想マシン](virtual-machine-solutions.md)

[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)
