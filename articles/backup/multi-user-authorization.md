---
title: Resource Guard を使用したマルチユーザー承認
description: Resource Guard を使用したマルチユーザー承認の概要。
ms.topic: how-to
ms.date: 10/20/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 02380588d4c238fe293027423969460aa0c62738
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707644"
---
# <a name="multi-user-authorization-using-resource-guard-preview"></a>Resource Guard を使用したマルチユーザー承認 (プレビュー)

Azure Backup のマルチユーザー承認 (MUA) を使用すると、Recovery Services コンテナーに対する重要な操作に保護レイヤーを追加できます。 MUA の場合、Azure Backup Resource Guard と呼ばれる別の Azure リソースを使用して、重要な操作が適用可能な承認でのみ実行される必要があります。

このドキュメントには、次が含まれます。

- Resource Guard を使用した MUA のしくみ
- 開始する前に
- テストのシナリオ
- Resource Guard を作成する
- Recovery Services コンテナーで MUA を有効にする
- コンテナーに対する不正な操作から保護する
- コンテナーに対する重要な操作を承認する
- Recovery Services コンテナーで MUA を無効にする

>[!NOTE]
>- Backup のマルチユーザー承認は現在プレビュー中であり、すべてのパブリック Azure リージョンで使用できます。
>- 2021 年 11 月 2 日より前のプレビューを使用している場合は、Azure portal の **Microsoft.RecoveryServices** プロバイダーのプレビュー機能 **AzureBackupResourceGuard** 機能を使用してサブスクリプションを登録して開始します。

## <a name="how-does-mua-for-backup-work"></a>MUA for Backup の動作

Azure Backup Recovery Services コンテナーの承認サービスとして Resource Guard を使用します。 そのため、重要な操作 (以下で説明します) を正常に実行するには、関連付けられている Resource Guard に対する十分なアクセス許可も必要です。

> [!Important]
> 意図した通り機能するには、Resource Guard が別のユーザーによって所有されている必要があります。また、コンテナー管理者に共同作成者のアクセス許可を付与することはできません。 Recovery Services コンテナーを含むサブスクリプションまたはテナントとは異なるサブスクリプションまたはテナントに Resource Guard を配置して、保護を強化できます。

### <a name="critical-operations"></a>重要な操作

次の表は、重要な操作として定義され、Resource Guard によって保護できる操作の一覧です。 コンテナーを関連付けするときに、Resource Guard を使用して特定の操作を保護から除外することができます。 [必須] として示される操作は、関連付けられているコンテナーの Resource Guard を使用して保護から除外できないことに注意してください。 また、除外された重要な操作は、Resource Guard に関連付けられているすべてのコンテナーに適用されます。

**操作** | **必須/省略可能**
--- | ---
論理的な削除の無効化 | Mandatory
MUA 保護を無効にする | Mandatory
バックアップ ポリシーの変更 | 省略可能: 除外できます
保護を変更する | 省略可能: 除外できます
保護の停止 | 省略可能: 除外できます
MARS セキュリティ PIN を変更する | 省略可能: 除外できます

### <a name="concepts"></a>概念
以下では、MUA for Backup を使用する場合の概念とプロセスについて説明します。

プロセスと責任を明確に理解するために、次の 2 人のユーザーを検討しましょう。 この記事では、これら 2 つのロールについて参照します。

**バックアップ管理者**: Recovery Services コンテナーの所有者であり、コンテナーに対して管理操作を実行します。 まず、バックアップ管理者は Resource Guard に対するアクセス許可を持つことはできません。

**セキュリティ管理者**: Resource Guard の所有者であり、コンテナーに対する重要な操作のゲートキーパーとして機能します。 そのため、セキュリティ管理者は、バックアップ管理者がコンテナーに対して重要な操作を実行するために必要なアクセス許可を制御します。

Resource Guard を使用して MUA が構成されているコンテナーで重要な操作を実行するための図形式の表現を次に示します。

:::image type="content" source="./media/multi-user-authorization/configure-mua-using-resource-card-diagram.png" alt-text="Resource Guard を使用した MUA の構成に関する図形式の表現。":::
 

一般的なシナリオでのイベントのフローを次に示します。

