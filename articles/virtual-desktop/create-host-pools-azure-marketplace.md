---
title: Azure Virtual Desktop ホスト プール - Azure portal - Azure
description: Azure portal を使用して Azure Virtual Desktop ホスト プールを作成する方法。
author: Heidilohr
ms.topic: tutorial
ms.custom: references_regions
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 3b95302be3eda412f6941abe359f6da63e235d24
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492040"
---
# <a name="tutorial-create-a-host-pool"></a>チュートリアル: ホスト プールを作成する

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトを含む Azure Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを含まない Azure Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md)を参照してください。 Azure Virtual Desktop (クラシック) を使用して作成したオブジェクトは、Azure portal では管理できません。

ホスト プールは、Azure Virtual Desktop 環境内にある、1 つまたは複数の同一の仮想マシン (VM) (''セッション ホスト'' ともいう) をまとめたものです。 各ホスト プールには、物理デスクトップの場合と同じようにユーザーが利用できるアプリ グループを含めることができます。 デプロイ アーキテクチャの詳細については、「[Azure Virtual Desktop 環境](environment-setup.md)」を参照してください。 あなたが Azure Virtual Desktop 用のリモート アプリ ストリーミングを使用しているアプリ開発者である場合、顧客またはユーザーは、物理デバイス上のローカル アプリと同じようにあなたのアプリを使用できます。 アプリ開発者として Azure Virtual Desktop を使用する方法の詳細については、「[Azure Virtual Desktop リモート アプリ ストリーミング](./remote-app-streaming/custom-apps.md)」のドキュメントを参照してください。

>[!NOTE]
>あなたが Azure Virtual Desktop 用のリモート アプリ ストリーミングを使用しているアプリ開発者で、あなたのアプリのユーザーがあなたのデプロイと同じ組織内にいる場合は、既存の Azure テナントを使用してホスト プールを作成できます。 ユーザーが組織外にいる場合は、セキュリティ上の理由から、組織ごとに少なくとも 1 つのホスト プールがある個別の Azure テナントを作成する必要があります。 デプロイのセキュリティを維持するために従うことが推奨される運用方法について詳しくは、「[アーキテクチャに関する推奨事項](./remote-app-streaming/architecture-recs.md)」を参照してください。

この記事では、Azure portal を使用して Azure Virtual Desktop 環境のホスト プールを作成するためのセットアップ プロセスについて説明します。 この方法では、ブラウザーベースのユーザー インターフェイスを使って Azure Virtual Desktop にホスト プールを作成し、Azure サブスクリプション内の VM を使用してリソース グループを作成し、それらの VM を Active Directory (AD) ドメインまたは Azure Active Directory (Azure AD) テナントに参加させて、VM を Azure Virtual Desktop に登録します。

## <a name="prerequisites"></a>前提条件

組織のデプロイを設定する IT プロフェッショナルであるか、アプリケーションを顧客に提供するアプリ開発者であるかによって、異なる 2 つの要件セットがあります。

### <a name="requirements-for-it-professionals"></a>IT プロフェッショナル向けの要件

ホスト プールを作成するには、次のパラメーターを入力する必要があります。

- VM イメージ名
- VM 構成
- ドメインとネットワークのプロパティ
- Azure Virtual Desktop ホスト プールのプロパティ

次の情報も把握しておく必要があります。

- 使用するイメージのソースがある場所。 Azure ギャラリーから取得したものか。それともカスタム イメージか。
- ドメイン参加資格情報。

### <a name="requirements-for-app-developers"></a>アプリ開発者向けの要件

あなたが Azure Virtual Desktop 用のリモート アプリ ストリーミングを使用して顧客にアプリを提供するアプリ開発者である場合は、次の作業を開始する必要があります。

