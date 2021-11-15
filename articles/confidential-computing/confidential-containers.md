---
title: Azure 上の機密コンテナー
description: 機密コンテナーによる変更されていないコンテナーのサポートについて説明します。
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 11/04/2021
ms.author: amgowda
ms.service: container-service
ms.custom: ignite-fall-2021
ms.openlocfilehash: 69045817c081ebe4eccf6044829b074d23ecd83c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853553"
---
# <a name="confidential-containers-on-azure"></a>Azure 上の機密コンテナー

Azure Confidential Computing では、機密コンテナーが提供されます。 [機密コンテナーには、複数のオプションを選択](choose-confidential-containers-offerings.md)できます。 構成証明を使用してセキュリティで保護され分離された環境により、コンテナーデプロイの全体的なセキュリティが向上します。 

ハードウェアベースの高信頼実行環境 (TEE) では、強力な保証が提供されます。 TEE は、信頼済みのコンピューティング ベース (TCB) コンポーネントからのハードウェアとソフトウェアの測定値を提供します。 Azure での機密コンテナー オファリングでは、これらの測定値を検証し、コンテナー アプリが検証可能な実行環境で実行されているかどうかを検証できます。

機密コンテナーでは、どのプログラミング言語で開発されたカスタム アプリケーションでもサポートしています。 Docker コンテナー アプリをシェルフから実行することもできます。

:::image type="content" source="./media/confidential-containers/sgx-confidential-container.png" alt-text="管理者のアクセスなしを示す、Kubernetes の機密コンテナー保護境界の図。":::

## <a name="enablers-with-intel-sgx-on-azure-kubernetes-serviceaks"></a>Azure Kubernetes Service (AKS) での Intel SGX を使用したイネーブラー

 既存の Docker コンテナーを実行するには、コンフィデンシャル コンピューティング ノード上のアプリケーションに、特別な CPU 命令セットを使用するためのアブストラクション レイヤーまたは Intel Software Guard Extensions (SGX) ソフトウェアが必要です。 SGX を構成して、機密アプリケーション コードを保護します。 SGX では、CPU への直接実行を作成して、ゲスト オペレーティング システム (OS)、ホスト OS、またはハイパーバイザーを信頼境界から削除します。 この手順によって、全体的な攻撃の危険と脆弱性が減ります。

Azure Kubernetes Service (AKS) では、機密コンテナーが完全にサポートされています。 AKS では、既存のコンテナーを機密性を保った状態で実行できます。

:::image type="content" source="./media/confidential-containers/confidential-containers-deploy-steps.png" alt-text="Intel SGX と AKS を有効にする新しい手順による、機密コンテナーの変換の図。":::

## <a name="partner-enablers"></a>パートナー イネーブラー

Azure パートナーとオープンソース ソフトウェア (OSS) プロジェクトで機密コンテナーを有効にすることができます。 開発者は、機能、Azure サービスとの統合、ツールのサポートに基づいて、ソフトウェア プロバイダーを選択できます。 

