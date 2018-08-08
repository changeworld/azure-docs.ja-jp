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
ms.openlocfilehash: a43febf1e78f80451b6aeed19e095b2c313d3216
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284855"
---
# <a name="how-to-administer-your-application"></a>アプリケーションを管理する方法

Microsoft Azure IoT Central アプリケーションを作成すると、Azure IoT Central ユーザー インターフェイスの **[Administration] (管理)** セクションを使ってアプリケーションを管理することができます。 **[Administration] (管理)** セクションに移動するには、左側のナビゲーション メニューで **[Administration] (管理)** を選択します。

**[Administration] (管理)** セクションでは、以下のことができます。

- ユーザーの管理

- ロールの管理

- 課金情報の表示

- アプリケーション設定の管理

- 無料試用版の延長

**[Administration] (管理)** セクションにはセカンダリ ナビゲーション メニューがあり、さまざまな管理タスクのリンクが含まれています。

**[Administration] (管理)** セクションにアクセスして使用するには、Azure IoT Central アプリケーションの**管理者**ロールが必要です。 Azure IoT Central アプリケーションを作成したユーザーは、自動的にそのアプリケーションの**管理者**ロールに割り当てられます。 この記事の「*ユーザーの管理*」セクションでは、**管理者**ロールを他のユーザーに割り当てる方法について説明します。

## <a name="change-application-name"></a>アプリケーション名の変更

アプリケーションの名前を変更するには、セカンダリ ナビゲーション メニューを使って、**[Administration] (管理)** セクションの **[Application Settings] (アプリケーション設定)** ページに移動します。

**[Application Settings] (アプリケーション設定)** ページで、選んだ名前を **[Application Name]**(アプリケーション名) フィールドに入力し、**[Save] (保存)** を選択します。

## <a name="change-the-application-url"></a>アプリケーション URL の変更

アプリケーションの URL を変更するには、セカンダリ ナビゲーション メニューを使って、**[Administration] (管理)** セクションの **[Application Settings] (アプリケーション設定)** ページに移動します。

![[Application Settings] (アプリケーション設定) ページ](media\howto-administer\image0-a.png)

**[Application Settings] (アプリケーション設定)** ページで、選んだ URL を **[URL] (URL)** フィールドに入力し、**[Save] (保存)** を選択します。 URL の長さは最大 200 文字まで可能です。 URL が使用できない場合は、検証エラーが表示されます

> [!Note]
> URL を変更した場合は、Azure IoT Central の別の顧客が古い URL を取得できます。 その場合、その URL を使用することはできなくなります。 URL を変更すると、古い URL は動作しなくなるので、使用する新しい URL をユーザーに通知する必要があります。

## <a name="change-the-application-image"></a>アプリケーション イメージの変更

Azure IoT Central アプリケーションでイメージを使用する方法の詳細については、「[Prepare and upload images to your Azure IoT Central application](howto-prepare-images.md)」(イメージを準備して Azure IoT Central アプリケーションにアップロードする) を参照してください。

## <a name="copy-an-application"></a>アプリケーションをコピーする

デバイス インスタンス、デバイス データ履歴、およびユーザー データ以外の任意のアプリケーションのコピーを作成できます。 コピーは、課金対象となる有料アプリケーションになります。 別のアプリケーションをコピーすることで試用版のアプリケーションを作成することはできません。

アプリケーションをコピーするは、**[アプリケーションの設定]** ページに移動し、**[コピー]** ボタンをクリックします。

![[アプリケーションの設定] ページ](media\howto-administer\appCopy1.png)

**[コピー]** ボタンをクリックすると、アプリケーションのコピーによって作成される新しいアプリケーションの名前、URL、AAD ディレクトリ、サブスクリプション、および Azure リージョンを選択できるダイアログが開きます。 これらの各フィールドの値を選択した後、**[コピー]** ボタンをクリックし、続行することを確認します。 これらの値の入力内容の詳細については、[アプリケーションの作成方法](howto-create-application.md)に関する記事を参照してください。

![[アプリケーションの設定] ページ](media\howto-administer\appCopy2.png)

アプリのコピー操作が成功したら、**[アプリケーションの設定]** ページに表示されるリンクをクリックすることで、アプリケーションのコピーによって作成された新しいアプリケーションに移動できます。

![[アプリケーションの設定] ページ](media\howto-administer\appCopy3.png)

> [!Note]
> アプリケーションをコピーすると、ルールやアクションの定義がコピーされます。 ただし、コピー元のアプリへのアクセス権を持つユーザーはコピーされたアプリにはコピーされないため、ユーザーが前提である電子メールなどのアクションに対してユーザーを手動で追加する必要があります。

## <a name="delete-an-application"></a>アプリケーションの削除

アプリケーションを削除するには、セカンダリ ナビゲーション メニューを使って、**[Administration] (管理)** セクションの **[Application Settings] (アプリケーション設定)** ページに移動します。

**[削除]** を選択します。

> [!Note]
> アプリケーションを削除すると、アプリケーションに関連付けられているすべてのデータが削除され、回復できなくなります。 また、アプリケーションを削除するには、アプリケーションを作成したときに選択した Azure サブスクリプションのリソースを削除する権限が必要です。 詳細については、「[ロールベースのアクセス制御を使用して Azure サブスクリプション リソースへのアクセスを管理する](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)」を参照してください。

## <a name="roles-in-azure-iot-central"></a>Azure IoT Central のロール

ロールを使用すると、組織内で Azure IoT Central のさまざまなタスクを実行するユーザーを制御することができます。 Azure IoT Central には、アプリケーションのユーザーに割り当てることができるロールが 3 つあります。 ロールは各アプリケーションによって割り当てられます。 同じユーザーが、異なるアプリケーションでさまざまなロールを持つことができます。 同じユーザーをアプリケーション内の複数のロールに割り当てることができます。

