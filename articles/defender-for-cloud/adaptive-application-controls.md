---
title: Microsoft Defender for Cloud での適応型アプリケーション制御
description: このドキュメントは、Microsoft Defender for Cloud で適応型アプリケーション制御を使用して、Azure マシンで実行されているアプリケーションの許可リストを作成するのに役立ちます。
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 717ef750a6948a7e3d97a304d79869249346dc93
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525572"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>適応型アプリケーション制御を使用して、マシンの攻撃対象領域を減らす

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud の適応型アプリケーション制御の利点と、このデータ駆動型のインテリジェント機能を使用してセキュリティを強化する方法について説明します。

## <a name="what-are-adaptive-application-controls"></a>適応型アプリケーション制御とは

適応型アプリケーション制御は、マシンの既知の安全なアプリケーションの許可リストを定義するためのインテリジェントで自動化されたソリューションです。 

多くの場合、組織には、同じプロセスを定期的に実行するマシンのコレクションがあります。 Microsoft Defender for Cloud によって機械学習が使用され、マシン上で実行されているアプリケーションが分析され、既知の安全なソフトウェアのリストが作成されます。 許可リストは特定の Azure ワークロードに基づいています。以下の手順を使用して、推奨事項をさらにカスタマイズすることができます。

適応型アプリケーション制御を有効にして構成した場合、安全なものとして定義したもの以外のアプリケーションが実行されると、セキュリティ アラートが表示されます。


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>適応型アプリケーション制御の利点とは

既知の安全なアプリケーションのリストを定義し、それ以外のものが実行されたときにアラートを生成すると、次のような複数の監視とコンプライアンスの目標を達成できます。

- 潜在的なマルウェア (マルウェア対策ソリューションでは見逃される可能性のあるものを含む) を識別する
- ライセンスのあるソフトウェアのみの使用を規定するローカル セキュリティ ポリシーのコンプライアンスを強化する
- 古くなったかサポートされていないバージョンのアプリケーションを特定する 
- 組織によって禁止されているが、コンピューターで実行されているソフトウェアを特定する
- 機密データにアクセスするアプリの監視を強化する

現時点では、利用できる強制オプションはありません。 適応型アプリケーション制御では、安全なものとして定義したもの以外のアプリケーションが実行されると、セキュリティ アラートが表示されることを想定しています。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|[Microsoft Defender for servers](defender-for-servers-introduction.md) が必要|
|サポートされているマシン:|:::image type="icon" source="./media/icons/yes-icon.png"::: Windows および Linux が実行されている Azure および Azure 以外のマシン<br>:::image type="icon" source="./media/icons/yes-icon.png"::: [Azure Arc](../azure-arc/index.yml) マシン|
|必要なロールとアクセス許可:|**セキュリティ閲覧者** および **閲覧者** のいずれのロールでも、グループと、既知の安全なアプリケーションのリストを表示できます<br>**共同作成者** および **セキュリティ管理者** のいずれのロールでも、グループと、既知の安全なアプリケーションのリストを編集できます|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 各国 (Azure Government、Azure China 21Vianet)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>マシンのグループに対してアプリケーション制御を有効にする

Microsoft Defender for Cloud で、同じような一連のアプリケーションを一貫して実行するサブスクリプション内のマシンのグループが特定されている場合は、次の推奨事項が表示されます。**安全なアプリケーションを定義するための適応型アプリケーション制御をマシンで有効にする必要 があります**。

推奨事項を選択するか、適応型アプリケーション制御ページを開いて、推奨される既知の安全なアプリケーションのリストとマシンのグループを表示します。