1. バックアップ管理者は Recovery Services コンテナーを作成します。
1. セキュリティ管理者が Resource Guard を作成します。 Resource Guard は、Recovery Services コンテナーとは異なるサブスクリプションまたは別のテナントに格納できます。 バックアップ管理者に Resource Guard に対する共同作成者アクセス許可が付与されていない必要があります。
1. セキュリティ管理者は、Resource Guard (または関連スコープ) のバックアップ管理者に **閲覧者** ロールを付与します。 バックアップ管理者は、コンテナーで MUA を有効にするには、閲覧者ロールが必要です。
1. これで、バックアップ管理者は、Resource Guard を介して MUA によって保護される Recovery Services コンテナーを構成します。
1. バックアップ管理者がコンテナーに対して重要な操作を実行する場合は、Resource Guard へのアクセスを要求する必要があります。 バックアップ管理者は、セキュリティ管理者に連絡して、このような操作を実行するためのアクセス権の取得の詳細を確認できます。 これは、Azure Active Directory Privileged Identity Management (PIM) または組織が要求する他のプロセスを使用して行います。
1. セキュリティ管理者は、重要な操作を実行するために、Resource Guard の **共同作成者** ロールをバックアップ管理者に一時的に付与します。
1. これで、バックアップ管理者が重要な操作を開始します。
1. Azure Resource Manager は、バックアップ管理者に十分なアクセス許可があるかどうかを確認します。 これで、バックアップ管理者が Resource Guard の共同作成者ロールを持つたび、要求は完了します。
   - バックアップ管理者に必要なアクセス許可/ロールが与えなかった場合、要求は失敗します。
1. セキュリティ管理者は、承認されたアクションが実行された後、または定義された期間後に、重要な操作を実行するための特権が取り消されます。 JIT ツール [Azure Active Directory Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) の使用が、これを確実に行うのに役立つ場合があります。

>[!NOTE]
>- MUA は、Recovery Services コンテナーに対して実行された上記の操作に対する保護を提供します。 データ ソースに対して直接実行された操作 (つまり、保護されている Azure リソース/ワークロード) は、Resource Guard の範囲を超えます。 
>- 現在この機能は、Azure portal を介してのみ使用できます。
>- この機能は現在、Recovery Services コンテナーでのみサポートされ、バックアップ コンテナーでは使用できません。

## <a name="before-you-start"></a>開始する前に

-  Resource Guard と Recovery Services コンテナーは、同じ Azure リージョンに含む必要があります。
-  前のセクションで説明したように、バックアップ管理者に Resource Guard に対する **共同作成者** のアクセス許可が付与されて **いない** 必要があります。 Resource Guard を同じディレクトリの別のサブスクリプションまたは別のディレクトリに含め、最大限の分離を確保することができます。

## <a name="usage-scenarios"></a>使用シナリオ

次の表は、Resource Guard と Recovery Services コンテナー (RS コンテナー) を作成するシナリオと、それぞれによって提供される相対的な保護を示しています。

>[!Important]
> どのシナリオでも、バックアップ管理者に Resource Guard に対する共同作成者のアクセス許可を持つ必要はありません。

**使用シナリオ** | **MUA による保護** | **実装の容易さ** | **ノート**
--- | --- |--- |--- |
RS コンテナーと Resource Guard は **同じサブスクリプション内** にあります。 </br> バックアップ管理者は Resource Guard へのアクセス権を持ちません。 | バックアップ管理者とセキュリティ管理者の間に最低限の分離が設けられます。 | 1 つのサブスクリプションだけが必要なので、比較的簡単に実装できます。 | リソース レベルのアクセス許可/ロールが正しく割り当てられている必要があります。
RS コンテナーと Resource Guard は **異なるサブスクリプションにあるが、同じテナント内** にあります。 </br> バックアップ管理者は、Resource Guard または対応するサブスクリプションへのアクセス権を持ちません。 | バックアップ管理者とセキュリティ管理者の間の中程度の分離が設けられます。 | 2 つのサブスクリプション (1 つのテナントを含む) が必要なので、比較的中程度の実装が容易です。 | リソースまたはサブスクリプションに対してアクセス許可/ロールが正しく割り当てられている必要があります。
RS コンテナーと Resource Guard は、**異なるテナント内** にあります。 </br> バックアップ管理者は、Resource Guard、対応するサブスクリプション、または対応するテナントへのアクセス権を持ちません。| バックアップ管理者とセキュリティ管理者の間の最大分離が設けられます。そのため、セキュリティは最大です。 | テストには 2 つのテナントまたはディレクトリが必要です。 | リソース、サブスクリプション、またはディレクトリに対してアクセス許可またはロールが正しく割り当てられている必要があります。

 >[!NOTE]
 > この記事では、最大限の保護を提供する別のテナントでの Resource Guard の作成について説明します。 重要な操作を実行するための要求の要求と承認に関して、この記事では、Resource Guard を収容するテナントで [Azure Active Directory Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) を使用して同じことを示します。 必要に応じて、他のメカニズムを使用して、設定に従って Resource Guard に対する JIT アクセス許可を管理できます。

