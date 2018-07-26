---
title: Microsoft Flow 内で IoT Central コネクタを使用してワークフローを作成する | Microsoft Docs
description: Microsoft Flow 内で IoT Central コネクタを使用して、ワークフローのトリガーや、ワークフロー内のデバイスの作成、更新、および削除を行います。
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/12/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: peterpr
ms.openlocfilehash: 2414fb0576448339b268dce92dafe6c70108ba5d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011640"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Microsoft Flow 内で IoT Central コネクタを使用してワークフローを作成する

Microsoft Flow を使用すると、ビジネス ユーザーが利用する多くのアプリケーションやサービスにまたがるワークフローを自動化できます。 Microsoft Flow 内で IoT Central コネクタを使用すると、IoT Central 内で規則が作動したときにワークフローをトリガーできます。 IoT Central やその他のアプリケーションによってトリガーされたワークフロー内では、IoT Central コネクタ内のアクションを使用して、デバイスの作成、デバイスのプロパティと設定の更新、デバイスの削除を行うことができます。 IoT Central をモバイル通知や Microsoft Teams などの他のサービスに接続する[これらの Microsoft Flow テンプレート](https://aka.ms/iotcentralflowtemplates)をチェックアウトしてください。

> [!NOTE] 
> Microsoft Flow にサインインするときは、Microsoft の個人、職場、または学校のアカウントを使用する必要があります。 Microsoft Flow のプランの詳細については、[こちら](https://aka.ms/microsoftflowplans)をご覧ください。

## <a name="trigger-a-workflow-when-a-rule-is-fired"></a>規則が作動したときに、ワークフローをトリガーする

このセクションでは、IoT Central 内で規則が作動したときに Flow モバイル アプリでモバイル通知をトリガーする方法について説明します。

1. 最初に、[IoT Central 内で規則を作成](howto-create-telemetry-rules.md)します。 規則の条件を保存したら、新しいアクションとして **[Microsoft Flow action]\(Microsoft Flow アクション\)** を選択します。 ブラウザー内に新しいタブまたはウィンドウが開き、Microsoft Flow が表示されます。

1. Microsoft Flow にサインインします。 このとき使用するアカウントは、IoT Central 内で使用するアカウントと同じである必要はありません。 概要ページが開き、カスタム アクションに接続している IoT Central コネクタが表示されます。

1. **[続行]** をクリックします。 ワークフローを作成するための Microsoft Flow デザイナーが表示されます。 ワークフローには、[アプリケーション] と [規則] が既に入力されている IoT Central トリガーが含まれています。

1. **[+ 新しいステップ]** と **[アクションの追加]** を選択します。 このとき、必要なすべてのアクションをワークフローに追加できます。 例として、モバイル通知を送信してみましょう。 **通知**を検索し、**[通知] - [Send me a mobile notification]\(モバイル通知を自分に送信する\)** を選択します。

1. このアクションの [テキスト] フィールドに通知の内容を入力します。 IoT Central 規則からの"*動的なコンテンツ*"を含め、デバイス名やタイムスタンプなどの重要な情報を通知に渡すことができます。

    > [!NOTE]
    > [動的なコンテンツ] ウィンドウ内の [See more]\(もっと見る\) のテキストをクリックし、規則をトリガーした測定値とプロパティ値を取得します。

    ![動的なウィンドウを開いて Flow でアクションを編集します](./media/howto-add-microsoft-flow/flowdynamicpane.PNG)

1. アクションの編集が完了したら、**[保存]** をクリックします。 ワークフローの概要ページが表示されます。 ここで、実行履歴を表示して、他の同僚と共有できます。

    > [!NOTE]
    > IoT Central アプリでこの規則を他のユーザーに編集してもらいたい場合は、Microsoft Flow 内でこの規則を他のユーザーと共有します。 ワークフロー内で他のユーザーの Microsoft Flow アカウントを所有者として追加します。

1. IoT Central アプリに戻ると、この規則の [Actions]\(アクション\) 区分の下に Microsoft Flow アクションが表示されます。

ワークフローの作成は、Microsoft Flow 内の IoT Central コネクタを使用して、いつでも始めることができます。 ワークフローの作成を開始したら、接続先の IoT Central アプリと規則を選択します。

## <a name="create-a-device-in-a-workflow"></a>ワークフロー内のデバイスを作成する

このセクションでは、Microsoft Flow モバイル アプリを使い、モバイル デバイスのボタンを押して IoT Central 内に新しいデバイスを作成する方法について説明します。 Flow 内でこのアクションを使用して、Dynamics などの ERP システムと IoT Central を統合できます。これを行うには、デバイスが他の場所で追加されたときに、新しいデバイスを作成する必要があります。

1. 最初に Microsoft Flow 内で空のワークフローを作成します。

1. トリガーとして **[Flow button for mobile]\(モバイル用の Flow ボタン\)** を検索します。

1. このトリガー内で、**[デバイス名]** という名前のテキスト入力を追加します。 説明のテキストを **[Enter the device name of your new device]\(新規デバイスのデバイス名を入力してください\)** に変更します。

1. 新しいアクションを追加します。 **[Azure IoT Central - Create a device]\(Azure IoT Central - デバイスの作成\)** アクションを検索します。

1. アプリケーションを選択し、デバイス作成元のデバイス テンプレートをドロップダウンから選択します。 [expand to show all the properties and settings of the device]\(デバイスのすべてのプロパティと設定を展開表示する\) というアクションが表示されます。

1. [デバイス名] フィールドを選択します。 [動的なコンテンツ] ウィンドウで、**[デバイス名]** を選択します。 この値は、ユーザーがモバイル アプリで入力した入力値から渡され、IoT Central 内の新しいデバイスの名前になります。 この例では、必須フィールドは [デバイス名] のみで、赤のアスタリスクで示されています。 別のデバイス テンプレートでは、複数の必須フィールドを持つものもあり、これらのフィールドをすべて入力しないと、新しいデバイスを作成できません。

    ![Flow の [create device action]\(デバイス アクションの作成\) 動的ウィンドウ](./media/howto-add-microsoft-flow/flowcreatedevice.PNG)
1. (省略可能) 新しいデバイスの作成に適していると思われるその他のフィールドに入力します。 たとえば、デバイスをシミュレートするかどうかを選択します。

1. 最後に、ワークフローを保存します。

1. Microsoft Flow モバイル アプリでワークフローを試してみます。 アプリの **[ボタン]** タブに移動します。 [Button -> Create a new device]\(ボタン -> デバイスの新規作成\) ワークフローが表示されます。 新しいデバイスの名前を入力し、その名前が IoT Central に表示されることを確認します。

    ![Flow の [デバイスの作成] モバイル スクリーンショット](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>ワークフロー内のデバイスを更新する

このセクションでは、Microsoft Flow モバイル アプリを使い、モバイル デバイスのボタンを押して IoT Central 内のデバイスの設定とプロパティを更新する方法について説明します。

1. 最初に Microsoft Flow 内で空のワークフローを作成します。

1. トリガーとして **[Flow button for mobile]\(モバイル用の Flow ボタン\)** を検索します。

1. このトリガー内で、変更する設定またはプロパティに対応するテキスト入力「**場所**」のような入力を追加します。 説明のテキストを変更します。

1. 新しいアクションを追加します。 **[Azure IoT Central - Update a device]\(Azure IoT Central - デバイスの更新\)** アクションを検索します。

1. ドロップダウンからアプリケーションを選択します。 ここで、更新する既存のデバイスのデバイス ID が必要になります。 デバイス ID は、IoT Central から **Device Explorer** で取得できます。

    ![IoT Central の Device Explorer のデバイス ID](./media/howto-add-microsoft-flow/iotcdeviceid.png)

1. ここで、デバイス名を更新し、それがシミュレートされたデバイスであるかどうかを更新できます。 デバイスのプロパティと設定を更新するには、更新するデバイスのデバイス テンプレートを **[Device Template]\(デバイス テンプレート\)** ドロップダウンで選択する必要があります。 アクション タイルが展開し、更新可能なすべてのプロパティと設定が表示されます。

1. 更新するプロパティと設定をそれぞれ選択します。 [動的なコンテンツ] ウィンドウで、対応する入力をトリガーから選択します。 この例では、[場所] の値が伝達され、デバイスの [場所] プロパティが更新されます。

    ![Flow の [update device action]\(デバイス アクションの更新\) 動的ウィンドウ](./media/howto-add-microsoft-flow/flowupdatedevice.PNG)

1. 最後に、ワークフローを保存します。

1. Microsoft Flow モバイル アプリでワークフローを試してみます。 アプリの **[ボタン]** タブに移動します。 [Button -> Update a device]\(ボタン -> デバイスの更新\) ワークフローが表示されます。 入力値を入力し、IoT Central でデバイスが更新されることを確認します。

## <a name="delete-a-device-in-a-workflow"></a>ワークフロー内のデバイスを削除する

**[Azure IoT Central - Delete a device]\(Azure IoT Central - デバイスの削除\)** アクションを使用して、デバイス ID でデバイスを削除できます。 Microsoft Flow モバイル アプリ内のボタンを押してデバイスを削除するワークフローの例を次に示します。

   ![Flow の[デバイスの削除] ワークフロー](./media/howto-add-microsoft-flow/flowdeletedevice.PNG)
    
## <a name="troubleshooting"></a>トラブルシューティング

Azure IoT Central コネクタへの接続を作成できない場合に役立つヒントを次に示します。

1. Microsoft 個人アカウント (@hotmail.com、@live.com、@outlook.com ドメインなど) は、現時点ではサポートされていません。 AAD 職場または学校アカウントを使用する必要があります。

2. AAD アカウントを使用しているときにエラーが発生した場合は、Windows PowerShell を開き、次のコマンドを管理者として実行してみてください。
    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```
    
## <a name="next-steps"></a>次の手順
ここでは、Microsoft Flow を使用して、ワークフローを作成する方法を学びました。推奨される次のステップは、[デバイスの管理](howto-manage-devices.md)です。