- 組織のアプリをエンドユーザーに提供する予定の場合は、実際にそのアプリの準備ができていることを確認してください。 詳細については、「[Azure Virtual Desktop を使用してカスタム アプリをホストする方法](./remote-app-streaming/custom-apps.md)」を参照してください。
- 既存の Azure ギャラリー イメージ オプションがご自分のニーズに合わない場合は、セッション ホスト VM 用に独自のカスタム イメージを作成する必要もあります。 VM イメージの作成方法については、「[Azure にアップロードする Windows VHD または VHDX を準備する](../virtual-machines/windows/prepare-for-upload-vhd-image.md)」、および「[Azure で一般化された VM の管理対象イメージを作成する](../virtual-machines/windows/capture-image-resource.md)」をご覧ください。
- ドメイン参加資格情報。 Azure Virtual Desktop と互換性のある ID 管理システムをまだお持ちでない場合は、ホスト プールの ID 管理を設定する必要があります。 詳細については、「[マネージド ID を設定する](./remote-app-streaming/identities.md)」を参照してください。

### <a name="final-requirements"></a>最終的な要件

最後に、Microsoft.DesktopVirtualization リソース プロバイダーが登録されていることを確認します。 まだ行っていない場合は、 **[サブスクリプション]** に移動し、お使いのサブスクリプションの名前を選択して、 **[リソース プロバイダー]** を選択します。 **[DesktopVirtualization]** を検索し、 **[Microsoft.DesktopVirtualization]** を選んでから、 **[登録]** を選択します。

あなたがネットワークを作成する IT プロフェッショナルである場合は、Azure Resource Manager テンプレートを使用して Azure Virtual Desktop ホスト プールを作成するときに、Azure ギャラリー、マネージド イメージ、またはアンマネージド イメージから仮想マシンを作成することができます。 VM イメージの作成方法については、「[Azure にアップロードする Windows VHD または VHDX を準備する](../virtual-machines/windows/prepare-for-upload-vhd-image.md)」、および「[Azure で一般化された VM の管理対象イメージを作成する](../virtual-machines/windows/capture-image-resource.md)」をご覧ください。 (アプリ開発者である場合は、この部分について心配する必要はありません)。

