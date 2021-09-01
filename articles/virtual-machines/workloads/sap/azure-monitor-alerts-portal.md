---
title: Azure portal を使用して Azure Monitor for SAP Solutions でアラートを構成する
description: Azure Monitor for SAP Solutions でアラートを構成するためにブラウザーの方法を使用する方法について説明します。
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/16/2021
ms.openlocfilehash: 3d75f08ea921e5a29709c697e7677f97891d9b3e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183022"
---
# <a name="configure-alerts-in-azure-monitor-for-sap-solutions-by-using-the-azure-portal"></a>Azure portal を使用して Azure Monitor for SAP Solutions でアラートを構成する

この記事では、[Azure portal](https://azure.microsoft.com/features/azure-portal) において Azure Monitor for SAP Solutions (AMS) でアラートを構成する手順について説明します。 ポータルのブラウザーベースのインターフェイスを使用して、アラートと通知を構成します。

前提条件: 少なくとも 1 つのプロバイダーを含む AMS リソースをデプロイする必要があります。 SAP アプリケーション (NetWeaver)、SAP HANA、Microsoft SQL サーバー、または高可用性 (pacemaker) クラスターのプロバイダーを構成できます。 

## <a name="sign-in-to-the-portal"></a>ポータルにサインインする

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-an-alert-rule"></a>アラート ルールを作成する

1.  Azure portal で、Azure Monitor for SAP Solutions (AMS) リソースを参照して選択します。 この AMS リソースに対して少なくとも 1 つのプロバイダーが構成されていることを確認してください。 
2.  選択したブック (たとえば SAP HANA) に移動し、HANA インスタンスを選択します。

    :::image type="content" source="./media/ams-alerts/ams-alert-1.png" alt-text="[アラート] ボタンの配置を示すスクリーンショット。" lightbox="./media/ams-alerts/ams-alert-1.png":::
  
3.  **[アラート]** ボタンをクリックして、使用可能な **アラート テンプレート** を表示します。

    :::image type="content" source="./media/ams-alerts/ams-alert-2.png" alt-text="アラート テンプレートの一覧を示すスクリーンショット。" lightbox="./media/ams-alerts/ams-alert-2.png":::
    
4.  **[ルールの作成]** をクリックして、任意のアラートを構成します。
5.  **[アラートのしきい値]** に入力し、 **[プロバイダー インスタンス]** を選択し、 **[アクション グループ]** を選択または作成して、通知設定を構成します。 要件に従って、頻度と重要度の情報を編集できます。

    >[!Tip]
    > [アクション グループ](../../../azure-monitor/alerts/action-groups.md)について学習します。 
    
7.  **[Enable alert rule]\(アラート ルールの有効化\)** をクリックします。

    :::image type="content" source="./media/ams-alerts/ams-alert-3.png" alt-text="アラートの構成ページを示すスクリーンショット。" lightbox="./media/ams-alerts/ams-alert-3.png":::
    
7.  **[Deploy alert rule]\(アラート ルールのデプロイ\)** をクリックして、アラート ルールの構成を完了します。 **[テンプレートの表示]** をクリックすると、アラート テンプレートの表示を選択できます。

    :::image type="content" source="./media/ams-alerts/ams-alert-4.png" alt-text="アラート構成の最後の手順を示すスクリーンショット。" lightbox="./media/ams-alerts/ams-alert-4.png":::
    
8.  **[アラート ルール]** に移動して、新たに作成されたアラート ルールを表示します。 アラートが発生した場合には、 **[発生したアラート]** の下で確認できます。

    :::image type="content" source="./media/ams-alerts/ams-alert-5.png" alt-text="アラート構成の結果を示すスクリーンショット。" lightbox="./media/ams-alerts/ams-alert-5.png":::

## <a name="next-steps"></a>次のステップ

Azure Monitor for SAP Solutions の詳細について学習します。

> [!div class="nextstepaction"]
> [Azure Monitor for SAP Solutions](azure-monitor-overview.md)