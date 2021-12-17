---
title: CI/CD ワークフロー内のコンテナー イメージに対する Defender for Cloud の脆弱性スキャナー
description: Microsoft Defender for container registries を使用して CI/CD ワークフローでコンテナー イメージをスキャンする方法について説明します。
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: how-to
ms.service: defender-for-cloud
manager: rkarlin
ms.openlocfilehash: b7b30e6702b4c2c5f899f6ea1fb584dc9cbd0927
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525595"
---
# <a name="identify-vulnerable-container-images-in-your-cicd-workflows"></a>CI/CD ワークフロー内の脆弱なコンテナー イメージを特定する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

このページでは、Azure Container Registry ベースのコンテナー イメージが GitHub ワークフローの一部として構築されている場合に、統合された脆弱性スキャナーを使用してスキャンする方法について説明します。

スキャナーを設定するには、**コンテナー レジストリ用 Microsoft Defender** と CI/CD 統合を有効にする必要があります。 CI/CD ワークフローでイメージがレジストリにプッシュされると、レジストリ スキャンの結果と CI/CD スキャン結果の概要を表示できます。

CI/CD スキャンの結果は、Qualys による既存のレジストリ スキャン結果をエンリッチしたものです。 Defender for Cloud の CI/CD スキャンは、[Aqua Trivy](https://github.com/aquasecurity/trivy) を利用しています。

脆弱性のあるイメージが発生しているワークフローを特定するのに役立てるため、GitHub ワークフローや GitHub の実行 URL などの追跡可能性情報を取得します。

> [!TIP]
> レジストリのスキャンで特定された脆弱性は、CI/CD スキャンの結果と異なる可能性があります。 これらの違いの理由の 1 つとして、レジストリのスキャンが [継続的](defender-for-container-registries-introduction.md#when-are-images-scanned)であるのに対し、CI/CD スキャンはワークフローがイメージをレジストリにプッシュする直前に行われる点が挙げられます。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:| **この CI/CD 統合はプレビュー段階です。**<br>非運用ワークフローでのみ試されることをお勧めします。<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|価格:|**Microsoft Defender for container registries** の課金については、[価格に関するページ](https://azure.microsoft.com/pricing/details/security-center/)をご覧ください。|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/no-icon.png"::: 各国 (Azure Government、Azure China 21Vianet)|
|||

## <a name="prerequisites"></a>前提条件

CI/CD ワークフローによってレジストリにプッシュされたときにイメージをスキャンするには、サブスクリプションで **コンテナー レジストリ用 Microsoft Defender**  を有効にする必要があります。

## <a name="set-up-vulnerability-scanning-of-your-cicd-workflows"></a>CI/CD ワークフローの脆弱性スキャンを設定する

GitHub ワークフローでイメージの脆弱性スキャンを有効にするには:

[手順1.Defender for CloudでCI/CD 統合を有効にします。](#step-1-enable-the-cicd-integration-in-defender-for-cloud)

[手順 2. 必要な行を GitHub ワークフローに追加する](#step-2-add-the-necessary-lines-to-your-github-workflow-and-perform-a-scan)

### <a name="step-1-enable-the-cicd-integration-in-defender-for-cloud"></a>手順 1. Defender for Cloudで CI/CD統合を有効にします。

1. [Defender for Cloud] メニューで、 **[環境設定]** を開きます。
1. 関連するサブスクリプションを選択します。
1. そのサブスクリプションの設定ページのサイド バーで、 **[Integrations]\(統合\)** を選択します。
1. 表示されたペインで、ワークフローから CI/CD スキャンの結果をプッシュする Application Insights アカウントを選択します。
1. 認証トークンと接続文字列を GitHub ワークフローにコピーします。

    :::image type="content" source="./media/defender-for-container-registries-cicd/enable-cicd-integration.png" alt-text="GitHub ワークフローでコンテナー イメージの脆弱性スキャンに対して CI/CD 統合を有効にします。" lightbox="./media/defender-for-container-registries-cicd/enable-cicd-integration.png":::

    > [!IMPORTANT]
    > 認証トークンと接続文字列は、取り込まれたセキュリティ テレメトリをサブスクリプション内のリソースと関連付けるために使用されます。 これらのパラメーターに無効な値を使用すると、テレメトリがドロップされます。

### <a name="step-2-add-the-necessary-lines-to-your-github-workflow-and-perform-a-scan"></a>手順 2. 必要な行を GitHub ワークフローに追加し、スキャンを実行する

1. GitHub ワークフローから、次のように CI/CD スキャンを有効にします。

    > [!TIP]
    > 次に示すように、YAML ファイルで参照するためにリポジトリに 2 つのシークレットを作成することをお勧めします。 シークレットには、独自の名前付け規則に従って名前を付けることができます。 この例では、シークレットは **AZ_APPINSIGHTS_CONNECTION_STRING** および **AZ_SUBSCRIPTION_TOKEN** として参照されています。

    > [!IMPORTANT]
    >  レジストリへのプッシュは、結果が発行される前に行う必要があります。

    ```yml
    - run: |
      echo "github.sha=$GITHUB_SHA"
      docker build -t githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
    
    - uses: Azure/container-scan@v0 
      name: Scan image for vulnerabilities
      id: container-scan
      continue-on-error: true
      with:
        image-name: githubdemo1.azurecr.io/k8sdemo:${{ github.sha }} 
    
    - name: Push Docker image - githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
      run: |
      docker push githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
    
    - name: Post logs to appinsights
      uses: Azure/publish-security-assessments@v0
      with: 
        scan-results-path: ${{ steps.container-scan.outputs.scan-report-path }}
        connection-string: ${{ secrets.AZ_APPINSIGHTS_CONNECTION_STRING }}
        subscription-token: ${{ secrets.AZ_SUBSCRIPTION_TOKEN }} 
    ```

1. 選択したコンテナー レジストリにイメージをプッシュするワークフローを実行します。 イメージがレジストリにプッシュされると、レジストリのスキャンが実行され、CI/CD スキャンの結果とレジストリ スキャンの結果を Microsoft Defender for Cloud内で表示できます。

1. [CI/CD スキャンの結果を表示します](#view-cicd-scan-results)。

## <a name="view-cicd-scan-results"></a>CI/CD スキャンの結果を表示する

1. 結果を表示するために、 **[推奨事項]** ページへ移動します。 問題が見つかった場合は、**Azure Container Registry イメージの脆弱性を修復する必要があります** という推奨事項が表示されます。

    ![問題を修復するための推奨事項。](media/monitor-container-security/acr-finding.png)

1. 推奨事項を選択します。

    追加情報を示した推奨事項の詳細ページが開きます。 この情報には、脆弱性のあるイメージ ("影響を受けるリソース") を含むレジストリの一覧とその修復手順が含まれます。

1. **影響を受けるリソース** の一覧を開き、異常なレジストリを選択して、その中の脆弱性のあるイメージを含むリポジトリを表示します。

    :::image type="content" source="media/defender-for-container-registries-cicd/select-registry.png" alt-text="異常なレジストリを選択します。":::

    影響を受けるリポジトリの一覧を示したレジストリの詳細ページが開きます。

1. 特定のリポジトリを選択して、その中の脆弱性のあるイメージを含むリポジトリを確認します。

    :::image type="content" source="media/defender-for-container-registries-cicd/select-repository.png" alt-text="異常なリポジトリを選択します。":::

    リポジトリの詳細ページが開きます。 脆弱性のあるイメージが、結果の重大度の評価と共に一覧表示されます。

1. 脆弱性を表示するには、特定のイメージを選択します。

    :::image type="content" source="media/defender-for-container-registries-cicd/select-image.png" alt-text="異常なイメージを選択します。":::

    選択されたイメージに対応する結果の一覧が開きます。

    :::image type="content" source="media/defender-for-container-registries-cicd/cicd-scan-results.png" alt-text="イメージ スキャンの結果。":::

1. どの GitHub ワークフローがこれらの脆弱性のあるイメージをプッシュしているかについて詳しく知るには、情報バブルを選択します。

    :::image type="content" source="media/defender-for-container-registries-cicd/cicd-findings.png" alt-text="特定の GitHub ブランチとコミットに関する CI/CD の結果。":::

## <a name="next-steps"></a>次の手順

[Microsoft Defender for Cloud の高度な保護プラン](defender-for-cloud-introduction.md)の詳細をご覧ください。
