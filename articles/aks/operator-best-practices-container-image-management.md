---
title: オペレーターのベスト プラクティス - Kubernetes Services (AKS) でのコンテナー イメージの管理
description: Azure Kubernetes Service (AKS) でコンテナー イメージを管理およびセキュリティで保護する方法に関するクラスター オペレーターのベスト プラクティス
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 998d8602b6aa0e71a04f75aff1c29551ba09c8a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105121"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Azure Kubernetes サービス (AKS) でのコンテナー イメージの管理とセキュリティに関するベスト プラクティス

Azure Kubernetes Service (AKS) でアプリケーションを開発および実行するときは、コンテナーとコンテナー イメージのセキュリティが主要な優先事項となります。 期限切れの基本イメージ、またはパッチ未適用のアプリケーション ランタイムを含むコンテナーは、セキュリティ上のリスクおよび考えられる攻撃ベクトルをもたらします。 

リスクを最小限に抑えるには、スキャンおよび修復のツールを統合してコンテナー内のビルドおよびランタイムで実行します。 脆弱性または期限切れの基本イメージをキャッチするのが早いほど、クラスターの安全性は高まります。 

この記事に出てくる "*コンテナー*" には次の両方の意味があります。
* コンテナー レジストリに格納されたコンテナー イメージ。
* 実行中のコンテナー。

この記事では、AKS でコンテナーをセキュリティで保護する方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * イメージの脆弱性をスキャンして修復する。
> * 基本イメージの更新時にコンテナー イメージを自動的にトリガーおよび再デプロイする。

[クラスター セキュリティ][best-practices-cluster-security]および[ポッド セキュリティ][best-practices-pod-security]に関するベスト プラクティスも参照できます。

また、コンテナーをスキャンして脆弱性を検出するのを支援するために、[Security Center のコンテナーのセキュリティ][security-center-containers]も使用できます。 Security Center と [Azure Container Registry の統合][security-center-acr]は、イメージおよびレジストリを脆弱性から保護するのに役立ちます。

## <a name="secure-the-images-and-run-time"></a>イメージおよびランタイムをセキュリティで保護する

> **ベスト プラクティスのガイダンス** 
>
> コンテナー イメージをスキャンして脆弱性を探す。 検証済みのイメージのみをデプロイする。 基本イメージとアプリケーション ランタイムを定期的に更新する。 AKS クラスターでワークロードを再デプロイする。

コンテナーベースのワークロードを採用する場合は、独自のアプリケーションをビルドするのに使用するイメージとランタイムのセキュリティを検証する必要があります。 デプロイにセキュリティの脆弱性が生じするのを回避するにはどうすればよいでょうか? 
* [Twistlock][twistlock] や [Aqua][aqua] などのツールを使用してコンテナー イメージをスキャンするプロセスをご利用のデプロイ ワークフローに含める。
* 検証済みのイメージのみをデプロイできるようにする。

![コンテナー イメージをスキャンして修正し、検証し、デプロイする](media/operator-best-practices-container-security/scan-container-images-simplified.png)

たとえば、継続的統合と継続的デプロイ (CI/CD) パイプラインを使用して、イメージのスキャン、検証、およびデプロイを自動化することができます。 Azure Container Registry には、これらの脆弱性スキャン機能が含まれます。

## <a name="automatically-build-new-images-on-base-image-update"></a>基本イメージの更新時に新しいイメージを自動的にビルドする

> **ベスト プラクティスのガイダンス** 
>
> アプリケーション イメージに基本イメージを使用する際には、基本イメージの更新時にオートメーションを使用して新しいイメージをビルドします。 通常、更新された基本イメージにはセキュリティ修正プログラムが含まれているので、すべてのダウンストリーム アプリケーション コンテナー イメージを更新します。

基本イメージが更新されるたびに、すべてのダウンストリーム コンテナー イメージも更新する必要があります。 このビルド プロセスを、[Azure Pipelines][azure-pipelines] や Jenkins などの検証およびデプロイのパイプラインに統合します。 これらのパイプラインにより、更新されたベース イメージで引き続きアプリケーションが確実に実行されます。 アプリケーション コンテナー イメージが検証されたら、AKS デプロイは、セキュリティで保護された最新のイメージを実行するように更新できます。

また、Azure Container Registry タスクは、基本イメージの更新時にコンテナー イメージを自動的に更新できます。 この機能を使用すると、いくつかの基本イメージをビルドし、それらをバグおよびセキュリティ修正プログラムを適用して更新された状態に維持することができます。

基本イメージの更新の詳細については、「[Automate image builds on base image update with Azure Container Registry Tasks][acr-base-image-update]」 (Azure Container Registry タスクを使用して基本イメージの更新時のコンテナー イメージ ビルドを自動化する) を参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、コンテナーをセキュリティで保護する方法について説明しました。 これらの領域のいくつかを実装する場合は、次の記事を参照してください。

* [Automate image builds on base image update with Azure Container Registry Tasks][acr-base-image-update] (Azure Container Registry タスクを使用して基本イメージの更新時のコンテナー イメージ ビルドを自動化する)

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: ../security-center/container-security.md
[security-center-acr]: ../security-center/defender-for-container-registries-introduction.md