---
title: Azure portal を使用した ARO クラスターのデプロイ
description: Azure portal を使用した ARO クラスターのデプロイ
author: rahulmehta
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: quickstart
ms.date: 10/21/2021
ms.openlocfilehash: 15fe2cc5c9735c28de867510cd5497a892554db8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130271234"
---
# <a name="quickstart-deploy-an-azure-red-hat-openshift-aro-cluster-using-the-azure-portal"></a>クイックスタート: Azure portal を使用した Azure Red Hat OpenShift (ARO) クラスターのデプロイ

Azure Red Hat OpenShift (ARO) は、クラスターをすばやくデプロイおよび管理できるマネージド OpenShift サービスです。 このクイックスタートでは、Azure portal を使用して ARO クラスターをデプロイします。

## <a name="prerequisites"></a>前提条件
[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-an-aro-cluster"></a>ARO クラスターの作成
1.  Azure portal メニュー上または **[ホーム]** ページから **[リソースの作成]** を選択します。
2.  **[Containers]**  >  **[Azure Red Hat OpenShift]** の順に選択します。
3.  **[基本]** ページで、次のオプションを構成します。
    * **プロジェクトの詳細**:
        *   **[Azure サブスクリプション]** を選択します。
        *   **[Azure リソース グループ]** (*myResourceGroup* など) を選択または作成します。
    * **クラスターの詳細**:
        * ARO クラスターの **[リージョン]** を選択します。
        *   OpenShift の **クラスター名**(*myAROCluster* など) を入力します。
        *   **ドメイン名** を入力します。
        *   **[マスター VM サイズ]** と **[ワーカー VM サイズ]** を選択します。

![Azure portal の **[Basic]** タブ](./media/portal-quickstart/basics-tab.jpg)

4.  **[認証]** ページで、次のオプションを構成します。
    1) [サービス プリンシパルの種類] セレクターから **[既存を選択]** を選択します (既に事前に選択されている場合は、アクションは必要ありません。次の手順に進みます)。
    2) Azure Red Hat Open Shift RP を検索して、選択します。 

>[!NOTE]
>[新規作成] ボタンと他の省略可能なフィールドは無視してください。

![Azure portal の **[認証]** タブ](./media/portal-quickstart/authentication.jpg)

5.  **[ネットワーク]** タブで、確実に次を構成します。
    * 仮想ネットワーク
    * マスター サブネット
    * ワーカー サブネット
    * API サーバーの可視性
    * イングレスの可視性

![Azure portal の **[ネットワーク]** タブ](./media/portal-quickstart/networking.jpg)

6.  **[タグ]** セクションで、リソースを整理するためにタグを追加します。

![Azure portal の **[タグ]** タブ](./media/portal-quickstart/tags.jpg)
 
7.  検証が完了したら、 **[確認および作成]** 、 **[作成]** の順にクリックします。

![Azure portal の **[確認と作成]** タブ](./media/portal-quickstart/review.jpg)
 
8.  ARO クラスターの作成には約 35 から 45 分かかります。 デプロイが完了したら、次のいずれかの方法でリソースに移動します。
    *   **[リソースに移動]** をクリックします。または
    *   ARO クラスター リソース グループを参照し、ARO リソースを選択します。
        *   下のクラスター ダッシュボードの例では、*myResourceGroup* を参照し、*myAROCluster* リソースを選択しています。
