---
title: Azure Spring Cloud でアクセス許可を使用する
description: この記事では、Azure Spring Cloud リソースに対するアクセス許可を委任するカスタム ロールを作成する方法を示します。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 61c4eb0322de60bb90d49bd71b111fe8b2db07ea
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015547"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Azure Spring Cloud でアクセス許可を使用する方法
この記事では、Azure Spring Cloud リソースに対するアクセス許可を委任するカスタム ロールを作成する方法を示します。 カスタム ロールは、さまざまな標準アクセス許可を使用して [Azure 組み込みロール](../role-based-access-control/built-in-roles.md)を拡張するものです。

ここでは以下のカスタム ロールを実装します。

* **開発者ロール**:

    * デプロイ
    * テスト
    * アプリを再起動する
    * Git リポジトリのアプリ構成に変更を加えて適用する
    * ログ ストリームを取得する

* **DevOps エンジニア ロール**:

    * Azure Spring Cloud ですべてを作成、読み取り、更新、削除する
* **Ops - サイト信頼性エンジニアリング ロール**:

    * アプリを再起動する
    * ログ ストリームを取得する
    * アプリや構成に変更を加えることはできない

* **Azure Pipelines、Jenkins、GitHub Actions ロール**:

    * 作成、読み取り、更新、削除の操作を実行する
    * Terraform または ARM テンプレートを使用して、サービス インスタンス内にある Azure Spring Cloud とアプリで、すべてを作成し、構成する: Azure Pipelines、Jenkins、GitHub Actions

## <a name="define-the-developer-role"></a>開発者ロールを定義する

開発者ロールには、アプリを再起動してログ ストリームを表示するアクセス許可が含まれます。 このロールは、アプリや構成に変更を加えることはできません。

#### <a name="portal"></a>[ポータル](#tab/Azure-portal)
1. Azure portal で、カスタム ロールを割り当てるサブスクリプションを開きます。
2. **[アクセス制御 (IAM)]** を開きます。
3. **[追加]** を選択します。
4. **[カスタム ロールの追加]** を選択します。
5. **[次へ]** を選択します。

   ![[カスタム ロールの作成] ウィンドウの [基本] タブを示すスクリーンショット。](media/spring-cloud-permissions/create-custom-role.png)

6. **[アクセス許可の追加]** を選択します。

   ![[アクセス許可の追加] ボタンを示すスクリーンショット。](media/spring-cloud-permissions/add-permissions.png)


