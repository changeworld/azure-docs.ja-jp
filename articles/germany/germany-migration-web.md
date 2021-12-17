---
title: Azure Web リソースを Azure Germany からグローバル Azure に移行する
description: この記事では、Azure Web リソースの Azure Germany からグローバル Azure への移行に関する情報を提供します。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: d67a4aadbe22b5447aa73063e48c0ca43fb45d04
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029209"
---
# <a name="migrate-web-resources-to-global-azure"></a>Web リソースをグローバル Azure に移行する

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

この記事には、Azure Web リソースの Azure Germany からグローバル Azure への移行に役立つ可能性のある情報が含まれています。

## <a name="web-apps"></a>Web Apps

現時点では、Azure App Service の Web Apps 機能を使用して作成したアプリの Azure Germany からグローバル Azure への移行はサポートされていません。 Web アプリを Azure Resource Manager テンプレートとしてエクスポートすることをお勧めします。 次に、場所プロパティを新しい宛先リージョンに変更した後に、再デプロイします。

> [!IMPORTANT]
> 新しいリージョンと一貫性を維持できるように、場所、Azure Key Vault シークレット、証明書、およびその他の GUID を変更します。

### <a name="migrate-web-app-resource"></a>Web アプリ リソースを移行する

1. Azure Germany サブスクリプションから [Web アプリと App Service プランをテンプレートとしてエクスポートします](../azure-resource-manager/templates/export-template-portal.md)。 Web アプリ リソース グループで移行するリソースを選択し、テンプレートとしてエクスポートします。
1. テンプレートを ZIP ファイルとしてダウンロードします。 
1. **template.json** ファイル内の location プロパティを編集して、ターゲットの Azure グローバル リージョンを指定します。 たとえば、次の JSON ファイルのターゲットの場所は "*米国西部*" です。

    ```json
        "resources": [
        {
            "type": "Microsoft.Web/serverfarms",
            "apiVersion": "2018-02-01",
            "name": "[parameters('serverfarms_myappservice_name')]",
            "location": "West US",

    ```
1. 変更したテンプレートを Azure グローバルにデプロイします。 たとえば、PowerShell を使用してデプロイできます。

    ```powershell
    az deployment group create --name "<web app name>" \
        --resource-group "<resource group name>" \
        --template-file "<path of your template.json file>"
    ```

### <a name="migrate-web-app-content"></a>Web アプリのコンテンツを移行する

1. Azure Germany portal で Web アプリを選択します。
1. **[開発ツール] > [高度なツール]** を選択します。
1. 上部のメニューから **[デバッグ コンソール]** を選択し、次に **[PowerShell]** を選択します。
1. **[サイト]** を選択します。
1. **wwwroot** フォルダーの横にある **ダウンロード アイコン** を選択します。 ダウンロードした ZIP ファイルには、Web アプリのソース コードが含まれています。
1. 移行された Azure グローバル Web アプリに Web ルートをデプロイします。 たとえば、次の PowerShell スクリプトを使用できます。

    ``` powershell
    az webapp deployment source config-zip \
        --resource-group "<resource group name>" \
        --name "<web App name>" \
        --src "path to webroot folder zip file"
    ```

詳細情報:

- [App Service のチュートリアル](../app-service/tutorial-dotnetcore-sqldb-app.md)を完了することによって知識を更新してください。
- [Azure Resource Manager テンプレートをエクスポートする](../azure-resource-manager/templates/export-template-portal.md)方法に関する情報を取得します。
- 「[Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)」を確認してください。
- 「[App Service の概要](../app-service/overview.md)」を確認してください。
- [Azure の場所の概要](https://azure.microsoft.com/global-infrastructure/locations/)を確認してください。
- [テンプレートを再デプロイする](../azure-resource-manager/templates/deploy-powershell.md)方法を説明します。

## <a name="notification-hubs"></a>Notification Hubs

ある Azure Notification Hubs インスタンスから別のインスタンスに設定を移行するには、すべての登録トークンをそれらのタグと共に、エクスポートしてインポートします。

1. Azure Blob ストレージ コンテナーに[既存の通知ハブの登録をエクスポート](/previous-versions/azure/azure-services/dn790624(v=azure.100))します。
1. ターゲット環境に新しい通知ハブを作成します。
1. BLOB ストレージから新しい通知ハブに、[登録トークンをインポート](/previous-versions/azure/azure-services/dn790624(v=azure.100))します。

詳細情報:

- [Notification Hubs のチュートリアル](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)を完了して、新しい知識を得ましょう。
- [Notification Hubs の概要](../notification-hubs/notification-hubs-push-notification-overview.md)を確認します。

## <a name="event-hubs"></a>Event Hubs

Azure イベント ハブを移行するには、イベント ハブ リソース テンプレートを Azure Germany からエクスポートした後、テンプレートをグローバル Azure にデプロイします。

1. Azure Germany サブスクリプションから[イベント ハブをテンプレートとしてエクスポートします](../azure-resource-manager/templates/export-template-portal.md)。
1. グローバル Azure サブスクリプションに[イベント ハブ テンプレートをカスタム テンプレートとしてデプロイします](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)。 Azure Germany サブスクリプションからエクスポートしたテンプレートを読み込んでデプロイします。

詳細情報:

- [Event Hubs の概要](../event-hubs/event-hubs-about.md)を確認してください。
- 「[Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)」を確認してください。
- [Azure Resource Manager テンプレートをエクスポートする](../azure-resource-manager/templates/export-template-portal.md)方法に関する情報を取得します。
- [テンプレートを再デプロイする](../azure-resource-manager/templates/deploy-powershell.md)方法を説明します。

## <a name="next-steps"></a>次のステップ

次のサービス カテゴリのリソースを移行するためのツール、テクニック、および推奨事項について学習します。

- [Compute](./germany-migration-compute.md)
- [ネットワーク](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [データベース](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [統合](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [Security](./germany-migration-security.md)
- [管理ツール](./germany-migration-management-tools.md)
- [メディア](./germany-migration-media.md)