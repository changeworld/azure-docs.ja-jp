---
title: "Azure AD Connect sync: Office 365 の Multi-Geo 機能での優先されるデータの場所の構成 | Microsoft Docs"
description: "Azure AD Connect 同期を使用して、Office 365 ユーザー リソースをユーザーの近くに配置する方法について説明します。"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: billmath
ms.openlocfilehash: 021f009e66e57665a2252646b210f0e6dc55d33c
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="azure-ad-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Azure AD Connect 同期: Office 365 リソースの優先されるデータの場所の構成
このトピックの目的は、Azure AD Connect 同期の PreferredDataLocation を構成する方法について説明することです。Office 365 で Multi-Geo 機能を使用するときに、この属性を使用して、ユーザーの Office 365 データの地理的な場所を指定します。 **リージョン**と **Geo** という用語は、どちらも同じ意味で使用することができます。

> [!IMPORTANT]
> Multi-Geo は現在プレビュー段階です。 プレビュー プログラムへの参加を希望する場合は、マイクロソフトの担当者にお問い合わせください。
>
>

## <a name="enable-synchronization-of-preferreddatalocation"></a>PreferredDataLocation の同期の有効化
既定では、ユーザーの Office 365 リソースは、Azure AD テナントと同じ geo にあります。 たとえば、テナントが北米にある場合、ユーザーの Exchange メールボックスも北米にあります。 多国籍組織にとっては、これが最適でないことがあります。 preferredDataLocation 属性を設定すると、ユーザーの geo を定義できます。

この属性を設定することで、ユーザーの Office 365 リソース (メールボックスや OneDrive など) をユーザーと同じ geo に配置しながら、組織全体で 1 つのテナントを維持できます。

> [!IMPORTANT]
> Multi-Geo を利用するには、Office 365 サブスクリプションに少なくとも 5000 のライセンスを所有している必要があります。
>
>