## <a name="creating-a-resource-guard"></a>リソース グループを作成する

**セキュリティ管理者** が Resource Guard を作成します。 コンテナーとして **別のサブスクリプション** または **別のテナント** に作成することをお勧めします。 ただし、コンテナーと **同じリージョン** に存在する必要があります。 バックアップ管理者は、Resource Guard またはそれを含むサブスクリプションに対する *共同作成者* アクセス権を **持たない** 必要があります。

次の例では、コンテナー テナントとは異なるテナントに Resource Guard を作成します。
1. Azure portal で、Resource Guard を作成するディレクトリに移動します。
   
   :::image type="content" source="./media/multi-user-authorization/portal-settings-directories-subscriptions.png" alt-text="ポータルの設定を示すスクリーンショット。":::
     
1. 検索バーで **Resource Guard** を検索し、ドロップダウンから対応する項目を選択します。
    
   :::image type="content" source="./media/multi-user-authorization/resource-guards-preview-inline.png" alt-text="プレビューのリソース ガードを示すスクリーンショット。" lightbox="./media/multi-user-authorization/resource-guards-preview-expanded.png":::
    
   - **[作成]** をクリックして、Resource Guard の作成を開始します。
   - [作成] ブレードで、この Resource Guard に必要な詳細を入力します。
       - Resource Guard が Recovery Services コンテナーと同じ Azure リージョン内にあることを確認します。
       - また、必要に応じて、関連付けられているコンテナーに対してアクションを実行するためにアクセスを取得または要求する方法の説明を追加すると便利です。 この説明は、バックアップ管理者が必要なアクセス許可を取得するためのガイドとして、関連付けられているコンテナーにも表示されます。 必要に応じて後で説明を編集できますが、常によく定義された説明を使用する方が推奨されます。
       
        :::image type="content" source="./media/multi-user-authorization/create-resource-guard.png" alt-text="Resource Guard の作成を示すスクリーンショット。":::
                
1. 必要に応じて、要件に従って Resource Guard にタグを追加します
1. **[確認と作成]** をクリックします
1. 状態と Resource Guard の正常な作成に関する通知に従います。

### <a name="select-operations-to-protect-using-resource-guard"></a>Resource Guard を使用して保護する操作を選択する

サポートされている重要な操作の中から、Resource Guard を使用して保護する操作を選択します。 既定では、サポートされている重要な操作はすべて有効になっています。 ただし、Resource Guard を使用して、特定の操作が MUA のビューに入るのを除外できます。 セキュリティ管理者は、次の手順を実行できます。

1. 上で作成した Resource Guard で、 **[プロパティ]** に移動します。
2. Resource Guard を使用して承認から除外する操作の場合は、 **[無効にする]** を選択します。 **[Disable soft delete]/(ソフト削除を無効にする)** 、および **[Remove MUA protection]/(MUA 保護の削除)** 操作は無効にできないことに注意してください。
3. 必要に応じて、このブレードを使用して Resource Guard の説明を更新できます。 
4. **[保存]** をクリックします。

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-properties.png" alt-text="デモ リソース ガードのプロパティを示すスクリーンショット。":::

## <a name="assign-permissions-to-the-backup-admin-on-the-resource-guard-to-enable-mua"></a>Resource Guard のバックアップ管理者にアクセス許可を割り当て、MUA を有効にする

コンテナーで MUA を有効にするには、コンテナーの管理者が Resource Guard または Resource Guard を含むサブスクリプションに対する **閲覧者** ロールを持っている必要があります。 Resource Guard に **閲覧者** ロールを割り当てるには、次の操作を実行します。

1. 上で作成した Resource Guard で、[アクセス制御 (IAM)] ブレードに移動し、 **[ロールの割り当ての追加]** に移動します。

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-access-control.png" alt-text="デモ リソースのガードアクセス制御を示すスクリーンショット。":::
    
1. 組み込みロールの一覧から **[閲覧者]** を選択し、画面の下部にある **[次へ]** をクリックします。

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-add-role-assignment-inline.png" alt-text="デモ リソース ガードの追加ロールの割り当てを示すスクリーンショット。" lightbox="./media/multi-user-authorization/demo-resource-guard-add-role-assignment-expanded.png":::