7. 検索ボックスで、**Microsoft.app** を検索します。 **[Microsoft Azure Spring Cloud]** を選択します。

   ![Microsoft.app の検索結果を示すスクリーンショット。](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. 開発者ロールのアクセス許可を選択します。

   **[Microsoft.AppPlatform/Spring]** から、以下を選択します。
   * **書き込み:Azure Spring Cloud サービス インスタンスを作成または更新する**
   * **読み取り:Azure Spring Cloud サービス インスタンスを取得する**
   * **その他:Azure Spring Cloud サービス インスタンスのテスト キーを一覧表示する**

   **[Microsoft.AppPlatform/Spring/apps]** から、以下を選択します。
   * **読み取り:Microsoft Azure Spring Cloud アプリケーションを読み取る**
   * **その他:Microsoft Azure Spring Cloud アプリケーション リソースのアップロード URL を取得する**

   **[Microsoft.AppPlatform/Spring/apps/bindings]** から、以下を選択します。
   * **読み取り:Microsoft Azure Spring Cloud アプリケーション バインドを読み取る**

   **[Microsoft.AppPlatform/Spring/apps/deployments]** から、以下を選択します。
   * **書き込み:Microsoft Azure Spring Cloud アプリケーション デプロイを書き込む**
   * **読み取り:Microsoft Azure Spring Cloud アプリケーション デプロイを読み取る**
   * **その他:Microsoft Azure Spring Cloud アプリケーション デプロイを起動する**
   * **その他:Microsoft Azure Spring Cloud アプリケーション デプロイを停止する**
   * **その他:Microsoft Azure Spring Cloud アプリケーション デプロイを再起動する**
   * **その他:Microsoft Azure Spring Cloud アプリケーション デプロイのログ ファイル URL を取得する**

   **[Microsoft.AppPlatform/Spring/apps/domains]** から、以下を選択します。
   * **読み取り:Microsoft Azure Spring Cloud アプリケーションのカスタム ドメインを読み取る**

   **[Microsoft.AppPlatform/Spring/certificates]** から、以下を選択します。
   * **読み取り:Microsoft Azure Spring Cloud 証明書を読み取る**

   **[Microsoft.AppPlatform/locations/operationResults/Spring]** から、以下を選択します。
   * **読み取り:操作結果を読み取る**

   **[Microsoft.AppPlatform/locations/operationStatus/operationId]** から、以下を選択します。
   * **読み取り:操作状態を読み取る**

    [![開発者のアクセス許可の選択内容を示すスクリーンショット。](media/spring-cloud-permissions/developer-permissions-box.png)](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. **[追加]** を選択します。

10. アクセス許可を確認します。

11. **[確認と作成]** を選択します。

#### <a name="json"></a>[JSON](#tab/JSON)
1. Azure portal で、カスタム ロールを割り当てるサブスクリプションを開きます。
2. **[アクセス制御 (IAM)]** を開きます。
3. **[追加]** を選択します。
4. **[カスタム ロールの追加]** を選択します。
5. **[次へ]** を選択します。

6. **[JSON]** タブを選択します。

7. **[編集]** を選択し、既定のテキストを削除します。

   ![既定の JSON テキストを示すスクリーンショット。](media/spring-cloud-permissions/create-custom-role-edit-json.png)

8. 次の JSON を貼り付けて、開発者ロールを定義します。

   ```json
   {
     "properties": {
       "roleName": "Developer",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/domains/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/Spring/certificates/read",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

   ![開発者ロールの JSON を示すスクリーンショット。](media/spring-cloud-permissions/create-custom-role-json.png)

9. **[保存]** を選択します。

10. アクセス許可を確認します。

11. **[確認と作成]** を選択します。

---

## <a name="define-the-devops-engineer-role"></a>DevOps エンジニア ロールを定義する

この手順では、Azure Spring Cloud アプリをデプロイ、テスト、再起動するためのアクセス許可を持つロールを定義します。

#### <a name="portal"></a>[ポータル](#tab/Azure-portal)
1. 開発者ロールを追加する手順では、手順 1 から 4 を繰り返します。

2. DevOps エンジニア ロールのアクセス許可を選択します。

   **[Microsoft.AppPlatform/Spring]** から、以下を選択します。
   * **書き込み:Azure Spring Cloud サービス インスタンスを作成または更新する**
   * **削除:Azure Spring Cloud サービス インスタンスを削除する**
   * **読み取り:Azure Spring Cloud サービス インスタンスを取得する**
   * **その他:Azure Spring Cloud サービス インスタンスのテスト エンドポイントを有効にする**
   * **その他:Azure Spring Cloud サービス インスタンスのテスト エンドポイントを無効にする**
   * **その他:Azure Spring Cloud サービス インスタンスのテスト キーを一覧表示する**
   * **その他:Azure Spring Cloud サービス インスタンスのテスト キーを再生成する**

   **[Microsoft.AppPlatform/Spring/apps]** から、以下を選択します。
   * **書き込み:Microsoft Azure Spring Cloud アプリケーションを書き込む**
   * **削除:Microsoft Azure Spring Cloud アプリケーションを削除する**
   * **読み取り:Microsoft Azure Spring Cloud アプリケーションを読み取る**
   * **その他:Microsoft Azure Spring Cloud アプリケーション リソースのアップロード URL を取得する**
   * **その他:Microsoft Azure Spring Cloud アプリケーションのカスタム ドメインを検証する**

   **[Microsoft.AppPlatform/Spring/apps/bindings]** から、以下を選択します。
   * **書き込み:Microsoft Azure Spring Cloud アプリケーション バインドを書き込む**
   * **削除:Microsoft Azure Spring Cloud アプリケーション バインドを削除する**
   * **読み取り:Microsoft Azure Spring Cloud アプリケーション バインドを読み取る**

   **[Microsoft.AppPlatform/Spring/apps/deployments]** から、以下を選択します。
   * **書き込み:Microsoft Azure Spring Cloud アプリケーション デプロイを書き込む**
   * **削除:Microsoft Azure Spring Cloud アプリケーション デプロイを削除する**
   * **読み取り:Microsoft Azure Spring Cloud アプリケーション デプロイを読み取る**
   * **その他:Microsoft Azure Spring Cloud アプリケーション デプロイを起動する**
   * **その他:Microsoft Azure Spring Cloud アプリケーション デプロイを停止する**
   * **その他:Microsoft Azure Spring Cloud アプリケーション デプロイを再起動する**
   * **その他:Microsoft Azure Spring Cloud アプリケーション デプロイのログ ファイル URL を取得する**

   **[Microsoft.AppPlatform/Spring/apps/deployments/skus]** から、以下を選択します。
   * **読み取り:アプリケーション デプロイの利用可能な SKU を一覧表示する**

   **[Microsoft.AppPlatform/locations]** から、以下を選択します。
   * **その他:名前を使用できるかどうかを調べる**

   **[Microsoft.AppPlatform/locations/operationResults/Spring]** から、以下を選択します。
   * **読み取り:操作結果を読み取る**

   **[Microsoft.AppPlatform/locations/operationStatus/operationId]** から、以下を選択します。
   * **読み取り:操作状態を読み取る**

   **[Microsoft.AppPlatform/skus]** から、以下を選択します。
   * **読み取り:利用可能な SKU を一覧表示する**

   [![DevOps のアクセス許可の選択内容を示すスクリーンショット。](media/spring-cloud-permissions/dev-ops-permissions.png)](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. **[追加]** を選択します。

4. アクセス許可を確認します。

5. **[確認と作成]** を選択します。

#### <a name="json"></a>[JSON](#tab/JSON)

1. 開発者ロールを追加する手順の手順 1 から 4 を繰り返します。
2. **[次へ]** を選択します。

3. **[JSON]** タブを選択します。

4. **[編集]** を選択し、既定のテキストを削除します。

   ![既定の JSON テキストを示すスクリーンショット。](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. 次の JSON を貼り付けて、DevOps エンジニア ロールを定義します。

   ```json
   {
     "properties": {
       "roleName": "DevOps engineer",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/delete",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/enableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/disableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/regenerateTestKey/action",
             "Microsoft.AppPlatform/Spring/apps/write",
             "Microsoft.AppPlatform/Spring/apps/delete",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/validateDomain/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/write",
             "Microsoft.AppPlatform/Spring/apps/bindings/delete",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/delete",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/skus/read",
             "Microsoft.AppPlatform/locations/checkNameAvailability/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read",
             "Microsoft.AppPlatform/skus/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. アクセス許可を確認します。

7. **[確認と作成]** を選択します。

---

## <a name="define-the-ops---site-reliability-engineering-role"></a>Ops - サイト信頼性エンジニアリング ロールを定義する

この手順では、Azure Spring Cloud アプリをデプロイ、テスト、再起動するためのアクセス許可を持つロールを定義します。

#### <a name="portal"></a>[ポータル](#tab/Azure-portal)
1. 開発者ロールを追加する手順の手順 1 から 4 を繰り返します。
2. Ops - サイト信頼性エンジニアリング ロールのアクセス許可を選択します。

   **[Microsoft.AppPlatform/Spring]** から、以下を選択します。
   * **読み取り:Azure Spring Cloud サービス インスタンスを取得する**
   * **その他:Azure Spring Cloud サービス インスタンスのテスト キーを一覧表示する**

   **[Microsoft.AppPlatform/Spring/apps]** から、以下を選択します。
   * **読み取り:Microsoft Azure Spring Cloud アプリケーションを読み取る**

   **[Microsoft.AppPlatform/apps/deployments]** から、以下を選択します。
   * **読み取り:Microsoft Azure Spring Cloud アプリケーション デプロイを読み取る**
   * **その他:Microsoft Azure Spring Cloud アプリケーション デプロイを起動する**
   * **その他:Microsoft Azure Spring Cloud アプリケーション デプロイを停止する**
   * **その他:Microsoft Azure Spring Cloud アプリケーション デプロイを再起動する**

   **[Microsoft.AppPlatform/locations/operationResults/Spring]** から、以下を選択します。
   * **読み取り:操作結果を読み取る**

   **[Microsoft.AppPlatform/locations/operationStatus/operationId]** から、以下を選択します。
   * **読み取り:操作状態を読み取る**

   [![Ops - サイト信頼性エンジニアリングのアクセス許可の選択内容を示すスクリーンショット。](media/spring-cloud-permissions/ops-sre-permissions.png)](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. **[追加]** を選択します。

4. アクセス許可を確認します。

5. **[確認と作成]** を選択します。

#### <a name="json"></a>[JSON](#tab/JSON)
1. 開発者ロールを追加する手順の手順 1 から 4 を繰り返します。
2. **[次へ]** を選択します。

3. **[JSON]** タブを選択します。

4. **[編集]** を選択し、既定のテキストを削除します。

   ![既定の JSON テキストを示すスクリーンショット。](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. 次の JSON を貼り付けて、Ops - サイト信頼性エンジニアリング ロールを定義します。

   ```json
   {
     "properties": {
       "roleName": "Ops - Site Reliability Engineering",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. アクセス許可を確認します。

7. **[確認と作成]** を選択します。

---

## <a name="define-the-azure-pipelines--jenkins--github-actions-role"></a>Azure Pipelines、Jenkins、GitHub Actions ロールを定義する

このロールでは、サービス インスタンス内にある Azure Spring Cloud とアプリで、すべてを作成し、構成できます。 このロールは、コードをリリースまたはデプロイするためのものです。

#### <a name="portal"></a>[ポータル](#tab/Azure-portal)

1. 開発者ロールを追加する手順の手順 1 から 4 を繰り返します。
2. **[アクセス許可]** オプションを開きます。

3. Azure Pipelines、Jenkins、GitHub Actions ロールのアクセス許可を選択します。

   **[Microsoft.AppPlatform/Spring]** から、以下を選択します。
   * **書き込み:Azure Spring Cloud サービス インスタンスを作成または更新する**
   * **削除:Azure Spring Cloud サービス インスタンスを削除する**
   * **読み取り:Azure Spring Cloud サービス インスタンスを取得する**
   * **その他:Azure Spring Cloud サービス インスタンスのテスト エンドポイントを有効にする**
   * **その他:Azure Spring Cloud サービス インスタンスのテスト エンドポイントを無効にする**
   * **その他:Azure Spring Cloud サービス インスタンスのテスト キーを一覧表示する**
   * **その他:Azure Spring Cloud サービス インスタンスのテスト キーを再生成する**

   **[Microsoft.AppPlatform/Spring/apps]** から、以下を選択します。
   * **書き込み:Microsoft Azure Spring Cloud アプリケーションを書き込む**
   * **削除:Microsoft Azure Spring Cloud アプリケーションを削除する**
   * **読み取り:Microsoft Azure Spring Cloud アプリケーションを読み取る**
   * **その他:Microsoft Azure Spring Cloud アプリケーション リソースのアップロード URL を取得する**
   * **その他:Microsoft Azure Spring Cloud アプリケーションのカスタム ドメインを検証する**

   **[Microsoft.AppPlatform/Spring/apps/bindings]** から、以下を選択します。
   * **書き込み:Microsoft Azure Spring Cloud アプリケーション バインドを書き込む**
   * **削除:Microsoft Azure Spring Cloud アプリケーション バインドを削除する**
   * **読み取り:Microsoft Azure Spring Cloud アプリケーション バインドを読み取る**

   **[Microsoft.AppPlatform/Spring/apps/deployments]** から、以下を選択します。
   * **書き込み:Microsoft Azure Spring Cloud アプリケーション デプロイを書き込む**
   * **削除:Microsoft Azure Spring Cloud アプリケーション デプロイを削除する**
   * **読み取り:Microsoft Azure Spring Cloud アプリケーション デプロイを読み取る**
   * **その他:Microsoft Azure Spring Cloud アプリケーション デプロイを起動する**
   * **その他:Microsoft Azure Spring Cloud アプリケーション デプロイを停止する**
   * **その他:Microsoft Azure Spring Cloud アプリケーション デプロイを再起動する**
   * **その他:Microsoft Azure Spring Cloud アプリケーション デプロイのログ ファイル URL を取得する**

   **[Microsoft.AppPlatform/Spring/apps/deployments/skus]** から、以下を選択します。
   * **読み取り:アプリケーション デプロイの利用可能な SKU を一覧表示する**

   **[Microsoft.AppPlatform/locations]** から、以下を選択します。
   * **その他:名前を使用できるかどうかを調べる**

   **[Microsoft.AppPlatform/locations/operationResults/Spring]** から、以下を選択します。
   * **読み取り:操作結果を読み取る**

   **[Microsoft.AppPlatform/locations/operationStatus/operationId]** から、以下を選択します。
   * **読み取り:操作状態を読み取る**

   **[Microsoft.AppPlatform/skus]** から、以下を選択します。
   * **読み取り:利用可能な SKU を一覧表示する**

   [![Azure Pipelines、Jenkins、GitHub Actions のアクセス許可の選択内容を示すスクリーンショット。](media/spring-cloud-permissions/pipelines-permissions-box.png)](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)

4. **[追加]** を選択します。

5. アクセス許可を確認します。

6. **[確認と作成]** を選択します。

#### <a name="json"></a>[JSON](#tab/JSON)

1. 開発者ロールを追加する手順の手順 1 から 4 を繰り返します。
2. **[次へ]** を選択します。

3. **[JSON]** タブを選択します。

4. **[編集]** を選択し、既定のテキストを削除します。

   ![既定の JSON テキストを示すスクリーンショット。](media/spring-cloud-permissions/create-custom-role-edit-json.png)

5. 次の JSON を貼り付けて、Azure Pipelines、Jenkins、GitHub Actions ロールを定義します。

   ```json
   {
     "properties": {
       "roleName": "Azure Pipelines/Provisioning",
       "description": "",
       "assignableScopes": [
         "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
       ],
       "permissions": [
         {
           "actions": [
             "Microsoft.AppPlatform/Spring/write",
             "Microsoft.AppPlatform/Spring/delete",
             "Microsoft.AppPlatform/Spring/read",
             "Microsoft.AppPlatform/Spring/enableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/disableTestEndpoint/action",
             "Microsoft.AppPlatform/Spring/listTestKeys/action",
             "Microsoft.AppPlatform/Spring/regenerateTestKey/action",
             "Microsoft.AppPlatform/Spring/apps/write",
             "Microsoft.AppPlatform/Spring/apps/delete",
             "Microsoft.AppPlatform/Spring/apps/read",
             "Microsoft.AppPlatform/Spring/apps/getResourceUploadUrl/action",
             "Microsoft.AppPlatform/Spring/apps/validateDomain/action",
             "Microsoft.AppPlatform/Spring/apps/bindings/write",
             "Microsoft.AppPlatform/Spring/apps/bindings/delete",
             "Microsoft.AppPlatform/Spring/apps/bindings/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/write",
             "Microsoft.AppPlatform/Spring/apps/deployments/delete",
             "Microsoft.AppPlatform/Spring/apps/deployments/read",
             "Microsoft.AppPlatform/Spring/apps/deployments/start/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/stop/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/restart/action",
             "Microsoft.AppPlatform/Spring/apps/deployments/getLogFileUrl/action",
             "Microsoft.AppPlatform/skus/read",
             "Microsoft.AppPlatform/locations/checkNameAvailability/action",
             "Microsoft.AppPlatform/locations/operationResults/Spring/read",
             "Microsoft.AppPlatform/locations/operationStatus/operationId/read"
           ],
           "notActions": [],
           "dataActions": [],
           "notDataActions": []
         }
       ]
     }
   }
   ```

6. **[追加]** を選択します。

7. アクセス許可を確認します。

---

## <a name="see-also"></a>関連項目

* [Azure portal を使用して Azure カスタム ロールを作成または更新する](../role-based-access-control/custom-roles-portal.md)

カスタム アクセス許可を定義する 3 つの方法の詳細については、以下を参照してください。
* [ロールを複製する](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [最初から行う](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [JSON から始める](../role-based-access-control/custom-roles-portal.md#start-from-json)
