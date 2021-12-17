---
title: Azure portal を使用して Azure Monitor for SAP Solutions でアラートを構成する
description: Azure Monitor for SAP Solutions でアラートを構成するためにブラウザーの方法を使用する方法について説明します。
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 08/30/2021
ms.openlocfilehash: 642eb96d07a351537f1320f823e698968e38d23e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309569"
---
# <a name="configure-alerts-in-azure-monitor-for-sap-solutions-by-using-the-azure-portal"></a>Azure portal を使用して Azure Monitor for SAP Solutions でアラートを構成する

この記事では、Azure Monitor for SAP Solutions でアラートを構成する手順について説明します。 [Azure portal](https://azure.microsoft.com/features/azure-portal) のブラウザーベースのインターフェイスを使用して、アラートと通知を構成します。

## <a name="prerequisites"></a>前提条件

少なくとも 1 つのプロバイダーを使用して Azure Monitor for SAP Solutions リソースをデプロイします。 以下のプロバイダーの構成ができます。 
- SAP Application (NetWeaver)
- SAP HANA
- Microsoft SQL Server
- 高可用性 (pacemaker) クラスター

## <a name="sign-in-to-the-portal"></a>ポータルにサインインする

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-an-alert-rule"></a>アラート ルールを作成する

1.  Azure portal で、Azure Monitor for SAP Solutions リソースを参照して選択します。 このリソースに対して少なくとも 1 つのプロバイダーが構成されていることを確認してください。 
2.  選択したブックに移動します。 たとえば、SAP HANA に移動し、HANA インスタンスを選択します。

    :::image type="content" source="./media/ams-alerts/ams-alert-1.png" alt-text="[アラート] ボタンの配置を示すスクリーンショット。" lightbox="./media/ams-alerts/ams-alert-1.png":::
  
3.  **[アラート]** ボタンを選択して、使用可能な **アラート テンプレート** を表示します。

    :::image type="content" source="./media/ams-alerts/ams-alert-2.png" alt-text="アラート テンプレートの一覧を示すスクリーンショット。" lightbox="./media/ams-alerts/ams-alert-2.png":::
    
4.  **[ルールの作成]** を選択して、任意のアラートを構成します。
5.  **[アラートのしきい値]** に入力し、 **[プロバイダー インスタンス]** を選択し、 **[アクション グループ]** を選択または作成して、通知設定を構成します。 要件に従って、頻度と重要度の情報を編集できます。

    >[!Tip]
    > [アクション グループ](../../../azure-monitor/alerts/action-groups.md)について学習します。 
    
7.  **[アラート ルールの有効化]** を選択します。

    :::image type="content" source="./media/ams-alerts/ams-alert-3.png" alt-text="アラートの構成ページを示すスクリーンショット。" lightbox="./media/ams-alerts/ams-alert-3.png":::
    
7.  **[アラート ルールのデプロイ]** を選択して、アラート ルールの構成を完了します。 **[テンプレートの表示]** をクリックすると、アラート テンプレートの表示を選択できます。

    :::image type="content" source="./media/ams-alerts/ams-alert-4.png" alt-text="アラート構成の最後の手順を示すスクリーンショット。" lightbox="./media/ams-alerts/ams-alert-4.png":::
    
8.  **[アラート ルール]** に移動して、新たに作成されたアラート ルールを表示します。 アラートが発生した場合には、 **[発生したアラート]** の下で確認できます。

    :::image type="content" source="./media/ams-alerts/ams-alert-5.png" alt-text="アラート構成の結果を示すスクリーンショット。" lightbox="./media/ams-alerts/ams-alert-5.png":::

## <a name="next-steps"></a>次のステップ

Azure Monitor for SAP Solutions の詳細について学習します。

> [!div class="nextstepaction"]
> [SAP on Azure の監視](monitor-sap-on-azure.md)