1. **[メンバーの選択]** をクリックし、バックアップ管理者の電子メール ID を追加して、それらを **閲覧者** として追加します。 この場合、バックアップ管理者は別のテナントにあるので、Resource Guard を含むテナントにゲストとして追加されます。

1. **[選択]** をクリックし、 **[レビューと割り当て]** に進み、ロールの割り当てを完了します。

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-select-members-inline.png" alt-text="デモ リソース ガードのメンバーの選択を示すスクリーンショット。" lightbox="./media/multi-user-authorization/demo-resource-guard-select-members-expanded.png":::

## <a name="enable-mua-on-a-recovery-services-vault"></a>Recovery Services コンテナーで MUA を有効にする

これで、バックアップ管理者は Resource Guard の閲覧者ロールを持ち、これらの管理者が管理するコンテナーに対するマルチユーザー承認を簡単に有効にできるようになります。 次の手順は、**バックアップ管理者** が実行します。

1. [Recovery Services コンテナー] に移動します。 左側のナビゲーション パネルで **[プロパティ]** に移動し、 **[マルチユーザー認可]** に移動し、 **[更新]** をクリックします。

1. これで、MUA を有効にし、次のいずれかの方法を使用して、Resource Guard を選択するオプションが表示されます。

   1. Resource Guard の URI を指定するか、**閲覧者** アクセス権を持ち、コンテナーと同じリージョンである Resource Guard の URI を指定していることを確認します。 Resource Guard の URI (Resource Guard ID) は、その **[概要]** 画面で確認できます。

      :::image type="content" source="./media/multi-user-authorization/resource-guard-rg-inline.png" alt-text="Resource Guard を示すスクリーンショット。" lightbox="./media/multi-user-authorization/resource-guard-rg-expanded.png":::
    
   1. または、**閲覧者** がアクセスできる Resource Guard の一覧と、リージョンで使用できるリソース ガードの一覧から Resource Guard を選択できます。

      1. **[リソース ガードの選択]** をクリックします。
      1. ドロップダウンをクリックし、Resource Guard が存在するディレクトリを選択します。
      1. **[認証]** をクリックして、ID とアクセスを検証します。
      1. 認証後、表示される一覧から **Resource Guard** を選択します。

      :::image type="content" source="./media/multi-user-authorization/testvault1-multi-user-authorization-inline.png" alt-text="[マルチユーザー認可] を示すスクリーンショット。" lightbox="./media/multi-user-authorization/testvault1-multi-user-authorization-expanded.png" :::

1. 終わったら **[保存]** をクリックして MUA を有効にします。

   :::image type="content" source="./media/multi-user-authorization/testvault1-enable-mua.png" alt-text="マルチユーザー認証を有効にする方法を示すスクリーンショット。":::

## <a name="protect-against-unauthorized-protected-operations"></a>承認されていない (保護された) 操作から保護する

MUA を有効にすると、バックアップ管理者が Resource Guard に必要なロール (共同作成者ロール) を持たなくても実行しようとすると、スコープ内の操作がコンテナーで制限されます。

 >[!NOTE]
 >MUA を有効にした後で設定をテストして、保護された操作が想定通りブロックされていることを確認し、MUA が正しく構成されていることを確認することを強くお勧めします。

次に示すのは、バックアップ管理者がこのような保護された操作を実行しようとした場合の動作を示しています (たとえば、ソフト削除を無効にする方法を次に示します)。 その他の保護された操作も同様のエクスペリエンスを持っています)。 次の手順は、必要なアクセス許可なしでバックアップ管理者によって実行されます。

1. ソフト削除を無効にするには、[Recovery Services コンテナー] > [プロパティ] > [セキュリティ設定] に移動し、 **[更新]** をクリックして [セキュリティ設定] を表示します。
1. スライダーを使用して、ソフト削除を無効にします。 これは保護された操作であり、Resource Guard へのアクセスを確認する必要があります。
1. Resource Guard を含むディレクトリを選択し、自分で認証します。 Resource Guard がコンテナーと同じディレクトリにある場合、この手順は必要ありません。
1. **[保存]** のクリックに進みます。 要求が失敗し、Resource Guard に対してこの操作を実行するための十分なアクセス許可がないというエラーが表示されます。

## <a name="authorize-critical-protected-operations-using-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management を使用して重要な (保護された) 操作を承認する

