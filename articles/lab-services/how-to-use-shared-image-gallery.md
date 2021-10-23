---
title: Azure Lab Services で共有イメージ ギャラリーを使用する | Microsoft Docs
description: 共有イメージ ギャラリーを使用するようにラボ アカウントを構成して、ユーザーが他のユーザーとイメージを共有したり、他のユーザーがイメージを使用してラボでテンプレート VM を作成したりできるようにする方法について説明します。
ms.topic: how-to
ms.date: 09/11/2020
ms.openlocfilehash: cadb95d4946eef02c98a322e61489588bd8ea767
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179946"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Azure Lab Services で共有イメージ ギャラリーを使用する

イメージには、VM にインストールされているオペレーティング システム、ソフトウェア アプリケーション、ファイル、および設定が含まれています。  新しいラボを設定するときに使用できるイメージには、次の 2 つの種類があります。
-   Azure 内で使用するために Microsoft によって事前に構築された Marketplace イメージ。  これらのイメージには、Windows または Linux がインストールされ、ソフトウェア アプリケーションが含まれる場合もあります。  たとえば、[Data Science Virtual Machine イメージ](../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm)には、インストール済みのディープ ラーニング フレームワークとツールが含まれています。
-   教育機関の IT 部門や他の教師によって作成されるカスタム イメージ。  Windows と Linux の両方のカスタム イメージを作成し、独自のニーズに基づいて Microsoft とサードパーティ製のアプリケーションを柔軟にインストールできます。  また、ファイルの追加やアプリケーション設定の変更なども行うことができます。

この記事では、教師やラボ管理者がテンプレート仮想マシンからカスタム イメージを作成して[共有イメージ ギャラリー](../virtual-machines/shared-image-galleries.md)に保存し、他のユーザーが新しいラボを作成する際に使用できるようにする方法について説明します。

> [!IMPORTANT]
> 共有イメージ ギャラリーの使用中は、Azure Lab Services では、128 GB 未満の OS ディスク領域を持つイメージのみがサポートされます。 128 GB を超えるディスク領域を持つイメージ、または複数のディスクのイメージは、ラボの作成時に仮想マシン イメージの一覧に表示されません。

## <a name="scenarios"></a>シナリオ
この機能でサポートされるいくつかのシナリオを次に示します。 

