---
title: Azure Dev Spaces と共に CI/CD を使用する | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: article
manager: yuvalm
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, コンテナー
ms.openlocfilehash: 0abe2902248c8203046cfe891d136ca7d5d0a75b
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665973"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Azure Dev Spaces と共に CI/CD を使用する

この記事では、Dev Spaces を有効にした状態で、Azure Kubernetes Service (AKS) への継続的インテグレーション/継続的配置 (CI/CD) を設定する方法について説明します。 AKS への CI/CD によって、コミットされたコードがソース リポジトリにプッシュされるたびに、アプリの更新プログラムを自動的にデプロイすることが可能になります。 Dev Spaces を有効にしたクラスターと組み合わせて CI/CD を利用すると、チームが操作するアプリケーションのベースラインを最新の状態に保つことができるので便利です。

![サンプルの CI/CD の図](../media/common/ci-cd-simple.png)

この記事では、Azure DevOps を使って説明していますが、Jenkins、TeamCity などの CI/CD システムにも同じ概念が適用されます。

## <a name="prerequisites"></a>前提条件
* [Azure Dev Spaces が有効になっている Azure Kubernetes Service (AKS) クラスター](../get-started-netcore.md)
* [インストール済みの Azure Dev Spaces CLI](upgrade-tools.md)
* [1 つのプロジェクトを含む Azure DevOps 組織](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Azure Container Registry [管理者アカウント](../../container-registry/container-registry-authentication.md#admin-account)の詳細が利用可能
* [お使いの Azure コンテナー レジストリからプルするように AKS クラスターを承認する](../../container-registry/container-registry-auth-aks.md)

## <a name="download-sample-code"></a>サンプル コードのダウンロード
時間を節約するために、サンプル コードの GitHub リポジトリのフォークを作成しましょう。 https://github.com/Azure/dev-spaces に移動して、**[フォーク]** を選択します。 フォーク プロセスが完了すると、リポジトリのフォーク済みバージョンがローカルに**複製**されます。 既定で _master_ ブランチがチェックアウトされますが、_azds_updates_ ブランチに時間を節約するためのいくつかの変更を組み入れてあり、フォーク時にこのブランチも転送されているはずです。 _azds_updates_ ブランチには、Dev Spaces チュートリアルのセクション内で手動実行するように示されている更新プログラムと、CI/CD システムのデプロイを効率化するために事前作成されたいくつかの YAML および JSON ファイルが含まれています。 `git checkout -b azds_updates origin/azds_updates` のようなコマンドを使用して、ローカル リポジトリに _azds_updates_ ブランチをチェックアウトできます。

## <a name="dev-spaces-setup"></a>Dev Spaces の設定
`azds space select` コマンドを使用して、_dev_ という新しい空間を作成します。 _dev_ 空間は、コードの変更をプッシュするために、CI/CD パイプラインによって使用されます。 また、_dev_ に基づく "_子空間_" を作成するためにも使用されます。

```cmd
azds space select -n dev
```

親 dev 空間を選択するように要求されたら、"_\<なし\>_" を選択します。

開発者が _dev_ から "_子空間_" を作成して大規模なアプリのコンテキスト内にある分離された変更をテストできるように、_dev_ 空間には常に最新状態のリポジトリが含まれます。 この概念については、Dev Spaces のチュートリアルで詳しく説明しています。

## <a name="creating-the-build-definition"></a>ビルド定義の作成
Web ブラウザーで Azure DevOps チーム プロジェクトを開き、"_[パイプライン]_" セクションに移動します。 最初に、Azure DevOps サイトの右上にあるプロファイル写真をクリックして、[プレビュー機能] ウィンドウを開き、"_[YAML パイプライン作成の新しいエクスペリエンス]_" を無効にします。

![[プレビュー機能] ウィンドウを開く](../media/common/preview-feature-open.png)

無効にするオプション:

![[YAML パイプライン作成の新しいエクスペリエンス] オプション](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> Azure DevOps の "_YAML パイプライン作成の新しいエクスペリエンス_" プレビュー機能は、この時点では、事前定義されたビルド パイプラインの作成と競合します。 事前定義されたビルド パイプラインをデプロイするために、今は無効にしておく必要があります。

_azds_updates_ ブランチには、*mywebapi* および *webfrontend* に必要なビルド手順を定義した簡単な [Azure パイプラインの YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) を組み込んでいます。

選択した言語に応じて、パイプラインの YAML は `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml` と類似のパスにチェックインされています。

このファイルからパイプラインを作成するには:
1. DevOps プロジェクトのメイン ページ上で、[パイプライン] > [ビルド] の順に移動します。
1. **新しい**ビルド パイプラインを作成するオプションを選択します。
1. ソースとして **[GitHub]** を選択し、必要に応じてご自身の GitHub アカウントを使って承認して、dev 空間のサンプル アプリ リポジトリのフォーク済みバージョンから _azds_updates_ ブランチを選択します。
1. テンプレートとして、**[コードとしての構成]** または **[YAML]** を選択します。
1. この時点で、ビルド パイプラインの構成ページが表示されています。 前述したように、**[YAML ファイル パス]** に言語固有のパスを入力します。 たとえば、`samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml` のように指定します。
1. [変数] タブに移動します。
1. _dockerId_ を変数として手動で追加します。[Azure Container Registry 管理者アカウント](../../container-registry/container-registry-authentication.md#admin-account)のユーザー名です  (記事の「前提条件」に記載されています)。
1. _dockerPassword_ を変数として手動で追加します。[Azure Container Registry 管理者アカウント](../../container-registry/container-registry-authentication.md#admin-account)のパスワードです。 セキュリティのために、_dockerPassword_ は必ずシークレットとして指定 (鍵のアイコンを選択) します。
1. **[保存してキューに登録]** を選択します。

これで、GitHub フォークの _azds_updates_ ブランチにプッシュされた任意の更新プログラムに対して *mywebapi* および *webfrontend* を自動的にビルドする CI ソリューションが作成されました。 Azure portal に移動し、お使いの Azure コンテナー レジストリを選択し、"_[リポジトリ]_" タブを参照して、Docker イメージがプッシュされたことを確認できます。

![Azure Container Registry リポジトリ](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>リリース定義の作成

1. DevOps プロジェクトのメイン ページ上で、[パイプライン] > [リリース] の順に移動します。
1. まだリリース定義を含まないまったく新しい DevOps プロジェクトに取り組んでいる場合は、先へ進む前に、まず空のリリース定義を作成する必要があります。 既存のリリース定義ができるまで、[インポート] オプションは UI に表示されません。
1. 左側にある **[+ 新規]** ボタンをクリックして、**[パイプラインのインポート]** をクリックします。
1. `samples/release.json` の .json ファイルを選択します。
1. [OK] をクリックします。 [パイプライン] ウィンドウがリリース定義の編集ページと共に読み込まれたことが確認できます。 また、引き続き構成する必要があるクラスター固有の詳細を示した赤い警告アイコンがいくつかあることが確認できます。
1. [パイプライン] ウィンドウの左側で、**[成果物の追加]** バブルをクリックします。
1. **[ソース]** ドロップダウンで、このドキュメントで少し前に作成したビルド パイプラインを選択します。
1. **[既定のバージョン]** には、**[Latest from the build pipeline default branch]\(ビルド パイプラインの既定のブランチからの最新版\)** を選択することをお勧めします。 タグを指定する必要はありません。
1. **[ソース エイリアス]** を `drop` に設定します。 事前定義済みのリリース タスクでは **[ソース エイリアス]** を使用するため、必ず設定しておく必要があります。
1. **[追加]** をクリックします。
1. 次に、新しく作成された `drop` アーティファクト ソース上にある、以下に示すような稲妻アイコンをクリックします。

    ![リリース アーティファクトの継続的配置の設定](../media/common/release-artifact-cd-setup.png)
1. **[継続的配置トリガー]** を有効にします。
1. 次に、[タスク] ウィンドウに移動します。 _dev_ ステージが選択され、以下のような 3 つの赤いドロップダウン コントロールが表示されています。

    ![リリース定義の設定](../media/common/release-setup-tasks.png)
1. Azure Dev Spaces と共に使用している Azure サブスクリプション、リソース グループ、およびクラスターを指定します。
1. この時点で、もう 1 つのセクションとして **[エージェント ジョブ]** セクションだけが、赤く示されています。 そこへ移動して、このステージのエージェント プールとして **[Hosted Ubuntu 1604]\(ホストされている Ubuntu 1604\)** を指定します。
1. 最上部の [タスク] セレクターにマウスを合わせて、_[prod]_ を選択します。
1. Azure Dev Spaces と共に使用している Azure サブスクリプション、リソース グループ、およびクラスターを指定します。
1. **[エージェント ジョブ]** の下で、エージェント プールとして **[Hosted Ubuntu 1604]\(ホストされている Ubuntu 1604\)** を構成します。
1. 右上にある **[保存]** をクリックし、**[OK]** をクリックします。
1. **[+ リリース]** ([保存] ボタンの横にある)、**[リリースの作成]** の順にクリックします。
1. [アーティファクト] セクション内でビルド パイプラインからの最新のビルドが選択されていることを確認して、**[作成]** をクリックします。

これで、自動化されたリリース プロセスが開始され、_dev_ の最上レベルの空間にある Kubernetes クラスターに、*mywebapi* および *webfrontend* のチャートがデプロイされます。 Azure DevOps の Web ポータル上で、リリースの進行状況を監視できます。

> [!TIP]
> "*UPGRADE FAILED: timed out waiting for the condition*" のようなエラー メッセージでリリースが失敗した場合、[Kubernetes ダッシュボードを使用して](../../aks/kubernetes-dashboard.md)、ご自身のクラスター内のポッドを調べてみてください。 ポットがエラーになっており、エラー メッセージが "*Failed to pull image "azdsexample.azurecr.io/mywebapi:122": rpc error: code = Unknown desc = Error response from daemon:Get https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: unauthorized: authentication required*" のように始まっている場合、クラスターがお使いの Azure コンテナー レジストリからプルするための承認を受けていないことが原因になっている可能性があります。 「[お使いの Azure コンテナー レジストリからプルするように AKS クラスターを承認する](../../container-registry/container-registry-auth-aks.md)」の前提条件を完了していることを確認してください。

これで、Dev Spaces サンプル アプリの GitHub フォークに対応する CI/CD パイプラインを完全に自動化できました。 コードをコミットしてプッシュするたびに、ビルド パイプラインでは *mywebapi* および *webfrontend* イメージをビルドして、お使いのカスタム ACR インスタンスにプッシュします。 その後、リリース パイプラインでは、アプリごとの Helm チャートを Dev Spaces が有効になっているクラスター上の _dev_ 空間にデプロイします。

## <a name="accessing-your-dev-services"></a>_dev_ サービスへのアクセス
デプロイ後は、`http://dev.webfrontend.<hash>.<region>.aksapp.io` のようなパブリック URL を使って、*webfrontend* の _dev_ バージョンにアクセスできます。

*kubectl* CLI を使用して、この URL を見つけることができます。
```cmd
kubectl get ingress -n dev webfrontend -o=jsonpath="{.spec.rules[0].host}"
```

## <a name="deploying-to-production"></a>運用環境へのデプロイ
リリース定義上で **[編集]** をクリックすると、_prod_ ステージが定義されていることが確認できます。

![運用環境のステージ](../media/common/prod-env.png)

このチュートリアルで作成した CI/CD システムを使用して、特定のリリースを _prod_ に手動で昇格するには:
1. DevOps ポータル上で以前に成功したリリースを開きます。
1. 'prod' ステージにマウスを合わせます。
1. [デプロイ] を選択します。

![運用環境への昇格](../media/common/prod-promote.png)

CI/CD パイプラインのサンプルでは、デプロイされている環境に応じて *webfrontend* の DNS プレフィックスを変更するために、変数を利用しています。 そのため、'prod' のサービスにアクセスするには、`http://prod.webfrontend.<hash>.<region>.aksapp.io` のような URL を使用できます。

デプロイ後は、次の *kubectl* CLI を使用して、この URL を見つけられます。<!-- TODO update below to use list-uris when the product has been updated to list non-azds ingresses #769297 -->

```cmd
kubectl get ingress -n prod webfrontend -o=jsonpath="{.spec.rules[0].host}"
```

## <a name="dev-spaces-instrumentation-in-production"></a>運用環境における Dev Spaces のインストルメンテーション
Dev Spaces のインストルメンテーションは、アプリケーションの通常の操作を妨害 "_しない_" ように設計されていますが、Dev Spaces が有効になっていない Kubernetes 名前空間内で運用環境のワークロードを実行することをお勧めします。 このタイプの Kubernetes 名前空間を使用する場合は、`kubectl` CLI を使用して運用環境の名前空間を作成するか、CI/CD システムによって最初の Helm デプロイ中にこれを作成できるようにしておく必要があります。 空間を "_選択する_" か、Dev Spaces ツールを使用して空間を作成すると、Dev Spaces インストルメンテーションがその名前空間に追加されます。

次に、機能のデプロイ、'dev' 環境、"_および_" 運用環境のすべてを単一の Kubernetes クラスター内でサポートする名前空間の構造のサンプルを示します。

![名前空間の構造のサンプル](../media/common/cicd-namespaces.png)

> [!Tip]
> `prod` 空間を既に作成済みであり、単純に Dev Spaces インストルメンテーションから (削除せずに) 除外したい場合、次の Dev Spaces CLI コマンドを使ってこれを実行できます。
>
> `azds space remove -n prod --no-delete`
>
> これを実行した後に `prod` 名前空間にあるすべてのポッドの削除が必要になる場合があります。そのため、Dev Spaces インストルメンテーションなしでの再作成が可能になっています。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Dev Spaces を使用したチーム開発について学ぶ](../team-development-netcore.md)