次のサブセクションでは、PIM を使用したこれらの要求の承認について説明します。 バックアップに対して重要な操作を実行する必要がある場合があります。MUA は、適切な承認またはアクセス許可が存在する場合にのみ実行されるのに役立ちます。 前に説明したように、バックアップ管理者は、Resource Guard スコープ内の重要な操作を実行するために、Resource Guard に対する共同作成者ロールを持っている必要があります。 このような操作に Just-In-Time を許可する方法の 1 つは、[Azure Active Directory (Azure AD) Privileged IDentity Management](../active-directory/privileged-identity-management/pim-configure.md) を使用することです。

>[!NOTE]
> PIM をAzure AD推奨される方法ですが、手動またはカスタムの方法を使用して、Resource Guard のバックアップ管理者のアクセスを管理できます。 Resource Guard へのアクセスを手動で管理するには、Resource Guard の左側のナビゲーション バーにある [アクセス制御 (IAM)] 設定を使用し、バックアップ管理者に **共同作成者** ロールを付与します。

### <a name="create-an-eligible-assignment-for-the-backup-admin-if-using-azure-ad-privileged-identity-management"></a>バックアップ管理者の資格を持つ割り当てを作成する (Azure AD Privileged Identity Management を使用する場合)

PIM を使用すると、セキュリティ管理者は、Resource Guard の共同作成者としてバックアップ管理者の有資格の割り当てを作成できます。 これにより、バックアップ管理者は、保護された操作を実行する必要があるときに (共同作成者ロールに対して) 要求を発生できます。 これを行うには、**セキュリティ管理者** が次の操作を実行します。

1. セキュリティ テナント (Resource Guard が含まれている) で、**Privileged IDentity Management** に移動し (Azure portal の検索バーでこれを検索)、**Azure リソース** (左側のメニューの **[管理]** の下) に移動します。
1. **共同作成者** ロールを割り当てるリソース (Resource Guard または含まれているサブスクリプション/RG) を選択します。

   1. リソースの一覧に対応するリソースが表示しない場合は、PIM によって管理される含まれているサブスクリプションを追加してください。

1. 選択したリソースで、 **[割り当て]** (左側のメニューの **[管理]** の下) に移動し、 **[割り当ての追加]** に移動します。

    :::image type="content" source="./media/multi-user-authorization/add-assignments.png" alt-text="ロールの割り当ての追加方法を示すスクリーンショット。":::

1. [割り当ての追加] 内
   1. 共同作成者としてロールを選択します。
   1. [メンバーの選択] に移動し、バックアップ管理者のユーザー名 (または電子メールの ID) を追加します
   1. [Next]\(次へ\) をクリックします

   :::image type="content" source="./media/multi-user-authorization/add-assignments-membership.png" alt-text="割り当てメンバーシップを追加する方法を示すスクリーンショット。":::

1. 次の画面
   1. [割り当ての種類] で、 **[対象]** を選択します。
   1. 対象となるアクセス許可が有効な期間を指定します。
   1. **[割り当て]** をクリックして、対象の割り当ての作成を終了します。

   :::image type="content" source="./media/multi-user-authorization/add-assignments-setting.png" alt-text="割り当て設定の追加方法を示すスクリーンショット。":::

### <a name="set-up-approvers-for-activating-contributor-role"></a>共同作成者ロールをアクティブ化する承認者を設定する

既定では、上記の設定では、PIM で承認者 (および承認フローの要件) が構成されていない可能性があります。 承認された要求のみを通過するために承認者が必要な場合は、セキュリティ管理者が次の手順を実行する必要があります。
これが構成されていない場合は、セキュリティ管理者や指定された承認者のレビューを行わずに、すべての要求が自動的に承認されます。 これに関する詳細については、[こちら](../active-directory/privileged-identity-management/pim-resource-roles-configure-role-settings.md)を参照してください。

1. Azure AD PIM で、左側のナビゲーション バーの **[Azure リソース]** を選択し、Resource Guard を選択します。

1. **[設定]** に移動し、**共同作成者** ロールに移動します。

   :::image type="content" source="./media/multi-user-authorization/add-contributor.png" alt-text="共同作成者を追加する方法を示すスクリーンショット。":::

1. **[承認者]** という名前の設定に [なし] が表示される場合、または不適切な承認者が表示される場合は、 **[編集]** をクリックして、共同作成者ロールのアクティブ化要求を確認して承認する必要があるレビュー担当者を追加します。

