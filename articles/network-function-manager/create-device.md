---
title: 'チュートリアル: Azure Network Function Manager のデバイス リソースを作成する'
description: このチュートリアルでは、Azure Network Function Manager のデバイス リソースを作成する方法について説明します。
author: cherylmc
ms.service: network-function-manager
ms.topic: tutorial
ms.date: 06/30/2021
ms.author: cherylmc
ms.openlocfilehash: 048b086c407f5fbdc6239aec9dc509aceda7c8c2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468193"
---
# <a name="tutorial-create-a-network-function-manager-device-resource-preview"></a>チュートリアル: Network Function Manager のデバイス リソースを作成する (プレビュー)

このチュートリアルでは、Azure Network Function Manager (NFM) の **デバイス** リソースを作成する方法について説明します。 Network Function Manager のデバイス リソースは、Azure Stack Edge リソースにリンクされています。 デバイス リソースにより、Azure Stack Edge デバイスに展開されたすべてのネットワーク関数が集計され、Azure Stack Edge に展開されたすべてのネットワーク関数の展開、監視、トラブルシューティング、および一貫した管理操作のための共通サービスが提供されます。 Azure Stack Edge デバイスにネットワーク関数を展開する前に、Network Function Manager のデバイス リソースを作成する必要があります。

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * 前提条件を確認する
> * デバイス リソースを作成する
> * 登録キーを取得する
> * デバイスの登録

## <a name="prerequisites"></a><a name="pre"></a>前提条件

次の前提条件を確認します。

* [概要](overview.md#prereq)の記事に記載されている前提条件をすべて満たしています。
* 適切なアクセス許可が割り当てられています。 詳細については、「[リソース プロバイダーの登録とアクセス許可](overview.md#permissions)」を参照してください。
* デバイス リソースを作成する前に「 [リージョンの可用性](overview.md#regions) 」セクションを確認します。
* Windows クライアントから PowerShell を介して Azure Stack Edge Pro GPU デバイスにリモートで接続できることを確認します。 詳細については、「[PowerShell インターフェイスに接続する](../databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)」を参照してください。

## <a name="create-a-device-resource"></a><a name="create"></a>デバイス リソースを作成する

既存の Azure Network Function Manager のデバイス リソースがある場合は、作成する必要はありません。 このセクションをスキップし、[登録キー](#key)のセクションに進んでください。

**デバイス** リソースを作成するには、次の手順を実行します。

1. Microsoft Azure の資格情報を使用して、Azure [プレビュー ポータル](https://aka.ms/AzureNetworkFunctionManager)にサインインします。

1. **[基本]** タブで、 **[プロジェクトの詳細]** と **[インスタンスの詳細]** にデバイスの設定を構成します。
   :::image type="content" source="./media/create-device/device-settings.png" alt-text="デバイス設定のスクリーンショット。":::

   各フィールドに入力するときに、追加した文字が有効になると緑色のチェックマークが表示されます。 自動で入力されるフィールドもあれば、カスタマイズできるフィールドもあります。

   * **サブスクリプション:** 一覧表示されているサブスクリプションが正しいことを確認します。 ドロップダウンを使用して、サブスクリプションを変更できます。
   * **リソース グループ:** 既存のリソース グループを選択するか、 **[新規作成]** をクリックして新しく作成します。
   * **場所**: デバイスの場所を選択します。 これはサポート対象のリージョンにする必要があります。 詳細については、「[利用可能なリージョン](overview.md#regions)」を参照してください。
   * **名前**: デバイスの名前を入力します。
   * **Azure Stack Edge:** Azure Network Function Manager のデバイスとして登録する Azure Stack Edge リソースを選択します。 デバイス リソースを正常に作成するには、ASE リソースが **オンライン** 状態である必要があります。 ASE リソースの状態を確認するには、Azure Stack Edge リソース ページの **[プロパティ]** セクションに移動します。
1. **[確認と作成]** を選択し、デバイス設定を検証します。
1. すべての設定が検証されたら、 **[作成]** を選択します。
   
   >[!NOTE]
   >Network Function Manager のデバイス リソースは、1 つの Azure Stack Edge リソースにのみリンクできます。 Azure Stack Edge リソースごとに個別の NFM デバイス リソースを作成する必要があります。
   >

## <a name="get-the-registration-key"></a><a name="key"></a>登録キーを取得する

1. デバイスのプロビジョニングに成功したら、デバイス リソースが展開されているリソース グループに移動します。
1. **デバイス** リソースをクリックします。 登録キーを取得するには、 **[Get registration key]\(登録キーの取得\)** をクリックします。 登録キーを生成するための適切なアクセス許可を持っていることを確認してください。 詳細については、[アクセス許可](overview.md#permissions)に関する記事を参照してください。

   :::image type="content" source="./media/create-device/register-device.png" alt-text="登録キーのスクリーンショット。" lightbox="./media/create-device/register-device.png":::
1. 次の手順で使用するデバイス登録キーをメモしておきます。

   > [!IMPORTANT]
   > 登録キーの有効期限は、生成されてから 24 時間です。 登録キーが無効になった場合は、このセクションの手順を繰り返して新しい登録キーを生成することができます。
   >

## <a name="register-the-device"></a><a name="registration"></a>デバイスを登録する

次の手順に従って、デバイス リソースを Azure Stack Edge に登録します。

1. 前の手順で取得した登録キーを使用してデバイスを登録するには、[Windows PowerShell](../databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) を介して Azure Stack Edge デバイスに接続します。

1. アプライアンスへの PowerShell (minishell) の接続が確立したら、前の手順で取得したデバイス登録キーをパラメーターとして使用して、次のコマンドを入力します。
   ```powershell
   Invoke-MecRegister <device-registration-key>
   ```

1. デバイス リソースの **[デバイスの状態] が "登録済み"** であることを確認します。

   :::image type="content" source="./media/create-device/device-registered.png" alt-text="登録済みのデバイスのスクリーンショット。" lightbox="./media/create-device/device-registered.png":::
 
## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ネットワーク機能をデプロイする](deploy-functions.md)
