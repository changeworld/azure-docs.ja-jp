---
title: Azure IoT Central アプリケーションを管理する | Microsoft Docs
description: 管理者として、Azure IoT Central アプリケーションを管理する方法
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 25b4777be4257933b84d58d0f10cf12571de9590
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2018
ms.locfileid: "50155322"
---
# <a name="administer-your-iot-central-application"></a>IOT Central アプリケーションを管理する

Microsoft Azure IoT Central アプリケーションを作成すると、Azure IoT Central ユーザー インターフェイスの **[Administration] (管理)** セクションを使ってアプリケーションを管理することができます。 **[Administration] (管理)** セクションに移動するには、左側のナビゲーション メニューで **[Administration] (管理)** を選択します。

**[Administration] (管理)** セクションでは、以下のことができます。

- ユーザーの管理

- ロールの管理

- 課金情報の表示

- アプリケーション設定の管理

- 無料試用版の提供

**[Administration] (管理)** セクションにはセカンダリ ナビゲーション メニューがあり、さまざまな管理タスクのリンクが含まれています。

**[Administration] (管理)** セクションにアクセスして使用するには、Azure IoT Central アプリケーションの**管理者**ロールが必要です。 Azure IoT Central アプリケーションを作成したユーザーは、自動的にそのアプリケーションの**管理者**ロールに割り当てられます。 この記事の「[ユーザーの管理](#manage-users)」セクションでは、**管理者**ロールを他のユーザーに割り当てる方法について説明します。

## <a name="change-application-name"></a>アプリケーション名の変更

アプリケーションの名前を変更するには、セカンダリ ナビゲーション メニューを使って、**[Administration] (管理)** セクションの **[Application Settings] (アプリケーション設定)** ページに移動します。

**[Application Settings] (アプリケーション設定)** ページの **[Application Name]**(アプリケーション名) フィールドにお好みの名前を入力します｡ 次に、**[保存]** を選択します。

## <a name="change-the-application-url"></a>アプリケーション URL の変更

アプリケーションの URL を変更するには、セカンダリ ナビゲーション メニューを使って、**[Administration] (管理)** セクションの **[Application Settings] (アプリケーション設定)** ページに移動します。

![[Application Settings] (アプリケーション設定) ページ](media\howto-administer\image0-a.png)

**[Application Settings] (アプリケーション設定)** ページの **[URL] (URL)** フィールドにお好みの URL を入力し、**[Save] (保存)** を選択します。 URL の長さは最大 200 文字まで可能です。 URL が無効な場合は、検証エラーが表示されます。

> [!Note]
> URL を変更した場合は、Azure IoT Central の別の顧客が古い URL を取得できます。 その場合、その URL は使用できなくなります。 URL を変更すると、古い URL は機能しなくなるため、使用する新しい URL をユーザーに通知する必要があります。

## <a name="change-the-application-image"></a>アプリケーション イメージの変更

Azure IoT Central アプリケーションでイメージを使用する方法の詳細については、「[Prepare and upload images to your Azure IoT Central application](howto-prepare-images.md)」(イメージを準備して Azure IoT Central アプリケーションにアップロードする) を参照してください。

## <a name="copy-an-application"></a>アプリケーションをコピーする

デバイス インスタンス、デバイス データ履歴、およびユーザー データ以外の任意のアプリケーションのコピーを作成できます。 コピーは、課金対象となる有料アプリケーションになります。 別のアプリケーションをコピーして試用版のアプリケーションを作成することはできません。

アプリケーションをコピーするには、**[Application Settings] (アプリケーション設定)** ページに移動します。 **[コピー]** ボタンを選択します。

![[Application Settings] (アプリケーション設定) ページ](media\howto-administer\appCopy1.png)

**[コピー]** ボタンをクリックすると、ダイアログ ボックスが開きます｡このダイアログ ボックスで､アプリケーションをコピーによって作成する新しいアプリケーションの名前と URL、AAD ディレクトリ、サブスクリプション、および Azure リージョンを選択できます｡ 各フィールドの値を選択します。 **[コピー]** ボタンを選択して､続行することを確認します｡ 上記の入力する値についての詳細は、[アプリケーションの作成方法](howto-create-application.md)に関する記事を参照してください。

![[Application Settings] (アプリケーション設定) ページ](media\howto-administer\appCopy2.png)

アプリのコピー操作が成功すると、アプリケーションをコピーすることによって作成された新しいアプリケーションに移動できます。 アプリに移動するには､**アプリケーション設定**ページに表示されているリンクを選択します。

![[Application Settings] (アプリケーション設定) ページ](media\howto-administer\appCopy3.png)

> [!Note]
> アプリケーションをコピーすると、ルールやアクションの定義もコピーされます。 ただし、コピー元のアプリへのアクセス権を持つユーザーがコピー先のアプリにコピーされることはないため、ユーザーが前提を前提とする電子メールなどのアクションには手動でユーザーを追加する必要があります。

## <a name="delete-an-application"></a>アプリケーションの削除

アプリケーションを削除するには、セカンダリ ナビゲーション メニューを使って、**[Administration] (管理)** セクションの **[Application Settings] (アプリケーション設定)** ページに移動します。

**[削除]** を選択します。

> [!Note]
> アプリケーションを完全に削除すると、そのアプリケーションに関連付けられているすべてのデータが削除されます。  また、アプリケーションを削除するには、アプリケーションを作成したときに選択した Azure サブスクリプションのリソースを削除する許可も必要です。 詳細は、「[ロールベースのアクセス制御を使用して Azure サブスクリプション リソースへのアクセスを管理する](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)」を参照してください。

## <a name="roles-in-azure-iot-central"></a>Azure IoT Central のロール

ロールを使用すると、組織内で Azure IoT Central のさまざまなタスクを実行できるユーザーを制御することができます。 Azure IoT Central には、アプリケーションのユーザーに割り当てることができるロールが 3 つあります。 ロールは各アプリケーションによって割り当てられます。 同じユーザーが、異なるアプリケーションでさまざまなロールを持つことができます。 1 つのアプリケーション内で､同じユーザーを複数のロールに割り当てることができます。

### <a name="administrator"></a>管理者

**管理者**ロール内のユーザーは、Azure IoT Central アプリケーション内のすべての機能にアクセスできます。

アプリケーションを作成したユーザーは、自動的に**管理者**ロールに割り当てられます。 **管理者**ロールには、少なくとも 1 人のユーザーが常に存在している必要があります。

### <a name="application-builder"></a>Application Builder (アプリケーション ビルダー)

**Application Builder (アプリケーション ビルダー)** ロール内のユーザーは、Azure IoT Central アプリケーションの管理を除き、アプリケーション内のすべてを実行することができます。

### <a name="application-operator"></a>Application Operator (アプリケーション オペレーター)

**Application Operator (アプリケーション オペレーター)** ロール内のユーザーは、**Application Builder (アプリケーション ビルダー)** ページにアクセスすることはできません。 アプリケーションを管理することもできません。

## <a name="manage-users"></a>ユーザーの管理

アプリケーション管理者は、アプリケーション内のロールにユーザーを割り当てることができます。

### <a name="add-users"></a>Add users

ユーザーが Azure IoT Central アプリケーションにサインインしてアクセスするには、ユーザー アカウントが必要です。 Azure IoT Central では、Microsoft アカウント (MSA) と Azure Active Directory (AAD) アカウントがサポートされています。 Azure Active Directory グループは 現在 Azure IoT Central でサポートされていません。

詳細は、「[Microsoft アカウントのヘルプ](https://support.microsoft.com/products/microsoft-account?category=manage-account)」と「[クイック スタート: Azure Active Directory に新しいユーザーを追加する](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)」を参照してください。

1. Azure IoT Central アプリケーションにユーザー アカウントを追加するには、セカンダリ ナビゲーション メニューから **[Administration] (管理)** セクションの **[Users] (ユーザー)** ページに移動します。

    ![ユーザーの一覧](media\howto-administer\image1.png)

1. **[Users] (ユーザー)** ページで、**[+ Add user] (+ ユーザーの追加)** を選択してユーザーを追加します。

    ![ユーザーの追加](media\howto-administer\image2.png)

1. **ロール**ドロップダウン メニューからユーザーに対するロールを選択します。 ロールに関する詳細については、この記事の「*Azure IoT Central のロール*」セクションを参照してください。

    ![ロールの選択](media\howto-administer\image3.png)

    > [!NOTE]
    >  ユーザーを一括で追加するには、追加するすべてのユーザーのユーザー ID をセミコロンで区切って入力します。 **[Role] (ロール)** ドロップダウンからロールを選択します｡ 次に、**[保存]** を選択します。

1. ユーザーを追加すると、そのユーザーのエントリが **[Users] (ユーザー)** ページに表示されます。

    ![ユーザー一覧](media\howto-administer\image4.png)

### <a name="edit-the-roles-that-are-assigned-to-users"></a>ユーザーに割り当てられているロールを編集します。

割り当て後、ロールを変更することはできません。 ユーザーに割り当てられているロールを変更するには、ユーザーを削除してから、別のロールを使用してユーザーを追加します。

### <a name="delete-users"></a>ユーザーを削除する

ユーザーを削除するには、**ユーザー**ページで 1 つまたは複数のチェック ボックスを選択します｡ 次に、**[削除]** を選択します。

## <a name="view-your-bill"></a>課金状況の表示

請求書を表示するには、**課金**ページの**管理**セクションに移動します。 次に､**課金**を選択します。 新しいタブに Azure 課金ページが開き、Azure IoT Central アプリケーションごとに請求書を確認することができます。

## <a name="convert-your-trial-to-a-paid-application"></a>試用版の有料アプリケーションへの変換

IoT Central を評価した後、試用版を有料アプリケーションに変換することができます。 このセルフ サービスのプロセスを完了するには、次の手順に従います。

1. セカンダリ ナビゲーション メニューを使って、**[Administration] (管理)** セクションの **[Billing] (課金)** ページに移動します。 試用版を延長していない場合、ページは次のようになります。

    ![無料試用版の状態](media/howto-administer/freetrial.png)

2. **[Convert to Paid] (有料版に変換)** をクリックします。 試用版を延長していない場合、次のようなポップアップが表示されます。

    ![無料試用版の延長](media/howto-administer/extend.png)

3. ポップアップで適切な Azure Active Directory テナントを選択し、IoT Central アプリケーションで使用する Azure サブスクリプションを選択します。

3. **[Convert] (変換)** をクリックすると、試用版が有料アプリケーションに変換され、課金が開始されます。

## <a name="extend-your-free-trial"></a>無料試用版の延長

既定では、無料試用版はどれも 7 日間利用できます。 試用版を 30 日間に延長する場合は、次の手順に従います。

1. セカンダリ ナビゲーション メニューを使って、**[Administration] (管理)** セクションの **[Billing] (課金)** ページに移動します。

1. **[Extend Trial] (試用版の延長)** を選択します。 ポップアップで適切な Azure Active Directory テナントを選択し、IoT Central アプリケーションで使用する Azure サブスクリプションを選択します。

1. **[Extend] (拡張)** を選択します。 これで、試用版は 30 日間有効になりました。

## <a name="use-the-azure-sdks-for-control-plane-operations"></a>Azure SDK を使用して制御プレーンを操作する

Node、Python、C#、フリガナ、Java、Go には､IoT Central の Azure Resource Manager SDK パッケージが用意されています｡ これらライブラリは､IoT Central に対する制御プレーン操作をサポートしており､ IoT Central アプリケーションの作成や一覧表示、更新、 削除を行うことができます｡ これらのライブラリには､各言語に固有の認証処理とエラー処理用のヘルパーも用意されています｡ 

Azure Resource Manager SDK を使用する方法例については､[ https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples) をご覧ください｡

詳細は、GitHub で以下のパッケージを参照してください。

| Language | リポジトリ | Package |
| ---------| ---------- | ------- |
| ノード | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>次の手順

ここでは、Azure IoT Central アプリケーションを管理する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [デバイス テンプレートを設定する](howto-set-up-template.md)
