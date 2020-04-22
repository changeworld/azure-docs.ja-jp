---
title: Azure Marketplace 向けクラウド パートナー ポータルの仮想マシンの [体験版] タブ
description: Azure Marketplace の VM プランの作成で使用する [体験版] タブについて説明します。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 22538047adc17a40438359e11bff7fd20e43bcc6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273088"
---
# <a name="virtual-machine-test-drive-tab"></a>仮想マシンの [体験版] タブ

> [!IMPORTANT]
> 2020 年 4 月 13 日以降、Azure 仮想マシン オファーの管理のパートナー センターへの移行が開始されます。 移行後は、パートナー センターにてオファーを作成・管理することになります。 「[Azure 仮想マシン オファーを作成する](https://aka.ms/CreateAzureVMoffer)」の手順に従って、移行されたオファーを管理します。

**[新しいプラン]** ページの **[体験版]** タブを使用すると、標準シナリオで製品の主な機能とメリットを示すセルフ型ハンズオン デモを見込み顧客に提供できます。  体験版は、体験版をサポートする種類のプランのオプション機能です。  体験版では、適切に実装する資産をサポートしている必要があります。  詳細については、記事「[Azure Marketplace Test Drive (Azure Marketplace 体験版) ](https://azure.microsoft.com/blog/azure-marketplace-test-drive/)」をご覧ください。  <!--TD: Replace with migrated version of Test Drive article! -->

この機能を有効にするには、 **[体験版]** タブで、 **[Enable a Test Drive] (体験版を有効にする)** で **[はい]** オプションをクリックします。  **[体験版]** タブには編集に使用できるフィールドが表示されます。  フィールド名に付いているアスタリスク (*) は、そのフィールドが必須であることを示します。

![仮想マシンの [新しいプラン] フォームの [体験版] タブ](./media/publishvm_007.png)


## <a name="field-values"></a>フィールドの値

次の表では、これらのフィールドの目的および内容について説明しています。  必須フィールドはアスタリスク (*) で示されます。


|    フィールド                  |       説明                                                            |
|  ---------                |     ---------------                                                          |
|  *詳細*   |  |
| **説明\***           | 体験版シナリオの概要を提供します。 このテキストは、体験版のプロビジョニング中にユーザーに表示されます。 書式設定された内容を提供したい場合、このフィールドでは基本的な HTML を使用できます。  |
| **[User Manual]\(ユーザー マニュアル\)\***           | 体験版のユーザーがソリューションの使用方法を理解するのに役立つ、詳細なユーザー マニュアル (.pdf) をアップロードします。  |
| **Test Drive Demo Video (体験版のデモ ビデオ)** | ソリューションを紹介する動画をアップロードします。  このオプションを選択した場合は、名前、動画の URL (YouTube または Vimeo でホストされる)、動画のサムネイル (533 x 324 ピクセル) を指定する必要があります。 |
| *技術的構成* |  |
| **[インスタンス]\***             | VM インスタンスの利用可能なリージョンと相対的な可用性を指定します (詳細は情報アイコンをクリックしてください)。  <br/>潜在的な体験版の同時セッションは、サブスクリプションのクォータ制限を超えてはいけません。  前者は次のように算出されます: [選択したリージョンの数] x [ホット インスタンス数] + [選択したリージョンの数] x [ウォーム インスタンス数] + [選択したリージョンの数] x [コールド インスタンス数] |
| **体験版の期間\***   | 最大セッション期間 (時間単位)。 この期間が過ぎると、体験版セッションは自動的に終了します。  |
|**[Test Drive ARM Template]\(体験版 ARM テンプレート\)\***| この体験版に関連付けられている Azure Resource Manager テンプレートをアップロードします。 詳細については、「[Transforming Virtual Machine Deployment Template for Test Drive (仮想マシンの展開テンプレートを体験版用に変換する)](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)」を参照してください。 |
| **[Access Information]\(アクセス情報\)\***    | Azure Resource Manager のアクセスと試用版のログイン情報。プレーン テキストまたはシンプルな HTML として書き込みます。 |
| *体験版デプロイ サブスクリプションの詳細* |  |
| **[Azure サブスクリプション ID]\*** | [Microsoft Azure portal](https://ms.portal.azure.com) にサインインし、左側のメニューバーで **[サブスクリプション]** をクリックして取得できます (例: "a83645ac-1234-5ab6-6789-1h234g764ghty")    この識別子は、フォーム `a83645ac-1234-5ab6-6789-1h234g764ghty` の GUID である必要があります。|
| **[Azure AD Tenant ID]\(Azure AD テナント ID\)\***    | Azure Active Directory テナント ID。  [Microsoft Azure portal](https://ms.portal.azure.com) にサインインし、左側のメニューバーで **[Azure Active Directory]** をクリックした後、中央のメニューバーで **[プロパティ]** をクリックし、フォームから **[ディレクトリ ID]** をコピーすることで取得できます。  また、この識別子は GUID である必要があります。  空白の場合は、組織のテナント ID を作成する必要があります。 |
| **[Azure AD App Id]\(Azure AD アプリ ID\)\***       | 登録されている Azure VM ソリューションの識別子  |
| **[Azure AD App Key]\(Azure AD アプリ キー\)\***      | 登録済みのソリューションの認証キー |
|   |   |


## <a name="next-steps"></a>次のステップ

次の [[Marketplace]](./cpp-marketplace-tab.md) タブでは、ソリューションに関するマーケティングおよび法的な情報を指定します。