> [!IMPORTANT]
> Azure パートナーには、次のソリューションが用意されています。 これらのソリューションでは、ライセンス料金が発生する可能性があります。 パートナー ソフトウェアの利用規約をすべて個別に確認してください。 

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) には、コンテナー化されたアプリケーションを SGX 対応の機密コンテナーに変換するためのポータルとコマンドラインインターフェイス (CLI) エクスペリエンスがあります。 アプリケーションを変更または再コンパイルする必要はありません。 Fortanix では、広範なアプリケーションを柔軟に実行して管理できます。 既存のアプリケーション、新しいエンクレーブ ネイティブ アプリケーション、事前パッケージ済みアプリケーションを使用できます。 Fortanix の [Enclave Manager](https://em.fortanix.com/) UI または [REST API](https://www.fortanix.com/api/em/) を使用して開始します。 [AKS の Fortanix のクイックスタートガイド](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service)を使用して、機密コンテナーを作成します。

![Fortanix デプロイ プロセスの図。アプリケーションを機密コンテナーに移動してデプロイする手順を示しています。](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain)

[Scone](https://scontain.com/) (Scontain) セキュリティ ポリシーでは、証明書、キー、シークレットを生成します。 アプリケーションの構成証明を持つサービスのみが、これらの資格情報を参照します。 アプリケーション サービスは、TLS によって自動的に相互の構成証明を実行します。 アプリケーションや TLS を変更する必要はありません。 詳細については、SCONE の [Flask アプリケーションのデモ](https://sconedocs.github.io/flask_demo/)を参照してください。

SCONE では、ほとんどの既存のバイナリをエンクレーブ内で実行されるアプリケーションに変換できます。 アプリケーションを変更または再コンパイルする必要はありません。 また、SCONE では、データ ファイルと Python コード ファイルの両方を暗号化することで、Python などの解釈された言語も保護されます。 SCONE セキュリティ ポリシーを使用して、不正なアクセス、変更、ロールバックから暗号化されたファイルを保護することができます。 詳細については、[既存の Python アプリケーションでの SCONE の使用方法](https://sconedocs.github.io/sconify_image/)に関する SCONE のドキュメントを参照してください。

![SCONE ワークフローの図。SCONE がバイナリイメージを処理する方法を示しています。](./media/confidential-containers/scone-workflow.png)

AKS を使用して Azure コンフィデンシャル コンピューティング ノードに SCONE をデプロイできます。 このプロセスは完全にサポートされ、統合されています。 詳細については、「[SCONE サンプル AKS アプリケーション](https://sconedocs.github.io/aks/)」を参照してください。

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) では、変更されていないコンテナーを AKS で実行する、SGX プラットフォーム ソフトウェアが提供されます。 詳細については、Anjuna の[機能とサンプル アプリケーションに関するドキュメント](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)を参照してください。

サンプル Redis Cache と Python カスタム アプリケーションについては[こちら](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)を参照してください

![Anjuna のプロセスの図。Azure Confidential Computing でコンテナーがどのように実行されるかを示しています。](media/confidential-containers/anjuna-process-flow.png)

## <a name="oss-enablers"></a>OSS イネーブラー

> [!NOTE]
> オープンソース プロジェクトには、次のソリューションが用意されています。 Azure Confidential Computing や Microsoft は、これらのプロジェクトとソリューションに直接関連するものではありません。  

### <a name="gramine"></a>Gramine

[Gramine](https://grapheneproject.io/) は軽量のゲスト OS であり、最小限のホスト要件で単一の Linux アプリケーションを実行するように設計されています。 Gramine では、分離された環境でアプリケーションを実行できます。 既存の Docker コンテナー アプリケーションを Gramine Shielded Containers (GSC) に変換するためのツールがサポートされています。

詳細については、Gramine の[サンプルアプリケーションと AKS でのデプロイ](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)に関する説明を参照してください。

### <a name="occlum"></a>Occlum

[Occlum](https://occlum.io/) は、Intel SGX 用のメモリセーフでマルチプロセス ライブラリの OS (LibOS) です。 この OS では、ソース コードをほとんど、またはまったく変更することなく、レガシ アプリケーションを SGX で実行できます。 Occlum では、ユーザー ワークロードの機密性が透過的に保護されながら、既存の Docker アプリケーションに簡単に「リフト アンド シフト」できます。

Occlum では AKS デプロイがサポートされています。 詳細については、Occlum の「[デプロイ手順とサンプル アプリ](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)」に関するページを参照してください。

### <a name="marblerun"></a>Marblerun

[Marblerun](https://marblerun.sh/) は、機密コンテナーのオーケストレーション フレームワークです。 SGX 対応 Kubernetes での機密サービスの実行とスケーリングができます。 Marblerun では、クラスターでのサービスの検証、それらのシークレットの管理、それらの間でのエンクレーブからエンクレーブへの mTLS 接続の確立といった定型タスクを扱います。 また、Marblerun によって、機密コンテナーのクラスターが単純な JSON で定義されたマニフェストに準拠することも保証されます。 リモート構成証明を使用して、外部クライアントでマニフェストを確認できます。

このフレームワークによって、1 つのエンクレーブの機密性、整合性、検証可能性のプロパティが、Kubernetes クラスターに拡張されます。

Marblerun では、[各 SDK の例](https://docs.edgeless.systems/marblerun/#/examples?id=examples)と共に、Graphene、Occlum、および EGo によって作成された機密コンテナーをサポートしています。 フレームワークは、既存のクラウドネイティブ ツールと共に Kubernetes 上で実行されます。 CLI および Helm チャートがあります。 Marblerun では、AKS 上のコンフィデンシャル コンピューティング ノードもサポートしています。 Marblerun の[ガイドに従って、AKS に Marblerun をデプロイ](https://docs.edgeless.systems/marblerun/#/deployment/cloud?id=cloud-deployment)します。

## <a name="confidential-containers-demo"></a>機密コンテナーのデモ

サンプル アプリケーションについては、「[機密性の高いコンテナーを使用した医療のデモ](https://github.com/Azure-Samples/confidential-container-samples/blob/main/confidential-healthcare-scone-confinf-onnx/README.md)」を参照してください。 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>お問い合わせ

実装について質問がありますか? 機密コンテナーのためのイネーブラーにしますか? <acconaks@microsoft.com> に電子メールを送信する。

## <a name="next-steps"></a>次のステップ

- [Intel SGX 機密 VM ノードを使用して AKS クラスターをデプロイする](./confidential-enclave-nodes-aks-get-started.md)
- [Microsoft Azure Attestation](../attestation/overview.md)
- [Intel SGX 機密仮想マシン](virtual-machine-solutions-sgx.md)
- [Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)