1. **[アクティブ化]** タブで、 **[アクティブ化の承認を必須にする]** を選択して、各要求を承認する必要がある承認者を追加します。 MFA の使用やチケット オプションの要求など、他のセキュリティ オプションを選択して共同作成者ロールをアクティブにできます。 必要に応じて、要件に従って **[割り当て]** タブと **[通知]** タブで関連する設定を選択します。

   :::image type="content" source="./media/multi-user-authorization/edit-role-settings.png" alt-text="ロール設定の編集方法を示すスクリーンショット。":::

1. 終了したら、 **[更新]** をクリックします。

### <a name="request-activation-of-an-eligible-assignment-to-perform-critical-operations"></a>重要な操作を実行するために適格な割り当てのアクティブ化を要求する

セキュリティ管理者が適格な割り当てを作成した後、バックアップ管理者は、保護されたアクションを実行するために共同作成者ロールの割り当てをアクティブにする必要があります。 次のアクションは、ロールの割り当てをアクティブ化するために **バックアップ管理者** によって実行されます。

1. [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) に移動します。 Resource Guard が別のディレクトリにある場合は、そのディレクトリに切り替えてから、[Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) に移動します。
1. 左側のメニューの [自分のロール] > [Azure リソース] に移動します。
1. バックアップ管理者は、共同作成者ロールの対象となる割り当てを確認できます。 **[アクティブにする]** をクリックして、有効にします。
1. バックアップ管理者は、承認のために要求が送信されたというポータル通知を介して通知されます。

   :::image type="content" source="./media/multi-user-authorization/identity-management-myroles-inline.png" alt-text="対象となる割り当てのアクティブ化を示すスクリーンショット。" lightbox="./media/multi-user-authorization/identity-management-myroles-expanded.png":::

### <a name="approve-activation-of-requests-to-perform-critical-operations"></a>重要な操作を実行する要求のアクティブ化を承認する

バックアップ管理者が共同作成者ロールをアクティブ化する要求を送信すると、その要求は **セキュリティ管理者** によって確認および承認されます。
1. セキュリティ テナントで、[Azure AD Privileged Identity Management.](../active-directory/privileged-identity-management/pim-configure.md) に移動します。
1. **[要求の承認]** に移動します。
1. **[Azure リソース]** の下に、**共同作成者** としてアクティブ化を要求するバックアップ管理者によって発生した要求が表示されます。
1. 要求をレビューします。 そうであれば、要求を選択して **[承認]** をクリックして承認します。
1. バックアップ管理者は、要求が承認されたという電子メール (または他の組織のアラート メカニズム) によって通知されます。
1. 承認されると、バックアップ管理者は、要求された期間に対して保護された操作を実行できます。

## <a name="performing-a-protected-operation-after-approval"></a>承認後の保護された操作の実行

Resource Guard の共同作成者ロールに対するバックアップ管理者の要求が承認されると、関連付けられているコンテナーに対して保護された操作を実行できます。 Resource Guard が別のディレクトリにある場合、バックアップ管理者は自身を認証する必要があります。

>[!NOTE]
> アクセスが JIT メカニズムを使用して割り当てられた場合、共同作成者ロールは承認された期間の終わりに取り消されます。 それ以外の場合、セキュリティ管理者は、重要な操作を実行するために、バックアップ管理者に割り当てられている **共同作成者** ロールを手動で削除します。

## <a name="disable-mua-on-a-recovery-services-vault"></a>Recovery Services コンテナーで MUA を無効にする

MUA の無効化は保護された操作であるため、MUA を使用して保護されます。 つまり、バックアップ管理者は Resource Guard で必要な共同作成者ロールを持っている必要があります。 このロールの取得の詳細については、こちらを参照してください。 コンテナーで MUA を無効にする手順の概要を次に示します。
1. バックアップ管理者は、Resource Guard の **共同作成者** ロールにセキュリティ管理者を要求します。 これは、組織によって承認されたメソッド (JIT プロシージャなど) や、[Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)、またはその他の内部ツールやプロシージャを使用して要求できます。 
1. セキュリティ管理者は要求を承認し (承認される価値がある場合)、バックアップ管理者に通知します。これで、バックアップ管理者が Resource Guard に対する "共同作成者" ロールを持つ。
1. バックアップ管理者は、コンテナー >[プロパティ] > [マルチユーザー認可] に移動します
1. **[Update]\(更新\)** をクリックします。
   1. [Resource Guard で保護する] チェック ボックスをオフにします
   1. Resource Guard を含むディレクトリを選択し、[認証] ボタン (該当する場合) を使用してアクセスを確認します。
   1. **認証** 後、 **[保存]** をクリックします。 適切なアクセス権を持つ場合、要求は正常に完了する必要があります。