最後になりましたが、Azure サブスクリプションをまだお持ちでない場合は、これらの手順に従い始める前に、必ず[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

## <a name="begin-the-host-pool-setup-process"></a>ホスト プールのセットアップ プロセスの開始

### <a name="portal"></a>[ポータル](#tab/azure-portal)

新しいホスト プールの作成を開始するには:

1. Azure Portal [https://portal.azure.com](https://portal.azure.com/) にサインインします。
   
   >[!NOTE]
   > US Gov ポータルにサインインする場合は、代わりに [https://portal.azure.us/](https://portal.azure.us/) にアクセスしてください。
   > 
   >Azure China ポータルにアクセスする場合は、[https://portal.azure.cn/](https://portal.azure.cn/) に移動してください。

2. 検索バーに「**Azure Virtual Desktop**」と入力し、[サービス] にある **[Azure Virtual Desktop]** を見つけて選択します。

3. **[Azure Virtual Desktop]** の概要ページで、 **[ホスト プールの作成]** を選択します。

4. **[基本]** タブのプロジェクトの詳細で、正しいサブスクリプションを選択します。

5. **[新規作成]** を選択して新しいリソース グループを作成するか、ドロップダウン メニューから既存のリソース グループを選択します。

6. ホスト プールの一意の名前を入力します。

7. [場所] フィールドで、ホスト プールの作成先にしたいリージョンをドロップダウン メニューから選択します。

   ご自分が選択したリージョンに関連付けられている Azure 地域は、このホスト プールとその関連オブジェクトのメタデータが格納される場所です。 サービス メタデータの格納先にしたい地域内のリージョンを選択していることを確認します。

     > [!div class="mx-imgBorder"]
     > ![米国東部の場所が選択されている [場所] フィールドを示す Azure portal のスクリーンショット。 このフィールドの横には、"メタデータは米国東部に格納されます。" というテキストが表示されます。](media/portal-location-field.png)
  
   >[!NOTE]
   > 米国外の[サポートされているリージョン](data-locations.md)にホスト プールを作成する場合は、リソース プロバイダーを再登録する必要があります。 再登録すると、場所を選択するためのドロップダウンに他のリージョンが表示されます。 再登録の方法については、[ホスト プールの作成](troubleshoot-set-up-issues.md#i-only-see-us-when-setting-the-location-for-my-service-objects)に関するトラブルシューティング記事をご覧ください。

8. [Host pool type]\(ホスト プールの種類\) で、自分のホスト プールを **[個人用]** と **[Pooled]\(プール\)** のいずれにするかを選択します。

    - **[個人用]** を選択した場合は、[割り当ての種類] フィールドで **[自動]** または **[ダイレクト]** を選択します。

      > [!div class="mx-imgBorder"]
      > ![[割り当ての種類] フィールドのドロップダウン メニューのスクリーンショット。 ユーザーは [自動] を選択しています。](media/assignment-type-field.png)

9.  **[Pooled]\(プール\)** を選択した場合は、次の情報を入力します。

     - **[Max session limit]\(最大セッション数\)** で、1 つのセッション ホストに負荷分散するユーザーの最大数を入力します。
     - **[負荷分散アルゴリズム]** には、使用パターンに応じて [breadth-first]\(幅優先\) と [depth-first]\(深さ優先\) のいずれかを選択します。 これらの各オプションの詳細については、「[ホスト プールの負荷分散方法](host-pool-load-balancing.md)」を参照してください。

       > [!div class="mx-imgBorder"]
       > ![[Pooled]\(プール\) が選択された [割り当ての種類] フィールドのスクリーンショット。 ユーザーは負荷分散のドロップダウン メニューの [breadth-first]\(幅優先\) にカーソルを合わせています。](media/pooled-assignment-type.png)

10. **[Next:仮想マシン >]** を選択します。

11. 仮想マシンを既に作成していて、それらを新しいホスト プールで使用する場合は、 **[いいえ]** を選択し、 **[次へ:ワークスペース >]** を選択して、 [[ワークスペース情報]](#workspace-information) セクションに移動します。 新しい仮想マシンを作成して新しいホスト プールに登録したい場合は、 **[はい]** を選択します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

まず、Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

サインインした後、[az desktopvirtualization hostpool create](/cli/azure/desktopvirtualization#az_desktopvirtualization_hostpool_create) コマンドを使用して新しいホスト プールを作成し、必要に応じて、セッション ホストをホスト プールに参加させるための登録トークンを作成します。

```azurecli
az desktopvirtualization hostpool create --name "MyHostPool" \
    --resource-group "MyResourceGroup" \ 
    --location "MyLocation" \
    --host-pool-type "Pooled" \
    --load-balancer-type "BreadthFirst" \
    --max-session-limit 999 \
    --personal-desktop-assignment-type "Automatic"  \
    --registration-info expiration-time="2022-03-22T14:01:54.9571247Z" registration-token-operation="Update" \
    --sso-context "KeyVaultPath" \
    --description "Description of this host pool" \
    --friendly-name "Friendly name of this host pool" \
    --tags tag1="value1" tag2="value2" 
```

新しい仮想マシンを作成し、それらを新しいホスト プールに登録する場合、次のセクションに進みます。 仮想マシンを既に作成していて、それらを新しいホスト プールで使用する場合は、「[ワークスペース情報](#workspace-information)」セクションに進んでください。 

---

ホスト プールを作成したので、セットアップ プロセスの次のパートに移って VM を作成しましょう。

## <a name="virtual-machine-details"></a>仮想マシンの詳細

最初のパートを完了したら、次は VM を設定する必要があります。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal のホスト プール セットアップ プロセス中に仮想マシンを設定するには:

1. **[リソース グループ]** で、仮想マシンを作成するリソース グループを選択します。 これは、ホスト プールに使用したリソース グループとは別のものにできます。

2. その後、**名前のプレフィックス** を指定して、セットアップ プロセスで作成される仮想マシンの名前を指定します。 サフィックスは、`-` に 0 から始まる番号が付いた形式になります。

3. 仮想マシンを作成する **仮想マシンの場所** を選択します。 これらは、ホスト プール用に選択したリージョンと同じでも、異なっていてもかまいません。 VM の価格はリージョンによって異なることに注意してください。また、可能であれば VM の場所をユーザーの近くにして、パフォーマンスを最大限に高めることをお勧めします。 詳細については、「[Azure Virtual Desktop のデータの場所](data-locations.md)」を参照してください。
   
4. 次に、実際のニーズに最も合った可用性オプションを選択します。 どのオプションが適しているかの詳細については、「[Azure の仮想マシンの可用性オプション](../virtual-machines/availability.md)」と [FAQ](./faq.yml#which-availability-option-is-best-for-me-) ご覧ください。
   
   > [!div class="mx-imgBorder"]
   > ![可用性ゾーンのドロップダウン メニューのスクリーンショット。 可用性ゾーン オプションが強調表示されています。](media/availability-zone.png)

5. 次に、仮想マシンを作成するために使用する必要があるイメージを選択します。 **[ギャラリー]** または **[ストレージ BLOB]** を選択できます。

    - **[ギャラリー]** を選択した場合は、ドロップダウン メニューから推奨イメージを 1 つ選択します。

      - Windows 10 Enterprise マルチセッション、バージョン 1909
      - Windows 10 Enterprise マルチセッション、バージョン 1909 + Microsoft 365 Apps
      - Windows Server 2019 Datacenter
      - Windows 10 Enterprise マルチセッション、バージョン 2004
      - Windows 10 Enterprise マルチセッション、バージョン 2004 + Microsoft 365 Apps

      目的のイメージが表示されない場合は、 **[すべてのイメージを表示]** を選択すると、ギャラリー内の別のイメージ、または Microsoft や他の発行元から提供されたイメージを選択できます。 選択したイメージが、[サポートされている OS イメージ](overview.md#supported-virtual-machine-os-images)のいずれかであることを確認します。

      > [!div class="mx-imgBorder"]
      > ![Microsoft 提供のイメージが一覧表示された Azure portal のスクリーンショット。](media/marketplace-images.png)

      また、 **[マイ アイテム]** に移動して、既にアップロード済みのカスタム イメージを選択することもできます。

      > [!div class="mx-imgBorder"]
      > ![[マイ アイテム] タブのスクリーンショット。](media/my-items.png)

    - **[Storage Blob]\(ストレージ BLOB\)** を選択した場合は、Hyper-V または Azure VM で独自のイメージ ビルドを利用できます。 ストレージ BLOB 内のイメージの場所を URI として入力するだけでかまいません。
   
   イメージの場所は可用性オプションに依存しませんが、イメージのゾーンの回復性によって、そのイメージを可用性ゾーンで使用できるかどうかが決まります。 イメージの作成中に可用性ゾーンを選択する場合は、ゾーンの回復性が有効になっているギャラリーのイメージを使用していることを確認してください。 ゾーンの回復性オプションのどれを使用すべきかの詳細については、[FAQ](./faq.yml#which-availability-option-is-best-for-me-) をご覧ください。

6. その後、使用する **仮想マシンのサイズ** を選択します。 既定のサイズのままにすることも、 **[サイズの変更]** を選択してサイズを変更することもできます。 **[サイズの変更]** を選択した場合は、表示されるウィンドウで、ワークロードに適した仮想マシンのサイズを選択してください。 仮想マシンのサイズと選択する必要があるサイズの詳細については、「[仮想マシンのサイズ設定ガイドライン](/windows-server/remote/remote-desktop-services/virtual-machine-recs?context=/azure/virtual-desktop/context/context)」を参照してください。

7. **[Number of VMs]\(VM の数\)** で、ホスト プール用に作成する VM の数を指定します。

    >[!NOTE]
    >セットアップ プロセスでは、ホスト プールの設定中に最大 400 個の VM を作成できます。各 VM セットアップ プロセスでは、リソース グループに 4 つのオブジェクトが作成されます。 この作成プロセスではサブスクリプションのクォータがチェックされません。そのため、入力する VM の数が、ご自分のリソース グループおよびサブスクリプションの Azure VM と API の制限の範囲内になるようにしてください。 VM はホスト プールの作成完了後にも追加できます。

8. 自分の VM で使用したい OS ディスクの種類を選択します。Standard SSD、Premium SSD、Standard HDD。

9. [Network and security]\(ネットワークとセキュリティ\) で、作成する仮想マシンの配置先となる **仮想ネットワーク** と **サブネット** を選択します。 仮想ネットワーク内の仮想マシンをドメインに参加させる必要があるため、仮想ネットワークでドメイン コントローラーに接続できることを確認します。 選択した仮想ネットワークの DNS サーバーは、ドメイン コントローラーの IP を使用するように構成する必要があります。

10. 使用するセキュリティ グループの種類を選択します ( **[基本]** 、 **[高度]** 、 **[なし]** )。

    **[基本]** を選択した場合は、受信ポートを開くかどうかを選択する必要があります。 **[はい]** を選択した場合は、受信接続を許可する標準ポートの一覧から選択を行います。

    >[!NOTE]
    >セキュリティを強化するために、パブリック受信ポートは開かないことをお勧めします。

    > [!div class="mx-imgBorder"]
    > ![ドロップダウン メニューで使用可能なポートの一覧を示すセキュリティ グループ ページのスクリーンショット。](media/available-ports.png)

    **[高度]** を選択した場合は、既に構成が済んでいる既存のネットワーク セキュリティ グループを選択します。

11. その後、**Active Directory** と **Azure Active Directory** (プレビュー) のどちらに仮想マシンを参加させるかを選択します。

    - Active Directory の場合は、ドメインに参加するためのアカウントを指定し、特定のドメインと組織単位に参加するかどうかを選択します。

        - [AD ドメイン参加 UPN] では、選択した仮想ネットワークの Active Directory ドメイン管理者の資格情報を入力します。 使用するアカウントで多要素認証 (MFA) を有効にすることはできません。 Azure Active Directory Domain Services (Azure AD DS) ドメインに参加する場合、使用するアカウントは Azure AD DC 管理者グループに属している必要があり、アカウント パスワードは Azure AD DS で機能する必要があります。

        - ドメインを指定するには、 **[はい]** を選択してから、参加するドメインの名前を入力します。 必要な場合は、完全なパス (識別名) を入力し、引用符を付けずに、仮想マシンを配置する特定の組織単位を追加することもできます。 ドメインを指定しない場合は、 **[いいえ]** を選択します。 VM は、 **[AD ドメイン参加 UPN]** のサフィックスと一致するドメインに自動的に参加します。
  
    - Azure Active Directory の場合、 **[VM を Intune に登録する]** を選択し、デプロイ後に VM を自動的に管理することができます。

12. **[仮想マシン管理者アカウント]** で、VM の作成時に追加するローカル管理者アカウントの資格情報を入力します。 このアカウントは、AD と Azure AD 参加済み VM の両方で管理目的で使用できます。

13. **[Post update custom configuration]\(更新後のカスタム構成\)** では、セッション ホストの作成後にカスタム構成を実行するための Azure Resource Manager テンプレートの場所を入力します。 Azure Resource Manager テンプレート ファイルと Azure Resource Manager テンプレート パラメーター ファイルの両方の URL を入力する必要があります。 

      >[!NOTE]
      >Azure Virtual Desktop では、テンプレートを使用して Azure リソースのプロビジョニングを行うことはできません。

14. **[Next:ワークスペース >]** を選択します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az vm create](/cli/azure/vm#az_vm_create) コマンドを使用して、新しい Azure 仮想マシンを作成します。

```azurecli
az vm create --name "MyVMName" \
    --resource-group "MyResourceGroup" \
    --image "MyImage" \
    --generate-ssh-keys
```

Azure CLI を使用した Azure 仮想マシンの作成について詳しくは、以下を参照してください。
- Windows
    - [Azure CLI を使用して Windows VM を作成する]( /azure/virtual-machines/windows/quick-create-cli)
    - [チュートリアル: Azure CLI を使用した Windows VM の作成と管理](/cli/azure/azure-cli-vm-tutorial)
- Linux
    - [Azure CLI を使用して新しく Linux VM を作成する](../virtual-machines/linux/quick-create-cli.md)
    - [チュートリアル:Azure CLI を使用した Linux VM の作成と管理]( /azure/virtual-machines/linux/tutorial-manage-vm) 
---

これで、ホスト プール設定の次の段階を開始し、アプリ グループをワークスペースに登録する準備が整いました。

## <a name="workspace-information"></a>ワークスペース情報

ホスト プールのセットアップ プロセスでは、既定でデスクトップ アプリケーション グループが作成されます。 ホスト プールを意図したとおりに機能させるには、このアプリ グループをユーザーまたはユーザー グループに発行する必要があります。また、アプリ グループをワークスペースに登録する必要があります。

>[!NOTE]
>あなたが組織のアプリを発行しようとしているアプリ開発者である場合は、MSIX アプリをユーザー セッションに動的にアタッチしたり、アプリ パッケージをカスタム VM イメージに追加したりできます。 詳細については、Azure Virtual Desktop でカスタム アプリを提供する方法を参照してください。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

デスクトップ アプリ グループをワークスペースに登録するには:

1. **[はい]** を選択します。

   **[いいえ]** を選択した場合は、後でアプリ グループを登録できますが、ホスト プールが正常に機能するように、できる限り早くワークスペースを登録することをお勧めします。

2. 次に、新しいワークスペースを作成するか、それとも既存のワークスペースから選択するかを選択します。 アプリ グループを登録できるのは、ホスト プールと同じ場所に作成されたワークスペースだけです。

3. 必要に応じて、 **[次へ:タグ >]** を選択できます。

    ここではタグを追加できます。これにより、管理者にとってわかりやすいように、メタデータを使用してオブジェクトをグループ化できます。

4. 完了したら、 **[確認および作成]** を選択します。

     >[!NOTE]
     >[確認および作成] の検証プロセスでは、パスワードがセキュリティ標準を満たしているかどうか、またアーキテクチャが正常かどうかはチェックされません。そのため、これらのいずれの点についても問題がないかご自身でチェックする必要があります。

5. ご自分のデプロイの情報を確認し、すべての内容が適切であることを確かめます。 完了したら **[作成]** を選択します。 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az desktopvirtualization workspace create](/cli/azure/desktopvirtualization#az_desktopvirtualization_workspace_create) コマンドを使用して、新しいワークスペースを作成します。

```azurecli
az desktopvirtualization workspace create --name "MyWorkspace" \
    --resource-group "MyResourceGroup" \
    --location "MyLocation" \
    --tags tag1="value1" tag2="value2" \
    --friendly-name "Friendly name of this workspace" \
    --description "Description of this workspace" 
```
---

これにより、デプロイ プロセスが開始され、次のオブジェクトが作成されます。

- 新しいホスト プール。
- デスクトップ アプリ グループ。
- ワークスペース (作成を選択した場合)。
- デスクトップ アプリ グループの登録を選択した場合は、登録が完了します。
- 仮想マシン (作成することを選択した場合)。これらはドメインに参加させられ、新しいホスト プールに登録されます。
- ご自分の構成に基づいた Azure リソース管理テンプレートのダウンロード リンク。

これですべて完了しました。

## <a name="run-the-azure-resource-manager-template-to-provision-a-new-host-pool"></a>Azure Resource Manager テンプレートを実行して新しいホスト プールをプロビジョニングする

自動プロセスを使用する場合は、代わりに [Azure Resource Manager テンプレートをダウンロードして](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates)、新しいホストプールをプロビジョニングします。

>[!NOTE]
>自動プロセスを使用して環境を構築する場合は、最新バージョンの構成 JSON ファイルが必要になります。 JSON ファイルは[こちら](https://wvdportalstorageblob.blob.core.windows.net/galleryartifacts?restype=container&comp=list)を参照してください。

## <a name="next-steps"></a>次のステップ

ホスト プールを作成できたので、次は RemoteApp プログラムを設定できます。 Azure Virtual Desktop でアプリを管理する方法について詳しく学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [アプリ グループの管理に関するチュートリアル](./manage-app-groups.md)
