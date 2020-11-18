---
title: Azure Lab Services を使用して Project Lead the Way ラボを設定する
description: Project Lead the Way クラスの授業用ラボを設定する方法について説明します。
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: 8585d09759319eef04da5ed68fec603cfa390093
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496391"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Project Lead the Way クラス用ラボを設定する

[Project Lead the Way (PLTW)](https://www.pltw.org/) は、コンピューター サイエンス、エンジニアリング、バイオメディカル サイエンスの PreK-12 カリキュラムを米国全域に提供する非営利団体です。  各 PLTW クラスでは、学生は実践的な学習体験の一環としてさまざまなソフトウェア アプリケーションを使用します。  それらのソフトウェア アプリケーションの多くは、高速 CPU、場合によっては GPU を必要とします。  この記事では、9 年生から 12 年生に通常提供される次の PLTW クラス用ラボを設定する方法について説明します。

- **エンジニアリング設計の概要**

    3D モデリングに [Autodesk の Inventor コンピューター支援設計 (CAD)](https://www.autodesk.com/products/inventor/new-features) ソフトウェアを使用するなど、エンジニアリング設計のプロセスを学生に紹介します。

- **エンジニアリングの原則**
    
    学生は、エンジニアリング メカニズム、構造および材料の強度、自動化について学習します。  このクラスでは、[MD Solids](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf)、[West Point Bridge Designer](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)、[America's Army Simulation](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf) などのソフトウェアを使用します。

- **土木工学とアーキテクチャ**

    学生は、3D ビルディング インフォメーション モデリング (BIM) に [Autodesk の Revit](https://www.autodesk.com/products/revit/overview) アーキテクチャ設計ソフトウェアを使用して、建物とサイトの設計と開発について学習します。

- **コンピューター統合製造**

    学生は、ロボット工学とオートメーションを含む最新の製造プロセスを探求します。   このクラスでは、学生は、[Autodesk の Inventor CAD](https://www.autodesk.com/products/inventor/new-features) と [Autodesk の Inventor コンピューター支援製造 (CAM)](https://www.autodesk.com/products/inventor-cam/overview) ソフトウェアを使用します。 

- **デジタル エレクトロニクス**

    学生は、[National Instrument の Multisim](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) シミュレーションおよび回路設計ソフトウェアを使用して電子論理回路およびデバイスについて学習します。

- **エンジニアリング設計と開発**

    学生は、エンジニアのパネルに提示する調査、設計、テストを組み合わせたエンドツーエンドのソリューションに寄与します。  このクラスでは、学生は、[Autodesk の Inventor CAD](https://www.autodesk.com/products/inventor/new-features) ソフトウェアを使用します。

- **コンピューター サイエンスの基本**

    計算の概念とツールを学生に紹介します。  学生は、[VEXcode V5 Blocks](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf) などのコーディング環境を使用して、ブロックベースのプログラミングから開始してテキストベースのコーディングの使用に進みます。

- **コンピューター サイエンスの原則**
    
    学生は、[Microsoft の Visual Studio Code 開発環境](https://code.visualstudio.com/)を使用して、[Python](https://www.python.org/) によるプログラミングの専門知識を深めます。 

- **コンピューター サイエンス A**

    学生は、モバイル アプリの開発を学ぶことで、このクラスでプログラミングの専門知識を深めます。  このクラスでは、[Microsoft の Visual Studio Code 開発環境](https://code.visualstudio.com/)を使用して、[Java](https://www.java.com/) を学習します。  学生はさらに、モバイル アプリ コードの実行とテストを可能にするエミュレーターも使用します。  Azure Labs でエミュレーターを設定する方法の詳細については、azlabspilot@microsoft.com にお問い合わせください。

各クラスの[ソフトウェアの完全な一覧](https://www.pltw.org/pltw-software)については、PLTW のサイトを参照してください。

## <a name="lab-configuration"></a>ラボの構成
PLTW のラボを設定するには、Azure サブスクリプションとラボ アカウントが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成できます。 新しいラボ アカウントの作成の詳細については、[ラボ アカウントの設定方法](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account)に関するチュートリアルを参照してください。 既存のラボ アカウントを使用することもできます。

ラボ アカウントを作成したら、学校が提供する PLTW クラスのセッションごとに個別のラボを作成する必要があります。  また、PLTW クラスの種類ごとに個別のイメージを作成することをお勧めします。  ラボとイメージを構成する方法の詳細については、ブログの投稿「[Moving from a Physical Lab to Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)」 (物理ラボから Azure Lab Services への移行) を参照してください。

### <a name="lab-account-settings"></a>ラボ アカウントの設定
以下の表に記載されているラボ アカウントの設定を有効にします。 マーケットプレース イメージを有効にする方法の詳細については、[ラボ作成者が利用できる Marketplace イメージを指定する方法](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)に関する記事を参照してください。

| ラボ アカウントの設定 | Instructions |
| -------------------- | ----- |
| Marketplace イメージ | ラボ アカウント内で使用する Windows 10 Pro イメージを有効にします。 |

### <a name="lab-settings"></a>ラボの設定
PLTW クラスに使用する VM の推奨サイズは、学生がクラスで実行しているワークロードの種類によって異なります。  上記のクラスでは、Large および Small GPU (視覚化) VM サイズを使用することをお勧めします。  PLTW クラスのラボを設定する際は、次の表のガイダンスを参照してください。

| ラボの設定 | 値/説明 |
| ------------ | ------------------ |
|仮想マシンのサイズ| **小規模 GPU (視覚化)** 。  この VM は、OpenGL や DirectX などのフレームワークを使用するリモート視覚化、ストリーミング、ゲーム、エンコードに最適です。  次の PLTW クラスには、このサイズを使用することをお勧めします: 土木工学とアーキテクチャ、デジタル エレクトロニクス、コンピューター統合製造、エンジニアリング設計と開発。
|仮想マシンのサイズ| **Large**。  このサイズは、高速の CPU、ローカル ディスクのよりすぐれたパフォーマンス、大規模なデータベース、大きなメモリ キャッシュを必要とするアプリケーションに最適です。  次の PLTW クラスには、このサイズを使用することをお勧めします: エンジニアリング設計の概要、エンジニアリングの原則、コンピューター サイエンスの基本、コンピューター サイエンスの原則、コンピューター サイエンス A。

### <a name="licensing-server"></a>ライセンス サーバー
上記の PLTW クラスで使用されるソフトウェアの多くは、ライセンスサーバーへのアクセスを必要と " *_しません_*"。  ただし、次のソフトウェアに Autodesk のネットワーク ライセンス モデルを使用する予定がある場合は、ライセンス サーバーにアクセスする必要があります。
-   Revit
-   Inventor
-   Inventor CAM

Autodesk のソフトウェアでネットワーク ライセンスを使用するために、PLTW は、Autodesk の License Manager をライセンス サーバーにインストールするための[詳細な手順を](https://www.pltw.org/pltw-software)提供しています。  通常、このライセンス サーバーは、オンプレミスのネットワークに配置されているか、Azure 仮想ネットワーク (VNet) 内の Azure 仮想マシン (VM) でホストされています。

ライセンス サーバーを設定した後、[VNet](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) を[ラボ アカウント](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account)にピアリングする必要があります。 ネットワーク ピアリングは、ラボの仮想マシンがライセンス サーバーに、またその逆にアクセスできるように、ラボを作成する "_前*" に行う必要があります。

Autodesk によって生成されたライセンス ファイルには、ライセンス サーバーの MAC アドレスが埋め込まれます。  Azure VM を使用してライセンス サーバーをホストする場合、ライセンス サーバーの MAC アドレスが変更されていないことを確認することが重要です。   それ以外の場合は、MAC アドレスが変更されたときに、ライセンス ファイルを再生成する必要があります。  MAC アドレスが変更されないようにするには、次のヒントに従ってください。

- ライセンス サーバーをホストする Azure VM の[静的プライベート IP と MAC アドレスを設定](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource#static-private-ip-and-mac-address)します。
- 十分な VM 容量があるリージョンまたは場所にラボ アカウントとライセンス サーバーの VNet の両方を設定して、後でこれらのリソースを新しいリージョンまたは場所に移動する必要がないようにします。

詳細については、[ライセンス サーバーを共有リソースとして設定する方法](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource)に関する記事も参照してください。

### <a name="template-machine"></a>テンプレート マシン
PLTW に必要なインストール ファイルの一部は大きいため、ラボのテンプレート マシンにダウンロードするときにコピーに時間がかかります。

インストール ファイルをテンプレート マシンにダウンロードして、そこにすべてをインストールするのではなく、物理環境で PLTW イメージを作成することをお勧めします。  次に、イメージを Shared Image Gallery にインポートし、これらのカスタム イメージを使用してラボを作成することができます。  詳細については、記事「[カスタム イメージを Shared Image Gallery にアップロードする](https://docs.microsoft.com/azure/lab-services/upload-custom-image-shared-image-gallery)」を参照してください。

この推奨事項に従ってラボを設定するための主なタスクは、次のとおりです。

1. 物理環境で、クラスのイメージを作成します。

    a.  PLTW の詳細な手順を使用して、インストール ファイルをダウンロードし、必要なソフトウェアをインストールします。

    > [!NOTE]    
    > Autodesk のアプリケーションをインストールする場合、Autodesk をインストールするコンピューターは、ライセンス サーバーと通信できる必要があります (Autodesk のインストール ウィザードにより、ライセンス サーバーがホストされているマシンのコンピューター名を指定するように求められます)。  Azure VM でライセンス サーバーをホストしている場合、Autodesk のインストール ウィザードがライセンス サーバーにアクセスできるように、ラボのテンプレート マシンに Autodesk がインストールされるのを待つことが必要な場合があります

    b.  [OneDrive (または学校で使用できる他のバックアップ オプション) をインストールして構成します](https://docs.microsoft.com/azure/lab-services/how-to-prepare-windows-template#install-and-configure-onedrive)。
    
    c.  [Windows Updates をインストールして構成します](https://docs.microsoft.com/azure/lab-services/how-to-prepare-windows-template#install-and-configure-updates)。

1.  [ラボ アカウントに接続されている Shared Image Gallery](https://docs.microsoft.com/azure/lab-services/how-to-attach-detach-shared-image-gallery) にカスタム イメージをアップロードします。

1.  ラボを作成し、前の手順でアップロードしたカスタム イメージを選択します。

1.  ラボが作成されたら、起動し、テンプレート マシンに接続して、イメージが想定どおりに動作することを検証します。

1.  最後に、テンプレート マシンを発行して、学生の VM を作成します。

## <a name="student-devices"></a>学生用デバイス
学生は、Windows または Mac コンピューターと Chromebook から自分のラボ VM に接続します。  これらの各オプションの手順へのリンクを次に示します。

- [Windows からの接続](https://docs.microsoft.com/azure/lab-services/how-to-use-classroom-lab#connect-to-the-vm)
- [Mac からの接続](https://docs.microsoft.com/azure/lab-services/connect-virtual-machine-mac-remote-desktop)
- [Chromebook からの接続](https://docs.microsoft.com/azure/lab-services/connect-virtual-machine-chromebook-remote-desktop)

## <a name="cost"></a>コスト
上記の PLTW クラスの考えられるコスト見積もりについて説明しましょう。  この見積もりには、ライセンス サーバーの実行および Shared Image Gallery の使用のコストは含まれません。  クラスの学生数は 25 名とします。  予定された授業時間は 20 時間です。  また、各学生には、予定された授業時間の他に、宿題や課題を行うための時間として 10 時間が割り当てられます。  **Large** および **Small GPU (視覚化)** の両方のサイズに関する次のコスト見積もりを参照してください。

- **Large VM**

    25 名の学生 (予定された 20 時間 + 割り当てられた 10 時間) × 70 ラボ ユニット × 0.01 USD/時間 = 525.00 USD

- **Small GPU (視覚化)**

    25 名の学生 (予定された 20 時間 + 割り当てられた 10 時間) × 160 ラボ ユニット × 0.01 USD/時間 = 1200.00 USD

> [!IMPORTANT] 
> このコスト見積もりは、例を示すためだけのものです。  価格について詳しくは、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」をご覧ください。

> [!NOTE] 
> PLTW クラスの多くは、MIT App Inventor など、ブラウザーを介してアクセスされるアプリケーションを使用します。  これらのブラウザーベースのアプリケーションは、高速の CPU または GPU を必要とせず、インターネットに接続されている任意のデバイスからアクセスできます。  学生がこのような種類のアプリケーションを使用している場合、ラボの VM でブラウザーを使用するのではなく、物理デバイスでブラウザーを使用することをお勧めします。  これにより、高速の CPU または GPU を必要とするアプリケーションにラボ VM のみを使用することで、コストを抑えるのに役立ちます。

## <a name="next-steps"></a>次のステップ
次の手順は、どのラボの設定でも同じです。

- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)。 