1. ワークロード保護ダッシュボードを開き、高度な保護領域から **適応型アプリケーション制御** を選択します。

    :::image type="content" source="./media/adaptive-application/opening-adaptive-application-control.png" alt-text="Azure ダッシュボードから適応型アプリケーション制御を開きます。" lightbox="./media/adaptive-application/opening-adaptive-application-control.png":::

    **[適応型アプリケーション制御]** ページが開き、VM が次のタブにグループ化されます。

    - **構成済み** - アプリケーションの許可リストが既に定義されているマシンのグループ。 グループごとに、[構成済み] タブには以下の内容が示されます。
        - グループ内のマシンの数
        - 最近のアラート

    - **推奨** - 同じアプリケーションを一貫して実行し、許可リストが構成されていないマシンのグループ。 これらのグループに対して適応型アプリケーション制御を有効にすることをお勧めします。
    
      > [!TIP]
      > "REVIEWGROUP" というプレフィックスが付いたグループ名が表示される場合は、部分的に一貫性のあるアプリケーションのリストがあるマシンが含まれます。 Microsoft Defender for Cloudでパターンは表示できませんが、このグループを参照し、「[グループの適応型アプリケーション制御規則の編集](#edit-a-groups-adaptive-application-controls-rule)」の説明に従って、いくつかの適応型アプリケーション制御規則を "_お客様_" が手動で定義できるかどうかを確認することが推奨されます。
      >
      > 「[グループ間でマシンを移動する](#move-a-machine-from-one-group-to-another)」の説明に従って、このグループから他のグループにマシンを移動することもできます。

    - **推奨なし** - アプリケーションの許可リストが定義されておらず、機能がサポートされていないマシン。 マシンは次の理由により、このタブに表示される場合があります。
      - Log Analytics エージェントが欠落している
      - Log Analytics エージェントによってイベントが送信されていない
      - GPO またはローカル セキュリティ ポリシーによって既存の [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) ポリシーが有効になっている Windows マシンである

      > [!TIP]
      > Defender for Cloudは、マシンのグループごとに固有の推奨事項を定義するために、少なくとも2週間のデータを必要とします。 最近作成されたか、Microsoft Defenderでのみ最近有効にされたサブスクリプションに属しているマシンは、 **[推奨なし]** タブの下に表示されます。

1. **[推奨]** タブを開きます。推奨される許可リストがあるマシンのグループが表示されます。

   ![[推奨] タブ。](./media/adaptive-application/adaptive-application-recommended-tab.png)

1. グループを選びます。 

1. 新しい規則を構成するには、この **[アプリケーションの制御に関する規則の構成]** ページのさまざまなセクションとその内容を確認します。これは、特定のマシンのグループに固有のものです。

   ![新しい規則を構成します。](./media/adaptive-application/adaptive-application-create-rule.png)

   1. **マシンの選択** - 既定では、識別されたグループ内のすべてのマシンが選択されます。 いずれかの選択を解除すると、それらはこの規則から除外されます。
   
   1. **推奨アプリケーション** - このグループ内のマシンに共通し、実行を許可することが推奨されるアプリケーションのこのリストを確認します。
   
   1. **その他のアプリケーション** - このグループ内のマシンでの表示頻度が低いか、あるいは利用可能であることがわかっているアプリケーションのこのリストを確認します。 警告アイコンは、アプリケーションの許可リストをバイパスするために、特定のアプリケーションが攻撃者によって使用される可能性があることを示します。 これらのアプリケーションを慎重に確認することをお勧めします。

      > [!TIP]
      > どちらのアプリケーション リストにも、特定のアプリケーションを特定のユーザーに制限するオプションが含まれています。 可能な限り、最小限の特権の原則を採用してください。
      > 
      > アプリケーションは、発行元によって定義されます。アプリケーションに発行元情報がない (署名されていない) 場合、特定のアプリケーションの完全なパスに対してパス規則が作成されます。

   1. 規則を適用するには、 **[監査]** を選択します。 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>グループの適応型アプリケーション制御規則の編集

組織内の既知の変更により、マシンのグループの許可リストを編集することができます。 

マシン グループの規則を編集するには、次のようにします。

1. **ワークロード保護ダッシュボード** を開き、高度な保護領域から **適応型アプリケーション制御** を選択します。

1. **[構成済み]** タブから、規則を編集するグループを選択します。

1. [マシンのグループに対する適応型アプリケーション制御の有効化](#enable-application-controls-on-a-group-of-machines)に関する説明に従って、 **[アプリケーションの制御に関する規則の構成]** ページのさまざまなセクションを確認します。

1. 必要に応じて、1 つまたは複数のカスタム規則を追加します。

   1. **[規則の追加]** を選択します。

      ![カスタム規則を追加します。](./media/adaptive-application/adaptive-application-add-custom-rule.png)

   1. 既知の安全なパスを定義する場合は、 **[規則の種類]** を [パス] に変更して、1 つのパスを入力します。 パスにはワイルドカードを含めることができます。
   
      > [!TIP]
      > パスのワイルドカードが役立つ場合があるシナリオには、次のようなものがあります。
      > 
      > * パスの末尾でワイルドカードを使用し、そのフォルダーとサブフォルダー内のすべての実行可能ファイルを許可します。
      > * パスの途中でワイルドカードを使用し、変化するフォルダー名を持つ既知の実行可能ファイルの名前 (既知の実行可能ファイルを含む個人ユーザー フォルダーや自動生成されたフォルダー名など) を有効にします。
  
   1. 許可されるユーザーと保護されるファイルの種類を定義します。

   1. 規則の定義が完了したら、 **[追加]** を選択します。

1. 変更を適用するには、 **[保存]** を選択します。


## <a name="review-and-edit-a-groups-settings"></a>グループの設定を確認して編集する

1. グループの詳細と設定を表示するには、 **[グループ設定]** を選択します

    このウィンドウには、グループの名前 (変更可能)、OS の種類、場所、およびその他の関連する詳細が表示されます。

    :::image type="content" source="./media/adaptive-application/adaptive-application-group-settings.png" alt-text="適応型アプリケーション制御の [グループ設定] ページ。" lightbox="./media/adaptive-application/adaptive-application-group-settings.png":::

1. 必要に応じて、グループの名前やファイルの種類の保護モードを変更します。

1. **[適用]** を選択し、 **[保存]** を選択します。



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>[適応型アプリケーション制御ポリシーの許可リスト ルールを更新する必要がある] 推奨事項への対応

Defender for Cloudの機械学習で、以前は許可されていなかった、正当である可能性のある動作が識別された場合、この推奨事項が表示されます。 誤検知アラートの数を減らすために、推奨事項で既存の定義の新しい規則が提案されます。

問題を修復するには、次のようにします。

1. 推奨事項ページから、 **[適応型アプリケーション制御ポリシーの許可リスト ルールを更新する必要がある]** 推奨事項を選択し、新しく識別された、正当である可能性のある動作のグループを表示します。

1. 規則を編集するグループを選択します。

1. [マシンのグループに対する適応型アプリケーション制御の有効化](#enable-application-controls-on-a-group-of-machines)に関する説明に従って、 **[アプリケーションの制御に関する規則の構成]** ページのさまざまなセクションを確認します。

1. 変更を適用するには、 **[監査]** を選択します。




## <a name="audit-alerts-and-violations"></a>アラートと違反の監査

1. **ワークロード保護ダッシュボード** を開き、高度な保護領域から **適応型アプリケーション制御** を選択します。

1. 最近のアラートが示されたマシンのグループを表示するには、 **[構成済み]** タブに一覧表示されているグループを確認します。

1. さらに調査するには、グループを選択します。

   ![最近のアラート。](./media/adaptive-application/recent-alerts.png)

1. 詳細および影響を受けるマシンのリストを表示するには、アラートを選択します。

    アラート ページには、アラートの詳細が表示され、脅威を軽減するための推奨事項と共に **[アクションの実行]** リンクが表示されます。

    :::image type="content" source="media/adaptive-application/adaptive-application-alerts-start-time.png" alt-text="適応型アプリケーション制御アラートの開始時刻は、適応型アプリケーション制御によってアラートが作成された時刻です。":::

    > [!NOTE]
    > 適応型アプリケーション制御では、12 時間ごとにイベントが計算されます。 アラート ページに表示される [アクティビティの開始時刻] は、疑わしいプロセスがアクティブだった時間では **なく**、適応型アプリケーション制御によってアラートが作成された時刻です。


## <a name="move-a-machine-from-one-group-to-another"></a>グループ間でマシンを移動する

グループ間でマシンを移動すると、適用されていたアプリケーション制御ポリシーが移動先のグループの設定に変更されます。 構成されたグループから構成されていないグループにマシンを移動することもできます。これにより、マシンに適用されていたアプリケーション制御規則がすべて削除されます。

1. **ワークロード保護ダッシュボード** を開き、高度な保護領域から **適応型アプリケーション制御** を選択します。

1. **[適応型アプリケーション制御]** ページの **[構成済み]** タブから、移動対象のマシンを含むグループを選択します。

1. **[構成されたマシン]** のリストを開きます。

1. 行の末尾にある 3 つの点をクリックしてマシンのメニューを開き、 **[移動]** を選択します。 **[Move machine to a different group]\(マシンを別のグループに移動する\)** ペインが開きます。

1. 移動先グループを選び、 **[Move machine]\(マシンを移動\)** を選択します。

1. 変更を保存するには、 **[保存]** を選択します。





## <a name="manage-application-controls-via-the-rest-api"></a>REST API を使用したアプリケーション制御の管理 

適応型アプリケーション制御をプログラムで管理するには、REST API を使用します。 

関連する API ドキュメントは、[Defender for Cloud の API ドキュメントの「適応型アプリケーション制御」セクション](/rest/api/securitycenter/adaptiveapplicationcontrols)で参照できます。

REST API から使用できる関数をいくつか以下に示します。

* **List** を使用すると、すべてのグループの推奨事項が取得され、各グループのオブジェクトを含む JSON が提供されます。

* **Get** を使用すると、すべての推奨データ (つまり、マシン、発行元またはパスの規則のリスト) を含む JSON が取得されます。

* **Put** を使用すると、規則が構成されます (この要求の本文として、**Get** で取得した JSON を使用)。
 
   > [!IMPORTANT]
   > **Put** 関数には、Get コマンドによって返される JSON に含まれるものより少ない数のパラメーターが必要です。
   >
   > Put 要求で JSON を使用する前に、recommendationStatus、configurationStatus、issues、location、sourceSystem の各プロパティを削除してください。


## <a name="faq---adaptive-application-controls"></a>FAQ - 適応型アプリケーション制御

- [アプリケーション制御を強制するためのオプションはありますか。](#are-there-any-options-to-enforce-the-application-controls)
- [お勧めアプリケーションに Qualys アプリが表示されるのはなぜですか?](#why-do-i-see-a-qualys-app-in-my-recommended-applications)

### <a name="are-there-any-options-to-enforce-the-application-controls"></a>アプリケーション制御を強制するためのオプションはありますか。
現時点では、利用できる強制オプションはありません。 適応型アプリケーション制御では、安全なものとして定義したもの以外のアプリケーションが実行されると、**セキュリティ アラート** が表示されることを想定しています。 これらにはさまざまな利点があり (「[適応型アプリケーション制御の利点とは](#what-are-the-benefits-of-adaptive-application-controls)」)、このページに示すように高度なカスタマイズが可能です。

### <a name="why-do-i-see-a-qualys-app-in-my-recommended-applications"></a>お勧めアプリケーションに Qualys アプリが表示されるのはなぜですか?
[Microsoft Defender for servers](defender-for-servers-introduction.md) には、お使いのマシンの脆弱性スキャンが追加費用なしで含まれています。 Qualys ライセンスも Qualys アカウントも必要ありません。すべてが Defender for Cloud 内でシームレスに処理されます。 このスキャナーの詳細およびデプロイ方法の手順については、[Defender for Cloud の統合された Qualys 脆弱性評価ソリューション](deploy-vulnerability-assessment-vm.md)に関する記事を参照してください。

Defender for Cloud にスキャナーをデプロイするときにアラートが生成されないようにするには、適応型アプリケーション制御の推奨許可リストに、すべてのコンピューターのスキャナーが含まれていることを確認します。 


## <a name="next-steps"></a>次の手順
このページでは、Azure と Azure 以外のマシンで実行されるアプリケーションの許可リストを定義するために、Microsoft Defender for Cloudで適応型アプリケーション制御を使用する方法を学習しました。 その他のクラウド ワークロード保護機能の詳細については、次のページを参照してください。

* [Just-In-Time (JIT) VM アクセスについて](just-in-time-access-overview.md)
* [Azure Kubernetes クラスターのセキュリティ保護](defender-for-kubernetes-introduction.md)