Office 365 の全 geo の一覧については「[データの保管場所](https://aka.ms/datamaps)」で確認できます。

Multi-Geo で使用できる Office 365 の geo を次に示します。

| ジオ (主要地域)  | preferredDataLocation 値 |
| --- | --- |
| アジア太平洋 | APC |
| オーストラリア | AUS |
| カナダ | CAN |
| 欧州連合 | EUR |
| インド | IND |
| 日本 | JPN |
| 韓国 | KOR |
| 英国 | GBR |
| 米国 | NAM |

* この表に掲載されていない geo (南米など) は、Multi-Geo には使用できません。
* インドと韓国の geo を利用できるのは、請求先住所とライセンス購入先がそれらの geo に該当するユーザーだけです。
* すべての Office 365 ワークロードで、ユーザーの geo 設定の使用がサポートされているわけではありません。

バージョン 1.1.524.0 以降の Azure AD Connect には、**ユーザー** オブジェクトの **PreferredDataLocation** 属性を同期する機能が備わっています。 具体的には、以下の変更が行われています。

* Azure AD コネクタで、**User** オブジェクト タイプのスキーマが拡張され、PreferredDataLocation 属性 (単一値の文字列型) が追加されました。
* メタバースで、**Person** オブジェクト タイプのスキーマが拡張され、PreferredDataLocation 属性 (文字列型、単一値) が追加されました。

既定では、PreferredDataLocation 属性は、同期に対して有効になっていません。 この機能は大規模な組織を対象としており、誰もがメリットを得られるわけではありません。 オンプレミスの Active Directory には PreferredDataLocation 属性が存在しないため、ユーザーの Office 365 geo を保持するための属性も識別する必要があります。 これは、組織ごとに異なります。

> [!IMPORTANT]
> 現在、Azure AD では、同期されたユーザー オブジェクトとクラウドのユーザー オブジェクトの両方の PreferredDataLocation 属性を、Azure AD PowerShell を使って直接構成できるようになっています。 いったん PreferredDataLocation 属性の同期を有効にしたら、**同期されたユーザー オブジェクト**の属性を Azure AD PowerShell で構成することはやめてください。Azure AD PowerShell で構成した属性の値は、Azure AD Connect での同期元となるオンプレミス Active Directory の属性の値に基づいて上書きされます。

> [!IMPORTANT]
> 2017 年 9 月 1 日以降、**同期されたユーザー オブジェクト**の PreferredDataLocation 属性を、Azure AD PowerShell で直接構成することはできません。 同期されたユーザー オブジェクトの PreferredLocation 属性を構成する場合は、Azure AD Connect を使用する必要があります。

PreferredDataLocation 属性の同期を有効にする前に、以下を行う必要があります。

* まず、ソース属性として使用するオンプレミス Active Directory 属性を決めます。 これは、**単一値の文字列**型である必要があります。 以下の手順では、extensionAttributes のいずれかが使用されます。
* Azure AD に既に存在する同期されているユーザー オブジェクトに対し、Azure AD PowerShell を使って PreferredDataLocation 属性を構成したことがある場合、その属性値を、オンプレミス Active Directory 内の対応するユーザー オブジェクトに**バックポート**する必要があります。

    > [!IMPORTANT]
    > オンプレミス Active Directory 内の対応するユーザー オブジェクトに属性値をバックポートしなかった場合、PreferredDataLocation 属性の同期を有効にした時点で、Azure AD Connect によって既存の属性値が Azure AD から削除されます。

* ソース属性は、少なくとも 2 ～ 3 個のオンプレミス AD ユーザー オブジェクトに対して構成することをお勧めします。これらのユーザー オブジェクトを後で検証に使用することができます。

PreferredDataLocation 属性の同期を有効にする大まかな手順は次のとおりです。

1. 同期スケジューラを無効にし、進行中の同期がないことを確認する
2. オンプレミスの ADDS コネクタのスキーマにソース属性を追加する
3. Azure AD コネクタのスキーマに PreferredDataLocation を追加する
4. オンプレミス Active Directory から属性値を誘導する受信方向の同期規則を作成する
5. Azure AD に属性値を誘導する送信方向の同期規則を作成する
6. 完全同期サイクルを実行する
7. 同期スケジューラを有効にする
8. 結果を確認する

> [!NOTE]
> 以降このセクションでは、これらの手順について詳しく説明します。 これらの説明では、単一フォレスト トポロジの Azure AD デプロイを想定しており、また、カスタム同期規則は使用しません。 複数フォレスト トポロジを使用していて、カスタム同期規則が構成されているか、またはステージング サーバーが存在する場合は、適宜これらの手順を調整する必要があります。

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>手順 1: 同期スケジューラを無効にし、進行中の同期がないことを確認する
意図しない変更が Azure AD にエクスポートされるのを防ぐために、同期規則の更新中は、同期が実行されないように注意してください。 組み込みスケジューラを無効にするには、以下のようにします。

1. Azure AD Connect サーバーで PowerShell セッションを開始します。
2. `Set-ADSyncScheduler -SyncCycleEnabled $false` コマンドレットを実行して、スケジュールされた同期を無効にします。
3. **[スタート]** > **[Synchronization Service]** の順に移動して、**Sychronization Service Manager** を起動します。
4. **[操作]** タブに移動し、状態が "*進行中*" になっている操作がないことを確認します。

![Synchronization Service Manager - 進行中の処理がないことを確認](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema"></a>手順 2: オンプレミスの ADDS コネクタのスキーマにソース属性を追加する
オンプレミス AD のコネクタ スペースには、一部の AD 属性がインポートされません。 既定で同期されない属性を使用するように選択した場合、その属性はインポートする必要があります。 インポート対象の属性のリストにソース属性を追加するには、次の手順を実行します。

1. Synchronization Service Manager の **[コネクタ]** タブに進みます。
2. **オンプレミス AD コネクタ**を右クリックし、**[プロパティ]** を選択します。
3. ポップアップ ダイアログで **[属性の選択]** タブに移動します。
4. 属性の一覧で、使用することを選択したソース属性のチェック ボックスがオンになっていることを確認します。 属性が表示されない場合は、[すべて表示] チェックボックスをクリックします。
5. **[OK]** をクリックして保存します。

![オンプレミス AD コネクタのスキーマへのソース属性の追加](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>手順 3: Azure AD コネクタのスキーマに PreferredDataLocation を追加する
既定では、Azure AD のコネクタ スペースに PreferredDataLocation 属性がインポートされません。 インポート対象の属性のリストに PreferredDataLocation 属性を追加するには、次の手順を実行します。

1. Synchronization Service Manager の **[コネクタ]** タブに進みます。
2. **Azure AD コネクタ**を右クリックし、**[プロパティ]** を選択します。
3. ポップアップ ダイアログで **[属性の選択]** タブに移動します。
4. 属性の一覧で PreferredDataLocation 属性を選択します。
5. **[OK]** をクリックして保存します。

![Azure AD コネクタのスキーマへのソース属性の追加](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>手順 4: オンプレミス Active Directory から属性値を誘導する受信方向の同期規則を作成する
受信方向の同期規則によって、属性値をオンプレミス Active Directory のソース属性からメタバースに誘導することができます。

1. **[スタート]** > **[Synchronization Rules Editor]** の順に移動して、**Synchronization Rules Editor** を起動します。
2. 検索フィルターの **[方向]** を **[受信]** に設定します。
3. **[新しいルールの追加]** ボタンをクリックして受信方向の規則を新規作成します。
4. **[説明]** タブで次の構成を指定します。

    | Attribute | 値 | 詳細 |
    | --- | --- | --- |
    | Name | *名前を入力します* | 例: "*In from AD - User PreferredDataLocation*" |
    | [説明] | "*ユーザー設定の説明を入力します*" |  |
    | 接続先システム | "*オンプレミス AD コネクタを選択します*" |  |
    | 接続先システム オブジェクトの種類 | **User** |  |
    | メタバース オブジェクトの種類 | **Person** |  |
    | リンクの種類 | **Join** |  |
    | 優先順位 | "*1 ～ 99 の範囲の数値を選択します*" | 1 ～ 99 は、カスタム同期規則用に予約されています。 他の同期規則で使用されている値は選択しないでください。 |

5. **[Scoping filter]\(スコープ フィルター\)** は空のままにして、すべてのオブジェクトを含めます。 スコープ フィルターは、実際の Azure AD Connect のデプロイに応じて調整が必要となる場合があります。
6. **[変換]** タブに移動し、次の変換規則を実装します。

    | フローの種類 | ターゲット属性 | ソース | 1 度だけ適用する | マージの種類 |
    | --- | --- | --- | --- | --- |
    |直接 | PreferredDataLocation | ソース属性を選択します | オフ | プライマリの |

7. **[追加]** をクリックして受信方向の規則を作成します。

![受信方向の同期規則の作成](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>手順 5: Azure AD に属性値を誘導する送信方向の同期規則を作成する
送信方向の同期規則によって、属性値をメタバースから Azure AD の PreferredDataLocation 属性に誘導することができます。

1. **同期規則**エディターに移動します。
2. 検索フィルターの **[方向]** を **[送信]** に設定します。
3. **[新しいルールの追加]** ボタンをクリックします。
4. **[説明]** タブで次の構成を指定します。

    | Attribute | 値 | 詳細 |
    | ----- | ------ | --- |
    | Name | *名前を入力します* | 例: "Out to AAD - User PreferredDataLocation" |
    | [説明] | *説明を入力します* ||
    | 接続先システム | "*AAD コネクタを選択します*" ||
    | 接続先システム オブジェクトの種類 | User ||
    | メタバース オブジェクトの種類 | **Person** ||
    | リンクの種類 | **Join** ||
    | 優先順位 | "*1 ～ 99 の範囲の数値を選択します*" | 1 ～ 99 は、カスタム同期規則用に予約されています。 他の同期規則で使用されている値は選択しないでください。 |

5. **[Scoping filter]\(スコープ フィルター\)** タブに移動し、**次の 2 つの句を使って単一のスコープ フィルター グループ**を追加します。

    | Attribute | 演算子 | 値 |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | User |
    | cloudMastered | NOTEQUAL | True |

    この送信方向の同期規則がどの Azure AD オブジェクトに適用されるかは、スコープ フィルターによって決まります。 この例で使用しているスコープ フィルターは、標準の同期規則 "Out to AD - User Identity" と同じものです。 こうすることで、オンプレミス Active Directory との間で同期されていないユーザー オブジェクトに、この同期規則が適用されるのを防いでいます。 スコープ フィルターは、実際の Azure AD Connect のデプロイに応じて調整が必要となる場合があります。

6. **[変換]** タブに移動し、次の変換規則を実装します。

    | フローの種類 | ターゲット属性 | ソース | 1 度だけ適用する | マージの種類 |
    | --- | --- | --- | --- | --- |
    | 直接 | PreferredDataLocation | PreferredDataLocation | オフ | プライマリの |

7. **[追加]** をクリックして送信方向の規則を作成します。

![送信方向の同期規則の作成](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>手順 6: 完全同期サイクルを実行する
AD のスキーマと Azure AD コネクタのスキーマに新しい属性を追加し、カスタム同期規則を導入したので、一般に完全同期サイクルが必要です。 これらの変更を検証したうえで Azure AD にエクスポートすることをお勧めします。 完全同期サイクルの手順を手動で実行する過程で、次の手順に従って変更を検証できます。

1. **オンプレミス AD コネクタ**で**フル インポート**の手順を実行します。

   1. Synchronization Service Manager の **[操作]** タブに進みます。
   2. **オンプレミス AD コネクタ**を右クリックし、**[実行]** を選択します。
   3. ポップアップ ダイアログで **[フル インポート]** を選択し、**[OK]** をクリックします。
   4. 操作の完了を待ちます。

    > [!NOTE]
    > インポート対象の属性のリストにソース属性が既に追加されている場合、オンプレミス AD コネクタでのフル インポートはスキップしてかまいません。 つまり、「[手順 2: オンプレミス AD コネクタのスキーマにソース属性を追加する](#step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema)」で何も変更する必要がなかったケースが該当します。

2. **Azure AD コネクタ**で**フル インポート**の手順を実行します。

   1. **Azure AD コネクタ**を右クリックし、**[実行]** を選択します。
   2. ポップアップ ダイアログで **[フル インポート]** を選択し、**[OK]** をクリックします。
   3. 操作の完了を待ちます。

3. 既存のユーザー オブジェクトで同期規則の変更を検証します。

オンプレミス Active Directory からのソース属性と Azure AD からの PreferredDataLocation がそれぞれのコネクタ スペースにインポートされています。 完全同期の手順に進む前に、オンプレミス AD のコネクタ スペースに既に存在しているユーザー オブジェクトで**プレビュー**を実行することをお勧めします。 選択したオブジェクトのソース属性に値が設定されている必要があります。 メタバースの PreferredDataLocation に値が設定されていることを**プレビュー**で確認できれば、同期規則が正しく構成されていると考えられます。 **プレビュー**の方法については、「[変更を確認する](active-directory-aadconnectsync-change-the-configuration.md#verify-the-change)」セクションを参照してください。

4. **オンプレミス AD コネクタ**で**完全同期**の手順を実行します。

   1. **オンプレミス AD コネクタ**を右クリックし、**[実行]** を選択します。
   2. ポップアップ ダイアログで **[完全同期]** を選択し、**[OK]** をクリックします。
   3. 操作の完了を待ちます。

5. Azure AD に対する**保留中のエクスポート**を確認します。

   1. **Azure AD コネクタ**を右クリックし、**[コネクタ スペースの検索]** を選択します。
   2. [Search Connector Space]\(コネクタ スペースの検索\) ポップアップ ダイアログで次の手順を実行します。

      1. **[スコープ]** を **[Pending Export]\(保留中のエクスポート\)** に設定します。
      2. **[追加]、[変更]、[削除]** を含む、3 つすべてのチェック ボックスをオンにします。
      3. **[検索]** ボタンをクリックすると、変更がエクスポート待ちになっているオブジェクトが一覧表示されます。 指定したオブジェクトの変更を検証するには、オブジェクトをダブルクリックします。
      4. 変更が正しいことを確認します。

6. **Azure AD コネクタ**で**エクスポート**の手順を実行します。

   1. **Azure AD コネクタ**を右クリックし、**[実行]** を選択します。
   2. [Run Connector]\(コネクタの実行\) ポップアップ ダイアログで、**[エクスポート]** を選択し、**[OK]** をクリックします。
   3. Azure AD へのエクスポートが完了するまで待ちます。

> [!NOTE]
> 以上の手順には、Azure AD コネクタでの完全同期の手順と AD コネクタでのエクスポートが含まれていないことにお気付きでしょうか。 属性値の流れはオンプレミス Active Directory から Azure AD への一方向であるため、これらの手順は必要ありません。

## <a name="step-7-re-enable-sync-scheduler"></a>手順 7: 同期スケジューラを再度有効にする
次の手順で組み込みの同期スケジューラを再度有効にします。

1. PowerShell セッションを開始します。
2. 以下のコマンドレットを実行して、スケジュールされた同期を再度有効にします。`Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>手順 8: 結果を確認する
次に、構成を確認し、ユーザーに対して有効にします。

1. ユーザーに対して選択した属性に geo を追加します。 使用できる geo の一覧については、[こちらの表](#enable-synchronization-of-preferreddatalocation)を参照してください。  
![ユーザーに追加される AD 属性](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. 属性が Azure AD に同期されるまで待ちます。
3. Exchange Online PowerShell を使用して、メールボックスのリージョンが適切に設定されていることを確認します。  
![Exchange Online でユーザーに設定されたメールボックスのリージョン](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
テナントがこの機能を使用できるように設定されていることを前提として、メールボックスが適切な geo に移動されます。 これは、メールボックスが配置されているサーバー名を調べることで確認できます。
4. この設定が多数のメールボックスで有効であることを確認するには、[Technet ギャラリー](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e)のスクリプトを使用します。 このスクリプトには、すべての Office 365 データセンターのサーバーのプレフィックスと、サーバーが配置されている geo の一覧も含まれます。 前の手順のメールボックスの場所を確認するためのリファレンスとして、それを使用できます。

## <a name="next-steps"></a>次の手順

**Office 365 での Multi-Geo の詳細**

* Ignite での Multi-Geo セッション: https://aka.ms/MultiGeoIgnite
* OneDrive における Multi-Geo: https://aka.ms/OneDriveMultiGeo
* SharePoint Online における Multi-Geo: https://aka.ms/SharePointMultiGeo

**同期エンジンの構成モデルの詳細:**

* この構成モデルについて詳しくは、「 [Understanding Declarative Provisioning (宣言型のプロビジョニングについて)](active-directory-aadconnectsync-understanding-declarative-provisioning.md)」をご覧ください。
* 式言語について詳しくは、「 [宣言型のプロビジョニングの式について](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)」をご覧ください。

**概要トピック**

* [Azure AD Connect sync: 同期を理解してカスタマイズする](active-directory-aadconnectsync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)
