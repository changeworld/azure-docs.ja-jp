---
title: Azure Marketplace 上で IoT Edge モジュール オファーのプランの技術的な構成を設定する
description: Azure Marketplace 上で IoT Edge モジュール オファーのプランの技術的な構成を設定します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 05/21/2021
ms.openlocfilehash: 28e9bbcb4086cc9a9bd5e18ef27eb1264f086e29
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070301"
---
# <a name="set-plan-technical-configuration-for-an-iot-edge-module-offer"></a>IoT Edge モジュール オファーのプランの技術的な構成を設定する

IoT Edge モジュール オファーの種類は、IoT Edge デバイス上で実行されているコンテナーの特定の種類です。 プラン **[技術的な構成]** タブを使用すると、[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) 内のコンテナー イメージ リポジトリの参照情報と、顧客がモジュールを使用するときに役立つ構成設定を指定できます。

オファーを発行した後、特定のパブリック コンテナー レジストリで IoT Edge コンテナー イメージが Azure Marketplace にコピーされます。 Azure ユーザーがモジュールの使用を要求すると、プライベート コンテナー レジストリではなく、Azure Marketplace パブリック コンテナー レジストリからサービスが提供されます。

複数のプラットフォームを対象にしたり、タグを利用し、複数のバージョンのモジュール コンテナー イメージを提供したりできます。 タグとバージョン管理の詳細については、「[IoT Edge モジュールの技術アセットの準備](iot-edge-technical-asset.md)」を参照してください。

## <a name="image-repository-details"></a>イメージ リポジトリの詳細

イメージ ソースとして **[Azure Container Registry]** を選択します。

リソースの使用状況が報告され、コンテナー イメージが含まれる Azure Container Registry に対してサービスが課金される **Azure サブスクリプション ID** を指定します。 この ID は Azure portal の[サブスクリプション ページ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)にあります。

Azure Container Registry とコンテナー イメージが含まれる [**Azure リソース グループ名**](../azure-resource-manager/management/manage-resource-groups-portal.md)を指定します。 リソース グループにはサブスクリプション ID (上記) でアクセスできます。 この名前は Azure portal の[リソース グループ](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) ページにあります。

コンテナー イメージが含まれる [**Azure コンテナー レジストリ名**](../container-registry/container-registry-intro.md)を指定します。 このコンテナー レジストリは、前に指定した Azure リソース グループに存在する必要があります。 完全なログイン サーバー名ではなく、レジストリ名のみを指定します。 名前から **azurecr.io** を省略します。 レジストリ名は、Azure portal の[コンテナー レジストリ ページ](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)にあります。

