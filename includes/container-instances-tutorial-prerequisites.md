---
title: インクルード ファイル
description: インクルード ファイル
services: container-instances
author: mmacy
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 95a8cf4eca7969631d069a31c9d08ba52c8b02b4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
ms.locfileid: "30193484"
---
このチュートリアルを完了するには、次の要件を満たす必要があります。

**Azure CLI**: ローカル コンピューターに Azure CLI バージョン 2.0.29 以降がインストールされている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール][azure-cli-install]」をご覧ください。

**Docker**: このチュートリアルの前提として、コンテナー、コンテナー イメージ、基本 `docker` コマンドなど、Docker のコア概念を基本的に理解している必要があります。 Docker やコンテナーの基礎についての入門情報については、「[Docker overview (Docker の概要)][docker-get-started]」を参照してください。

**Docker エンジン**: このチュートリアルを完了するには、Docker エンジンがローカルにインストールされている必要があります。 Docker には、[macOS][docker-mac]、[Windows][docker-windows]、[Linux][docker-linux] 上で Docker 環境の構成を行うパッケージが用意されています。

> [!IMPORTANT]
> Azure Cloud Shell には Docker デーモンが含まれていないため、このチュートリアルを完了するためには、Azure CLI と Docker エンジンの両方を自分で*ローカル コンピューター*にインストールする*必要があります*。 このチュートリアルで Azure Cloud Shell を使用することはできません。

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli