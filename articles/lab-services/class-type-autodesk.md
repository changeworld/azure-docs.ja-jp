---
title: Azure Lab Services を使用して Autodesk でラボを設定する
description: Autodesk でエンジニアリング クラスを教えるためのラボの設定方法について説明します。
author: nicolela
ms.topic: how-to
ms.date: 04/21/2021
ms.author: nicolela
ms.openlocfilehash: 2c9abda8f42023ebc54de77caadbe2860afe137f
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177063"
---
# <a name="set-up-labs-for-autodesk"></a>Autodesk のラボを設定する

この記事では、エンジニアリング クラス用に Autodesk Inventor および Autodesk Revit のソフトウェアを設定する方法について説明します。
- [Inventor コンピューター支援設計 (CAD)](https://www.autodesk.com/products/inventor/new-features) と[コンピューター支援製造 (CAM)](https://www.autodesk.com/products/inventor-cam/overview) は、3D モデリングを提供し、エンジニアリング設計で使用されます。
- [Revit](https://www.autodesk.com/products/revit/overview) は、3D ビルディング インフォメーション モデリング (BIM) のアーキテクチャ設計で使用されます。

Autodesk は、大学と K-12 の学校の両方で一般的に使用されています。  たとえば、K-12 では、AutoDesk は [Project Lead the Way (PLTW)](./class-type-pltw.md) のカリキュラムに含まれています。

## <a name="lab-configuration"></a>ラボの構成

このラボを設定するには、Azure サブスクリプションとラボ アカウントが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成できます。 新しいラボ アカウントの作成の詳細については、[ラボ アカウントの設定方法](./tutorial-setup-lab-account.md)に関するチュートリアルを参照してください。 既存のラボ アカウントを使用することもできます。

### <a name="lab-account-settings"></a>ラボ アカウントの設定

次の表の説明に従って、ラボ アカウントの設定を有効にします。 Azure Marketplace イメージを有効にする方法の詳細については、「[ラボ作成者が利用できる Azure Marketplace イメージを指定する](./specify-marketplace-images.md)」を参照してください。

| ラボ アカウントの設定 | Instructions |
| -------------------- | ----- |
| Marketplace イメージ | ラボ アカウント内で使用する Windows 10 イメージを有効にします。 |

### <a name="lab-settings"></a>ラボの設定
仮想マシン (VM) の推奨サイズは、学生が実行する必要があるワークロードの種類によって異なります。  Small GPU (視覚化) サイズを使用することをお勧めします。

| ラボの設定 | 値と説明 |
| ------------ | ------------------ |
| 仮想マシンのサイズ | **Small GPU (視覚化)**<br>OpenGL や DirectX などのフレームワークを使用するリモート視覚化、ストリーミング、ゲーム、エンコードに最適です。 | 

> [!NOTE]
> **小規模 GPU (視覚化)** 仮想マシン サイズは、高パフォーマンスのグラフィックス エクスペリエンスを実現するように構成されています。 この仮想マシン サイズの詳細については、[GPU を使用したラボの設定方法](./how-to-setup-lab-gpu.md)に関する記事を参照してください。

### <a name="license-server"></a>ライセンス サーバー
Autodesk のネットワーク ライセンス モデルを使用する予定がある場合は、ライセンス サーバーにアクセスする必要があります。  詳細については、Autodesk の記事「[Network License Administration](https://knowledge.autodesk.com/customer-service/network-license-administration/network-deployment/preparing-for-deployment/determining-installation-type)」 (ネットワーク ライセンス管理) を参照してください。

AutoDesk は、Autodesk ソフトウェアでネットワーク ライセンスを使用するために、Autodesk Network License Manager をライセンス サーバーにインストールするための[詳細な手順](https://knowledge.autodesk.com/customer-service/network-license-administration/install-and-configure-network-license)を提供しています。  通常、このライセンス サーバーは、オンプレミスのネットワークに配置されているか、Azure 仮想ネットワーク内の Azure 仮想マシン (VM) でホストされています。

ライセンス サーバーを設定した後は、[仮想ネットワーク](./how-to-connect-peer-virtual-network.md)を[ラボ アカウント](./tutorial-setup-lab-account.md)にピアリングする必要があります。 ラボの VM とライセンス サーバーの間で通信できるように、ラボを作成する "*前*" に、ネットワーク ピアリングを実行する必要があります。

Autodesk によって生成されたライセンス ファイルには、ライセンス サーバーの MAC アドレスが埋め込まれます。  Azure VM を使用してライセンス サーバーをホストする場合、ライセンス サーバーの MAC アドレスが変更されていないことを確認することが重要です。 MAC アドレスが変更された場合は、ライセンス ファイルを再生成する必要があります。 MAC アドレスが変更されないようにするには、次のようにします。

- ライセンス サーバーをホストする Azure VM の[静的プライベート IP と MAC アドレスを設定](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address)します。
- 十分な VM 容量があるリージョンまたは場所にラボ アカウントとライセンス サーバーの仮想ネットワークの両方を設定して、後でこれらのリソースを新しいリージョンまたは場所に移動する必要がないようにします。

詳細については、[共有リソースとしてのライセンス サーバーの設定](./how-to-create-a-lab-with-shared-resource.md)に関する記事を参照してください。

> [!WARNING]
> ラボを作成する **前** に、ライセンス サーバーの仮想ネットワークにラボ アカウントの [仮想ネットワークをピアリングする](./how-to-connect-peer-virtual-network.md)ことを忘れないでください。

### <a name="template-machine"></a>テンプレート マシン
このセクションの手順では、テンプレート VM を設定する方法を示します。

1. テンプレート VM を起動し、そのマシンに接続します。

1. [AutoDesk の指示](https://knowledge.autodesk.com/customer-service/download-install/install-software)に従って、Inventor と Revit をダウンロードしてインストールします。  メッセージが表示されたら、ライセンス サーバーのコンピューター名を指定します。

1.  最後に、テンプレート VM を発行して、学生の VM を作成します。

## <a name="cost"></a>コスト
このクラスのコスト見積もりの例について説明します。  この見積もりには、ライセンス サーバーの実行に伴うコストは含まれません。 たとえば、クラスに 25 人の学生がいて、それぞれに予定された授業時間が 20 時間あるとします。  また、各学生には、予定された授業時間の他に、宿題や課題を行うための時間として追加の 10 時間が割り当てられます。  ここで選択した仮想マシンのサイズは **小規模 GPU (視覚化)** であり、160 ラボ ユニットです。

- 25 人の学生 &times; (予定された 20 時間 + 割り当てられた 10 時間) &times; 160 ラボ ユニット &times; 0.01 USD/時間 = 1200.00 USD

> [!IMPORTANT] 
> このコスト見積もりは、例を示すためだけのものです。  現在の価格の詳細については、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」を参照してください。

## <a name="next-steps"></a>次のステップ

ラボを設定するときは、次の記事を参照してください。

- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users) 
