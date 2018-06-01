---
title: Habitat を使用してアプリケーションを Azure にデプロイする
description: Azure 仮想マシンとコンテナーにアプリケーションを一貫した方法でデプロイする方法を説明します
keywords: azure、chef、devops、仮想マシン、概要、自動化、habitat
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 73c6834eb53c0496fa673dd25ab90abc18a6a139
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267078"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Habitat を使用してアプリケーションを Azure にデプロイする
[Habitat](https://www.habitat.sh/) は、まったく新しいアプリケーション管理アプローチを提供する今までにない種類のオープン ソース プロジェクトです。 Habitat は、アプリケーションとその自動化をデプロイの単位にします。 アプリケーションが軽量の "環境" にラップされると、ランタイム環境は、コンテナー、ベア メタル、PaaS のいずれであってもフォーカスを失い、アプリケーションを制約しなくなります。 

この記事では、Habitat を使用する利点について説明します。

## <a name="support-for-the-modern-application"></a>最新アプリケーションのサポート
Habitat パッケージには、アプリケーションがライフサイクル全体を通じて実行する必要があるものがすべて含まれます。 Habitat のコア コンポーネントは次のとおりです。
- パッケージ形式 - Habitat パッケージ内のアプリケーションは、アトミック、不変、監査可能です。
- Habitat スーパーバイザーは、パッケージのピア関係、アップグレード戦略、セキュリティ ポリシーを認識してアプリケーション パッケージを実行します。 Habitat スーパーバイザーは、どのような環境が存在するかにかかわらず、そのアプリケーションを構成および管理します。
- Habitat エコシステムでは、Habitat ビルド プランを取得し、Habitat パッケージを作成し、保管場所に公開するビルド サービスも提供されます。

## <a name="run-any-application-anywhere"></a>任意の場所で任意のアプリケーションを実行
Habitat では、アプリケーションは任意のランタイム環境で変更せずに実行できます。 これには、ベア メタルや仮想マシンから、コンテナー (Docker など)、クラスター管理システム (Mesosphere や Kubernetes など)、PaaS システム (Pivotal Cloud Foundry) に至るすべてのものが含まれます。

## <a name="easily-port-legacy-applications"></a>レガシ アプリケーションを簡単に移植
レガシ アプリケーションが Habitat パッケージにラップされている場合、アプリケーションは最初に設計された対象の環境に依存しません。 パッケージは、クラウドやコンテナーなどの最新の環境にすばやく移行できます。 また、Habitat パッケージには外部に公開された標準インターフェイスがあるので、レガシ アプリケーションの管理がはるかに容易になります。

## <a name="improve-the-container-experience"></a>コンテナーのエクスペリエンスの向上
Habitat は、運用環境でのコンテナーの管理の複雑さを軽減します。 コンテナー内のアプリケーション構成を自動化することで、Habitat はコンテナー ベースのアプリケーションを開発環境から運用環境に移行するときに開発者が直面する課題に対処します。

## <a name="integrate-into-the-chef-devops-workflow"></a>Chef DevOps ワークフローとの統合
Habitat プロジェクトは、Chef によって後援されています。 Habitat は、インフラストラクチャの自動化に関する Chef の豊富な経験を活用して、これまでにない自動化機能をアプリケーションに提供します。 Chef は Habitat の商用サポートを提供し、Habitat と Chef Delivery 間のシームレスな統合を実現して、開発からデプロイまでのアプリケーション リリース サイクルを完全に自動化します。

## <a name="next-steps"></a>次の手順
* [Chef で Azure 仮想マシンの展開を自動化する](/azure/virtual-machines/windows/chef-automation)