### <a name="administrator"></a>管理者

**管理者**ロール内のユーザーは、Azure IoT Central アプリケーション内のすべての機能にアクセスできます。

アプリケーションを作成したユーザーは、自動的に**管理者**ロールに割り当てられます。 **管理者**ロールには、少なくとも 1 人のユーザーが常に存在している必要があります。

### <a name="application-builder"></a>Application Builder (アプリケーション ビルダー)

**Application Builder (アプリケーション ビルダー)** ロール内のユーザーは、Azure IoT Central アプリケーションの管理を除き、アプリケーション内のすべてを実行することができます。

### <a name="application-operator"></a>Application Operator (アプリケーション オペレーター)

**Application Operator (アプリケーション オペレーター)** ロール内のユーザーは、**Application Builder (アプリケーション ビルダー)** ページにアクセスすることはできません。 アプリケーションを管理することもできません。

## <a name="manage-users"></a>ユーザーの管理

アプリケーション管理者は、アプリケーション内のロールにユーザーを割り当てることができます。

### <a name="add-users"></a>ユーザーの追加

ユーザーが Azure IoT Central アプリケーションにサインインしてアクセスするには、ユーザー アカウントが必要です。 Azure IoT Central では、Microsoft アカウント (MSA) と Azure Active Directory (AAD) アカウントがサポートされています。 Azure Active Directory グループは現在 Azure IoT Central でサポートされていません。

詳細については、「[Microsoft アカウントのヘルプ](https://support.microsoft.com/products/microsoft-account?category=manage-account)」と「[クイック スタート: Azure Active Directory に新しいユーザーを追加する](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)」を参照してください。

1. Azure IoT Central アプリケーションにユーザー アカウントを追加するには、セカンダリ ナビゲーション メニューを使って、**[Administration] (管理)** セクションの **[Users] (ユーザー)** ページに移動します。

    ![ユーザーの一覧](media\howto-administer\image1.png)

1. **[Users] (ユーザー)** ページで、**[+ Add user] (+ ユーザーの追加)** を選択してユーザーを追加します。

    ![ユーザーの追加](media\howto-administer\image2.png)

1. Azure IoT Central アプリケーションにユーザーを追加するときは、**[Role] (ロール)** ドロップダウンからユーザーのロールを選択します。 ロールに関する詳細については、この記事の「*Azure IoT Central のロール*」セクションを参照してください。

    ![ロールの選択](media\howto-administer\image3.png)

    > [!NOTE]
    >  ユーザーを一括で追加するには、追加するすべてのユーザーのユーザー ID をセミコロンで区切って入力します。 **[Role] (ロール)** ドロップダウンからロールを選択し、**[Save] (保存)** を選択します。

1. ユーザーを追加すると、そのユーザーのエントリが **[Users] (ユーザー)** ページに表示されます。

    ![ユーザー一覧](media\howto-administer\image4.png)

### <a name="edit-the-roles-assigned-to-users"></a>ユーザーに割り当てられているロールの編集

一度割り当てられたロールは変更できません。 ユーザーに割り当てられているロールを変更するには、ユーザーを削除し、別のロールを使用してユーザーを追加します。

### <a name="delete-users"></a>ユーザーを削除する

ユーザーを削除するには、**[Users] (ユーザー)** ページで 1 つまたは複数のチェック ボックスをオンにし、**[Delete] (削除)** を選択します。

## <a name="view-your-bill"></a>課金状況の表示

課金状況を表示するには、**[Administration] (管理)** セクションの **[Billing] (課金)** ページに移動し、**[Billing] (課金)** を選択します。 Azure の課金ページが新しいタブで開き、Azure IoT Central アプリケーションごとの課金状況を確認できます。

## <a name="convert-your-trial-to-a-paid-application"></a>試用版の有料アプリケーションへの変換

IoT Central を評価した後で、試用版を有料アプリケーションに変換することができます。 このセルフ サービスのプロセスを完了するには、次の手順に従います。

1. セカンダリ ナビゲーション メニューを使って、**[Administration] (管理)** セクションの **[Billing] (課金)** ページに移動します。 試用版を延長していない場合、ページは次のようになります。

    ![無料試用版の状態](media/howto-administer/freetrial.png)

2. **[Convert to Paid] (有料版に変換)** をクリックします。 試用版を延長していない場合、ポップアップが次のように表示されます。

    ポップアップで適切な Azure Active Directory テナントを選択し、IoT Central アプリケーションで使用する Azure サブスクリプションを選択します。

    ![無料試用版の延長](media/howto-administer/extend.png)

3. **[Convert] (変換)** をクリックすると、試用版が有料アプリケーションに変換され、課金が開始されます。

## <a name="extend-your-free-trial"></a>無料試用版の延長

既定では、すべての無料試用版は 7 日間利用できます。 試用版を 30 日間に延長する場合は、次の手順に従います。

1. セカンダリ ナビゲーション メニューを使って、**[Administration] (管理)** セクションの **[Billing] (課金)** ページに移動します。

1. **[Extend Trial] (試用版の延長)** をクリックします。 ポップアップで適切な Azure Active Directory テナントを選択し、IoT Central アプリケーションで使用する Azure サブスクリプションを選択します。

1. 次に、**[Extend] (延長)** をクリックします。 これで、試用版は 30 日間有効になりました。

## <a name="next-steps"></a>次の手順

ここでは、Azure IoT Central アプリケーションを管理する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [デバイス テンプレートを設定する](howto-set-up-template.md)