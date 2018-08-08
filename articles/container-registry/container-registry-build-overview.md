---
title: Azure Container Registry ビルド (ACR ビルド) を使用して OS とフレームワークの修正プログラムの適用を自動化する
description: クラウド内での安全で自動化されたコンテナー イメージのビルドと修正プログラムの適用を提供する、Azure Container Registry の機能スイートである ACR ビルドの概要。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 08/01/2018
ms.author: marsma
ms.openlocfilehash: 63bbd9b5711330207c34ac4aa05aac3a71304653
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413581"
---
# <a name="automate-os-and-framework-patching-with-acr-build"></a>ACR ビルドを使用して OS とフレームワークの修正プログラムの適用を自動化する

コンテナーは、アプリケーションと開発者の依存関係をインフラストラクチャおよび操作の要件から分離する新たなレベルの仮想化を提供します。 ただし、このアプリケーション仮想化に修正プログラムを適用する方法に取り組む必要が残っています。

**ACR ビルド**は、Azure Container Registry 内の一連の機能です。 Linux、Windows、および ARM 用のクラウドベースのコンテナー イメージのビルド機能があり、Docker コンテナーの [OS およびフレームワークのパッチ適用](#automate-os-and-framework-patching)を自動化できます。

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="what-is-acr-build"></a>ACR ビルドとは

Azure Container Registry ビルドは、Azure ネイティブ コンテナー イメージ ビルド サービスです。 ACR ビルドにより、オンデマンドのコンテナー イメージ ビルドを含むクラウド内の内部ループ開発と、ソース コードのコミットと基本イメージの更新に対する自動化されたビルドが可能になります。

コードが Git リポジトリにコミットされたとき、またはコンテナーの基本イメージが更新されたときに、コンテナー イメージ ビルドを自動的にトリガーします。 基本イメージの更新のトリガーを使用して、OS とアプリケーション フレームワークの修正プログラム適用のワークフローを自動化することができ、不変のコンテナーの原則に従いながら、セキュリティで保護された環境を維持できます。

## <a name="quick-build-inner-loop-extended-to-the-cloud"></a>クイック ビルド: クラウドに拡張された内部ループ

ライフサイクル管理は、開発者が最初のコード行をコミットする前に開始します。 ACR ビルドの[クイック ビルド](container-registry-tutorial-quick-build.md)機能を使用して、統合されたローカル内部ループ開発エクスペリエンスを有効にし、Azure にビルドをオフロードできます。 クイック ビルドでは、コードをコミットする前に、自動化されたビルド定義を検証できます。

よく知られている `docker build` 形式を使用して、Azure CLI の [az acr build][az-acr-build] コマンドは**コンテキスト** (ビルド対象の一連のファイル) を取得して ACR ビルド サービスに送信し、既定では、完了時にビルド済みのイメージをそのレジストリにプッシュします。

次の表は、ACR ビルドでサポートされているコンテキストの場所の例を示しています。

| コンテキストの場所 | 説明 | 例 |
| ---------------- | ----------- | ------- |
| ローカル ファイルシステム | ローカル ファイル システム上のディレクトリ内のファイル。 | `/home/user/projects/myapp` |
| GitHub master ブランチ | GitHub リポジトリの master (またはその他の既定の) ブランチ内のファイル。  | `https://github.com/gituser/myapp-repo.git` |
| GitHub ブランチ | GitHub リポジトリの特定のブランチ。| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub PR | GitHub リポジトリのプル要求。 | `https://github.com/gituser/myapp-repo.git#pull/23/head` |
| GitHub のサブフォルダー | GitHub リポジトリのサブフォルダー内のファイル 例は、PR とサブフォルダーの指定の組み合わせを示しています。 | `https://github.com/gituser/myapp-repo.git#pull/24/head:myfolder` |
| リモート tarball | リモート Web サーバー上の圧縮されたアーカイブ内のファイル。 | `http://remoteserver/myapp.tar.gz` |

また、ACR ビルドは geo レプリケートされているレジストリに従い、分散している開発チームが最寄りのレプリケート済みレジストリを利用できるようにします。

ACR ビルドは、コンテナー ライフサイクル プリミティブとして設計されています。 たとえば、ACR ビルドを CI/CD ソリューションに統合します。 [az login][az-login] を[サービス プリンシパル][az-login-service-principal]で実行することにより、CI/CD ソリューションは [az acr build][az-acr-build] コマンドを発行してイメージ ビルドを開始できます。

クイック ビルドを使用する方法については、最初の ACR ビルド チュートリアル、「[Build container images in the cloud with Azure Container Registry Build](container-registry-tutorial-quick-build.md)」(Azure Container Registry ビルドを使用してクラウド内のコンテナー イメージをビルドする) をご覧ください。

## <a name="automatic-build-on-source-code-commit"></a>ソース コードのコミット時の自動ビルド

ACR ビルドを使用して、コードが Git リポジトリにコミットされたときにコンテナー イメージ ビルドを自動的にトリガーします。 Azure CLI コマンド [az acr build-task][az-acr-build-task] を使用して構成できるビルド タスクにより、Git リポジトリと、必要に応じて分岐および Dockerfile を指定できます。 チームがコードをリポジトリにコミットすると、ACR ビルドで作成された webhook が、リポジトリで定義されているコンテナー イメージのビルドをトリガーします。

ソース コードのコミット時にビルドをトリガーする方法については、2 番目の ACR ビルド チュートリアル、「[Automate container image builds with Azure Container Registry Build](container-registry-tutorial-build-task.md)」(Azure Container Registry ビルドを使用してコンテナー イメージ ビルドを自動化する) をご覧ください。

## <a name="automate-os-and-framework-patching"></a>OS とフレームワークの修正プログラムの適用を自動化する

ACR ビルドがコンテナー ビルド パイプラインを真に強化する能力は、基本イメージに対する更新を検出する機能に由来します。 更新された基本イメージがレジストリにプッシュされると、ACR ビルドはそれに基づいてアプリケーション イメージを自動的にビルドできます。

コンテナー イメージは、"*基本*" イメージと "*アプリケーション*" イメージに大きく分類できます。 通常、基本イメージには、アプリケーションのビルドとその他のカスタマイズの基になるオペレーティング システムとアプリケーション フレームワークが含まれます。 通常、これらの基本イメージ自体は、[Alpine Linux][base-alpine]、[Windows][base-windows]、[.NET][base-dotnet]、[Node.js][base-node] などのパブリック アップストリーム イメージに基づきます。 いくつかのアプリケーション イメージは、共通の基本イメージを共有することがあります。

重要な OS セキュリティ更新プログラムなどによって OS またはアプリ フレームワークのイメージが更新された場合は、基本イメージも更新して重要な修正を組み込む必要があります。 各アプリケーション イメージもリビルドして、基本イメージに組み込まれたこれらのアップストリームの修正プログラムを組み込む必要があります。

ACR ビルドはコンテナー イメージの構築時に基本イメージの依存関係を動的に検出するので、アプリケーション イメージのベース イメージが更新された場合にそれを検出できます。 事前に構成された 1 つの[ビルド タスク](container-registry-tutorial-base-image-update.md#create-build-task)で、ACR ビルドは**すべてアプリケーション イメージを自動的にリビルド**します。 この自動検出とリビルドにより、ACR ビルドは、更新された基本イメージを参照している各アプリケーション イメージを手動で追跡および更新するために通常必要になる時間と労力を削減します。

OS とフレームワークの修正プログラム適用については、3 番目 ACR ビルド チュートリアル、「[Automate image builds on base image update with Azure Container Registry Build](container-registry-tutorial-base-image-update.md)」(Azure Container Registry ビルドを使用して基本イメージの更新時のイメージ ビルドを自動化する) をご覧ください。

> [!NOTE]
> 初期のプレビューでは、基本イメージの更新は、基本イメージとアプリケーション イメージの両方が同じ Azure コンテナー レジストリまたはパブリックにアクセスできる Docker Hub リポジトリに存在する場合にのみビルドをトリガーします。

## <a name="next-steps"></a>次の手順

クラウドにコンテナー イメージをビルドすることで OS とフレームワークの修正プログラム適用を自動化する準備ができたら、3 部構成の ACR ビルド チュートリアル シリーズをご覧ください。

> [!div class="nextstepaction"]
> [Azure Container Registry ビルドを使用してクラウド内のコンテナー イメージをビルドする](container-registry-tutorial-quick-build.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