コンテナー イメージが含まれる Azure Container Registry に関連付けられている [**Azure Container Registry の管理者ユーザー名**](../container-registry/container-registry-authentication.md#admin-account)を指定します。 このユーザー名とパスワード (次の手順) は、会社にレジストリへのアクセス権があることを確実にするために必要です。 管理者ユーザー名とパスワードを取得するには、Azure コマンド ライン インターフェイス (CLI) で **admin-enabled** プロパティを **[True]** に設定します。 任意で、Azure portal で **[管理者ユーザー]** を **[有効にする]** に設定できます。

:::image type="content" source="media/iot-edge/example-iot-update-container-registry.png" alt-text="[コンテナー レジストリの更新] ダイアログ ボックスの画像。":::

#### <a name="call-out-description"></a>コールアウトの説明

1. 管理者ユーザー

<br>コンテナー イメージが含まれる Azure Container Registry に関連付けられている管理者ユーザー名の **Azure Container Registry の管理者パスワード** を指定します。 会社でレジストリへのアクセスを確保するには、ユーザー名とパスワードが必要です。 **[コンテナー レジストリ]** 、 **[アクセス キー]** の順に移動するか、Azure CLI で [show コマンド](/cli/azure/acr/credential#az-acr-credential-show)を使用することで、Azure portal からパスワードを取得できます。

:::image type="content" source="media/iot-edge/example-iot-access-keys.png" alt-text="Azure portal のアクセス キー画面の画像。":::

#### <a name="call-out-descriptions"></a>コールアウトの説明

1. アクセス キー
2. ユーザー名
3. Password

イメージが含まれる **Azure Container Registry 内のリポジトリ名** を指定します。 レジストリにイメージをプッシュするとき、リポジトリの名前を指定します。 リポジトリの名前は、[[コンテナー レジストリ]](https://azure.microsoft.com/services/container-registry/) の **リポジトリ ページ** に移動すると見つかります。 詳細については、「[Azure Portal でコンテナー レジストリ リポジトリを表示する](../container-registry/container-registry-repositories.md)」を参照してください。 設定した名前は変更できません。 アカウント内のオファー別に一意の名前を使用します。

> [!NOTE]
> Encrypted Azure Container Registry for Edge Module Certification はサポートしていません。 Azure Container Registry は暗号化を有効にせずに作成してください。

## <a name="image-versions"></a>イメージ バージョン

更新プログラムを公開したとき、顧客は Azure Marketplace から更新プログラムを自動的に取得できなければなりません。 彼らが更新しない場合、特定のバージョンのイメージを維持できなければなりません。 これはイメージを更新するたびに新しいイメージ タグを追加することで行います。

**[Add Image version]\(イメージ バージョンの追加\)** を選択して、サポートされているすべてのプラットフォームでイメージの最新バージョンをポイントする **イメージ タグ** を含めます。 バージョン タグも含める必要があります (たとえば、xx.xx.xx から始まり、xx は数字です)。 複数のプラットフォームを対象とするには、顧客は[マニフェスト タグ](https://github.com/estesp/manifest-tool)を使用する必要があります。 マニフェスト タグで参照されるすべてのタグも、アップロードできるように追加する必要があります。 マニフェスト タグ (最新のタグを除く) はすべて X.Y- か X.Y.Z- で始める必要があります。X、Y、Z は整数です。 たとえば、最新タグが `1.0.1-linux-x64`、`1.0.1-linux-arm32`、`1.0.1-windows-arm32` を指している場合、これらの 6 つのタグをこのフィールドに追加する必要があります。 タグとバージョン管理の詳細については、「[IoT Edge モジュールの技術資産の準備](iot-edge-technical-asset.md)」を参照してください。

> [!TIP]
> テスト タグをイメージに追加すると、テスト中にイメージを識別できます。

## <a name="default-deployment-settings"></a>既定のデプロイ設定

IoT Edge モジュールをデプロイするための最も一般的な設定を定義します (省略可能)。 こうした既定設定によって IoT Edge モジュールをすぐに起動できるようにし、お客様のデプロイを最適化します。

**既定のルート**。 IoT Edge ハブは、モジュール、IoT ハブ、デバイス間の通信を管理します。 モジュールと IoT Hub の間でデータの入力と出力の既定のルートを設定することにより、メッセージの処理や追加のコードの記述を行うための追加のサービスを必要とせず、自由な場所にメッセージを送信できます。 ルートは名前と値のペアを使用して作成されます。 最大 5 つの既定ルート名を定義できます。それぞれ最大 512 文字を使用できます。

ルート値に正しい[ルート構文](../iot-edge/module-composition.md#declare-routes)を使用します (通常、FROM/message/* INTO $upstream として定義されています)。 つまり、モジュールにより送信されるメッセージはすべて、IoT Hub に移動します。 モジュールを参照するには、既定のモジュール名を使用します。モジュール名は、スペースと特殊文字を除いた **オファー名** です。 未知の他のモジュールを参照するには、お客様がこの情報を更新する必要があることがわかるように <FROM_MODULE_NAME> 規約を使用します。 IoT Edge ルートの詳細については、「[ルートの宣言](../iot-edge/module-composition.md#declare-routes)」を参照してください。

たとえば、モジュール ContosoModule が ContosoInput の入力をリッスンし、ContosoOutput の出力データをリッスンする場合、次の 2 つの既定のルートを定義するのが適切です。

- 名前 #1:ToContosoModule
- 値 #1:FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- 名前 #2:FromContosoModuleToCloud
- 値 #2:FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**既定のモジュール ツインの必要なプロパティ**。 モジュール ツインは IoT Hub の JSON ドキュメントであり、必要なプロパティなど、モジュール インスタンスの状態情報が格納されます。 モジュールの構成や状態を同期するために、必要なプロパティを報告されるプロパティと共に使用します。 ソリューション バックエンドにより必要なプロパティを設定でき、モジュールによりそれらを読み取れます。 モジュールでは、必要なプロパティに対する変更を知らせる通知を受け取ることもできます。 必要なプロパティは最大 5 つの名前と値のペアで作成され、各既定値は 512 文字未満にする必要があります。 最大 5 つの名前と値のツインの目的のプロパティを定義できます。 ツインの目的のプロパティの値は、有効な JSON であり、エスケープせず、配列を含まず、最大ネスト階層は 4 レベルでなければなりません。 既定値に必要なパラメーターでは意味をなさないシナリオでは (たとえば、顧客のサーバーの IP アドレスなど)、既定値としてパラメーターを追加できます。 ツインの必要なプロパティに関する詳細については、「[必要なプロパティの定義または更新](../iot-edge/module-composition.md#define-or-update-desired-properties)」をご覧ください。

たとえば、モジュールがツインの目的のプロパティによって動的に構成可能なリフレッシュ レートをサポートする場合、以下の既定ツインの目的のプロパティを定義するのが適切です。

- 名前 #1:RefreshRate
- 値 #1:60

**既定の環境変数**。 環境変数は、構成プロセスを容易にする補足情報をモジュールに提供します。 環境変数は名前と値のペアを使用して作成されます。 既定の環境変数の名前と値はそれぞれ、512 文字未満にする必要があります。最大 5 つを定義できます。 既定値に必要なパラメーターでは意味をなさないとき (たとえば、顧客のサーバーの IP アドレスなど)、既定値としてパラメーターを追加できます。

たとえば、モジュールが開始される前に使用条件に同意する必要がある場合は、次の環境変数を定義できます。

- 名前 #1:ACCEPT_EULA
- 値 #1:Y

**既定のコンテナー作成オプション**。 コンテナー作成オプションでは、IoT Edge モジュールの Docker コンテナーの作成が指示されます。 IoT Edge では、Docker エンジン API のコンテナー作成オプションがサポートされています。 すべてのオプションは「[List containers](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList)」(コンテナーの一覧) をご覧ください。 作成オプション フィールドは有効な JSON、エスケープなし、512 文字未満にする必要があります。

たとえば、モジュールでポートをバインドする必要がある場合、次の作成オプションを定義します。

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="samples"></a>サンプル

Azure Marketplace でのプランの詳細の例を次に示します (表示されている価格は例示のみを目的としており、実際のコストを反映することを意図していません)。

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-plan.png" alt-text="Azure Marketplace でのプランの詳細を示しています。":::

#### <a name="call-out-descriptions"></a>コールアウトの説明

1. プラン名
2. プラン名
3. プラン説明

<br>Azure portal でのプランの詳細の例を次に示します (表示されている価格は例示のみを目的としており、実際のコストを反映することを意図していません)。

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-plan-details.png" alt-text="Azure portal でのプラン詳細を示しています。":::

#### <a name="call-out-descriptions"></a>コールアウトの説明

1. プラン名
2. プラン名
3. プラン説明

**[下書きの保存]** を選択し、左側のナビゲーション メニューで **[← プランの概要]** を選択して、プランの概要ページに戻ります。

## <a name="next-steps"></a>次の手順

- **Microsoft と共同販売する** (省略可能) には、左側のナビゲーション メニューでそれを選択します。 詳細については、[共同販売パートナーのエンゲージメント](./co-sell-overview.md)に関するページを参照してください。
- 共同販売を設定していないか、完了した場合は、[オファーを確認して発行](review-publish-offer.md)します。