- ラボ アカウント管理者がラボ アカウントに共有イメージ ギャラリーをアタッチし、ラボのコンテキスト外で共有イメージ ギャラリーにイメージをアップロードします。 そうすると、ラボ作成者が共有イメージ ギャラリーからそのイメージを使用して、ラボを作成できます。 
- ラボ アカウント管理者が、ラボ アカウントに共有イメージ ギャラリーをアタッチします。 ラボ作成者 (インストラクター) が、カスタマイズ済みの自分のラボのイメージを共有イメージ ギャラリーに保存します。 そうすると、他のラボ作成者が共有イメージ ギャラリーからこのイメージを選択して、各自のラボ用のテンプレートを作成できます。 

    イメージを共有イメージ ギャラリーに保存すると、Azure Lab Services によって、同じ[地域](https://azure.microsoft.com/global-infrastructure/geographies/)内で使用可能な他のリージョンに、保存したイメージがレプリケートされます。 これにより、同じ地域内の他のリージョンで作成されたラボでイメージを使用できるようになります。 共有イメージ ギャラリーにイメージを保存すると、追加コストが発生します。これには、レプリケートされたすべてのイメージのコストが含まれます。 このコストは、Azure Lab Services の使用コストとは別のものです。 共有イメージ ギャラリーの価格について詳しくは、[共有イメージ ギャラリーの課金](../virtual-machines/shared-image-galleries.md#billing)に関するページを参照してください。
    
## <a name="prerequisites"></a>前提条件
- [共有イメージ ギャラリー](../virtual-machines/create-gallery.md)を作成します。
- あらかじめ共有イメージ ギャラリーをラボ アカウントにアタッチしておきます。 詳細な手順については、[共有イメージ ギャラリーをアタッチまたはデタッチする方法](how-to-attach-detach-shared-image-gallery.md)に関するページを参照してください。

## <a name="save-an-image-to-the-shared-image-gallery"></a>共有イメージ ギャラリーに既存のイメージを保存する
共有イメージ ギャラリーがアタッチされたら、ラボ アカウントの管理者または教師は、他の教師が再利用できるように共有イメージ ギャラリーにイメージを保存できます。 

1. ラボの **[テンプレート]** ページで、ツール バーにある **[Export to Shared Image Gallery]\(Shared Image Gallery にエクスポートする\)** を選択します。

    ![[Save image]\(イメージの保存\) ボタン](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. **[Export to Shared Image Gallery]\(Shared Image Gallery にエクスポートする\)** ダイアログで **イメージの名前を入力** し、 **[エクスポート]** を選択します。 

    ![[Export to Shared Image Gallery]\(Shared Image Gallery にエクスポートする\) ダイアログ](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)

3. この操作の進捗状況は **[テンプレート]** ページで確認できます。 この操作には時間がかかることがあります。 

    ![エクスポートが進行中](./media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. エクスポート操作に成功すると、次のメッセージが表示されます。

    ![エクスポート完了](./media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    共有イメージ ギャラリーにイメージを保存すると、別のラボを作成するときにギャラリーからそのイメージを使用できるようになります。 ラボのコンテキスト外で共有イメージ ギャラリーにイメージをアップロードすることもできます。 詳細については、次を参照してください。


    - [共有イメージ ギャラリーの概要](../virtual-machines/shared-image-galleries.md)
    - [カスタム イメージを作成するための推奨される方法](approaches-for-custom-image-creation.md)


    > [!IMPORTANT]
    > 共有イメージ ギャラリーに、Azure Lab Services 内の [ラボのテンプレート イメージを保存](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery)すると、そのイメージは **特殊化されたイメージ** としてギャラリーにアップロードされます。 [特殊化されたイメージ](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images)には、マシン固有の情報とユーザー プロファイルが保持されます。 一般化されたイメージは、引き続き Azure Lab Services の外部のギャラリーに直接アップロードできます。    

## <a name="use-a-custom-image-from-the-shared-image-gallery"></a>共有イメージ ギャラリーのカスタム イメージを使用する
教師は、新しいラボの設定中に作成したテンプレート VM の共有イメージ ギャラリーから利用できるカスタム イメージを選択できます。

![ギャラリーの仮想マシン イメージを使用する](./media/how-to-use-shared-image-gallery/use-shared-image.png)

> [!NOTE]
> Azure Lab Services の **一般化された** イメージと **特殊化された** イメージの両方に基づいて、テンプレート VM を作成できます。

### <a name="resave-a-custom-image-to-shared-image-gallery"></a>カスタム イメージを共有イメージ ギャラリーに再保存する

共有イメージ ギャラリーのカスタム イメージからラボを作成した後、テンプレート VM を使用してイメージに変更を加え、イメージを共有イメージ ギャラリーに再エクスポートできます。  再エクスポートするときに、新しいイメージを作成するか、元のイメージを更新するか選択できます。 

 ![[共有イメージ ギャラリーに再エクスポート] ダイアログ](./media/how-to-use-shared-image-gallery/reexport-to-shared-image-gallery-dialog.png) 

**[新しいイメージを作成]** を選択すると、新しい [イメージ定義](../virtual-machines/shared-image-galleries.md#image-definitions)が作成されます。  これにより、共有イメージ ギャラリーに既に存在する元のカスタム イメージを変更することなく、完全に新しいカスタム イメージを保存できます。

代わりに **[既存のイメージを更新]** を選択すると、元のカスタム イメージの定義が新しい [バージョン](../virtual-machines/shared-image-galleries.md#image-versions)で更新されます。  ラボ サービスでは、次にカスタム イメージを使用してラボが作成されるときに、最新バージョンが自動的に使用されます。

## <a name="next-steps"></a>次のステップ
共有イメージ ギャラリーをラボ アカウントにアタッチおよびデタッチして設定する方法については、[共有イメージ ギャラリーをアタッチおよびデタッチする方法](how-to-attach-detach-shared-image-gallery.md)に関するページを参照してください。

ラボのコンテキスト外で共有イメージ ギャラリーにカスタム イメージを取り込む他のオプションについては、「[カスタム イメージを作成するための推奨される方法](approaches-for-custom-image-creation.md)」を参照してください。

共有イメージ ギャラリー全般の詳細については、[共有イメージ ギャラリー](../virtual-machines/shared-image-galleries.md)に関するページを参照してください。