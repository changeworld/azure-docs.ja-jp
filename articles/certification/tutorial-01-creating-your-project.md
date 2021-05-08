---
title: Azure Certified Device プログラム - チュートリアル - プロジェクトを作成する
description: Azure Certified Device ポータルでプロジェクトを作成するためのガイド
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 11d72847209a2e706d4aa32d38af1b2c8af3dfa0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313999"
---
# <a name="tutorial-create-your-project"></a>チュートリアル: プロジェクトを作成する

Azure Certified Device プログラムによるデバイスの認定に興味を持っていただきありがとうございます。 デバイスに適した認定プログラムを選択したら、いつでもポータルでの作業を開始できます。

このチュートリアルでは、次の内容を学習します。

> [!div class="checklist"]
> * [Azure Certified Device ポータル](https://certify.azure.com/)にサインインする
> * デバイスの新しい認定プロジェクトを作成する
> * プロジェクトの基本的なデバイスの詳細を指定する

## <a name="prerequisites"></a>前提条件

- 職場または学校の有効な [Azure Active Directory アカウント](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)が必要です。
- 検証済みの Microsoft Partner Network (MPN) アカウントが必要です。 MPN アカウントをお持ちでない場合は、最初に[パートナー ネットワークに参加](https://partner.microsoft.com/)してください。

## <a name="signing-into-the-azure-certified-device-portal"></a>Azure Certified Device ポータルにサインインする

最初に、ポータルにサインインする必要があります。デバイス情報の入力、認定テストの実施、Azure Certified Device カタログへのデバイス発行管理は、ポータルから行うことになります。

1. [Azure Certified Device ポータル](https://certify.azure.com)に移動します。
1. 左側にある [`Company profile`]\(企業情報\) を選択し、製造元情報を更新します。
   ![[Company profile]\(企業情報\) セクション](./media/images/company-profile.png)
1. プログラムの契約に同意して、プロジェクトを開始します。

## <a name="creating-your-project-on-the-portal"></a>ポータルでプロジェクトを作成する

ポータルでの準備が完了したら、認定プロセスを開始できます。 まず、デバイスのプロジェクトを作成する必要があります。

1. [Home]\(ホーム\) 画面の [`Create new project`]\(新しいプロジェクトの作成\) を選択します。 次のセクションで基本的なデバイス情報を追加するためのウィンドウが開きます。

 ![[Create new project]\(新しいプロジェクトの作成\) ボタンの画像](./media/images/create-new-project.png)

## <a name="identifying-basic-device-information"></a>基本的なデバイス情報を指定する

次に、基本的なデバイス情報を指定する必要があります。 この情報は後で編集できます。

1. [`Basics`]\(基本\) セクションで要求されたフィールドに入力します。 **必須** フィールドの詳細については、次の表を参照してください。

    | フィールド                  | 説明                                                                                                                         |
    |------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
    | プロジェクト名           | Azure Certified Device カタログには表示されない内部名                                                        |
    | デバイス名            | 公開用のデバイス名                                                                                                |
    | デバイスの種類            | Finished Product または Solution-Ready Developer Kit の指定。     用語の詳細については、「[認定の用語集](./resources-glossary.md)」を参照してください。                                                                     |
    | デバイス クラス           | ゲートウェイ、センサーなど。  用語の詳細については、「[認定の用語集](./resources-glossary.md)」を参照してください。                                                                    |
    | デバイス ソース コード URL | Solution-Ready Dev Kit を認定する場合は必須。それ以外の場合は省略可能です。 URL は、GitHub におけるデバイスコードの場所にする必要があります。 |
1. [`Next`]\(次へ\) ボタンを選択して [`Certifications`]\(認定\) タブに進みます。

    ![[Create new project]\(新しいプロジェクトの作成\) フォームの [Certifications]\(認定\) タブの画像](./media/images/create-new-project-certificationswindow.png)

1. デバイスに関して取得したい認定を指定します。
1. [`Create`]\(作成\) を選択すると、新しいプロジェクトが保存され、ポータルのホーム ページに表示されます。

    ![プロジェクト テーブルの画像](./media/images/project-table.png)

1. テーブルでプロジェクト名を選択します。 プロジェクトのサマリー ページが表示され、デバイスに関するその他の詳細情報を追加したり表示したりすることができます。

    ![プロジェクトの詳細ページの画像](./media/images/device-details-section.png)

## <a name="next-steps"></a>次のステップ

Microsoft の認定サービスを使用してデバイスの詳細を追加し、デバイスをテストする準備はこれで完了です。 次の記事に進んで、デバイスの詳細を編集する方法をご覧ください。
> [!div class="nextstepaction"]
> [チュートリアル: デバイスの詳細を追加する](tutorial-02-adding-device-details.md)
