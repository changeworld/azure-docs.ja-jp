---

title: "Azure Active Directory のグループベースのライセンスが使用されるその他のシナリオ | Microsoft Docs"
description: "Azure Active Directory のグループベースのライセンスが使用されるその他のシナリオ"
services: active-directory
keywords: "Azure AD のライセンス"
documentationcenter: 
author: curtand
manager: femila
editor: piotrci
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/02/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 9b5b87574edc828a41b421a64f543fc34742c5a3
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017


---

# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Azure Active Directory のライセンス管理にグループを使用する際のシナリオ、制限、および既知の問題

次の情報と例を使用して Azure Active Directory (Azure AD) のグループベースのライセンスについて詳しく理解できます。

## <a name="usage-location"></a>利用場所

一部の Microsoft サービスは、すべての場所で利用できないことがあります。 ライセンスをユーザーに割り当てる前に、管理者はユーザーの **[利用場所]** プロパティを指定しておく必要があります。 [Azure Portal](https://portal.azure.com) の **[ユーザー]** &gt; **[プロファイル]** &gt; **[設定]** で指定できます。

グループ ライセンスの割り当ての際、利用場所が指定されていないユーザーは、ディレクトリの場所を継承します。 複数の場所にユーザーがいる場合、そのことをユーザー オブジェクトに正しく反映させてから、ライセンスが付与されたグループにユーザーを追加するようにしてください。

> [!NOTE]
> グループ ライセンスの割り当てによってユーザーの既存の利用場所の値が変更されることはありません。 Azure AD でのユーザー作成フローの一環として利用場所を常に設定することをお勧めします (AAD Connect 構成など)。これにより、ライセンス割り当ての結果が常に正しいことが保証され、ユーザーは許可されていない場所でサービスを受けることができなくなります。

## <a name="use-group-based-licensing-with-dynamic-groups"></a>グループベースのライセンスを動的グループとともに使用する

グループベースのライセンスは任意のセキュリティ グループとともに使用できます。つまり、Azure AD の動的グループと組み合わせることができます。 動的グループでは、ユーザー オブジェクト属性に対して規則が実行され、グループのユーザーが自動的に追加、削除されます。

たとえば、ユーザーに割り当てる一連の製品の動的グループを作成できます。 各グループは属性でユーザーを追加する規則によって設定され、各グループには、受け取る必要があるライセンスが割り当てられます。 属性をオンプレミスで割り当てて Azure AD と同期するか、属性をクラウドで直接管理できます。

ライセンスはグループに追加された後すぐに、ユーザーに割り当てられます。 属性が変更されると、ユーザーはグループを離れ、ライセンスが削除されます。

### <a name="example"></a>例

どのユーザーが Microsoft Web サービスにアクセスできる必要があるかを決定するオンプレミスの ID 管理ソリューションの例について考えてみましょう。 **extensionAttribute1** を使用して、ユーザーが必要とするライセンスを表す文字列値を格納します。 Azure AD Connect がそれを Azure AD と同期します。

ユーザーはいずれか 1 つのライセンスのみが必要な場合もあれば、両方が必要となる場合もあります。 Office 365 Enterprise E5 および Enterprise Mobility + Security (EMS) ライセンスをグループのユーザーに配布する例を次に示します。

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Enterprise E5: 基本サービス

![Office 365 Enterprise E5 の基本サービスのスクリーンショット](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: ライセンスを持つユーザー

![Enterprise Mobility + Security のライセンスを持つユーザーのスクリーンショット](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

この例では、ユーザーに両方のライセンスを割り当てる場合、1 人のユーザーを変更し、extensionAttribute1 の値を `EMS;E5_baseservices;` に設定します。 この変更はオンプレミスで実行できます。 変更がクラウドと同期されると、ユーザーは両方のグループに自動的に追加され、ライセンスが割り当てられます。

![ユーザーの extensionAttribute1 を設定する方法を示すスクリーン ショット](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

### <a name="modify-a-dynamic-group-membership-rule"></a>動的グループのメンバーシップ規則の変更

既存のグループのメンバーシップ規則を変更するときには注意が必要です。 ルールを変更すると、すべてのユーザーがグループから削除されます。 ルールが評価され、その後ユーザーが新しい条件でグループに追加されます。

グループにライセンスが割り当てられている場合は、プロセス中にすべてのユーザーからそれらのライセンスが削除されます。 新しいライセンスは、新しい規則が評価され、ユーザーがもう一度追加された後にのみ適用されます。 ユーザーはサービスを利用できなくなったり、場合によってはデータが失われたりする可能性があります。

ライセンスの割り当てに使用されるグループのメンバーシップ規則は変更しないことをお勧めします。 代わりに、新しいメンバーシップ規則でグループを作成し、元のグループと同じライセンス設定を指定します。 すべてのメンバーが追加され、すべてのユーザーにライセンスが適用されるまで待機します。 そうなってから、元のグループを削除するようにしてください。 この方法により、ユーザーのアクセス権やデータを失わずに、新しいメンバーシップ規則に安全かつ段階的に切り替えることができます。


## <a name="multiple-groups-and-multiple-licenses"></a>複数のグループと複数のライセンス

ユーザーは、ライセンスを持つ複数のグループのメンバーになることができます。 考慮事項をいくつか以下に示します。

- 同じ製品の複数のライセンスが重複することがあり、その結果、有効になっているすべてのサービスがユーザーに適用されることがあります。 次の例は、2 つのライセンス グループを示します。"*E3 base services (E3 基本サービス)*" にはすべてのユーザーに先にデプロイする基本サービスが含まれています。 "*E3 extended services (E3 拡張サービス)*" には一部のユーザーにのみデプロイするその他のサービス (Sway と Planner) が含まれています。 この例では、ユーザーは両方のグループに追加されました。

  ![有効になっているサービスのスクリーンショット](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  その結果、この製品のライセンスを 1 つのみ使用して、製品の 12 のサービスのうち 7 つが有効になっています。

- "*E3*" ライセンスを選ぶと、どのグループが原因でどのサービスがユーザーに対して有効になったかという情報などの詳細が表示されます。

  ![グループ別に有効になっているサービスのスクリーンショット](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexist-with-group-licenses"></a>グループ ライセンスと共存する直接ライセンス

ユーザーがグループからライセンスを継承する場合、ユーザーのプロパティでライセンス割り当てを直接削除または変更することはできません。 グループで変更を行い、すべてのユーザーに反映する必要があります。

ただし、継承されたライセンスに加えて、ユーザーに対して同じ製品のライセンスを直接割り当てることができます。 他のユーザーに影響を与えずに、1 人のユーザーに対してのみ製品の追加サービスを有効にすることができます。

直接割り当てられたライセンスは削除できます。また、継承されたライセンスに影響しません。 グループから Office 365 Enterprise E3 ライセンスを継承するユーザーについて考えてみましょう。

1. 最初に、ユーザーは "*E3 basic services (E3 基本サービス)*" グループからのみライセンスを継承します。このグループでは、次のように 4 つのサービス プランが有効になります。

  ![E3 グループで有効になっているサービスのスクリーンショット](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. **[割り当て]** を選択して、E3 ライセンスをユーザーに直接割り当てることができます。 このケースでは、Yammer Enterprise 以外のすべてのサービス プランを無効にします。

  ![ユーザーにライセンスを直接割り当てる方法を示すスクリーンショット](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. その結果、ユーザーは引き続き E3 製品のライセンスを 1 つだけ使用します。 ただし、直接割り当てにより Yammer Enterprise のサービスはそのユーザーに対してのみ有効になります。 グループ メンバーシップと直接割り当てによって有効になっているサービスを確認できます。

  ![継承と直接割り当てのスクリーンショット](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. 直接割り当てを使用すると、次の操作を行うことができます。

  - Yammer Enterprise はユーザー オブジェクトに対して直接無効にできます。 その他のサービスと異なり、図の **[オン]/[オフ]** の切り替えがこのサービスに対して有効になっています。 サービスがそのユーザーに対して直接有効になっており、変更できるためです。
  - 直接割り当てられたライセンスの一部として、追加サービスも有効にすることができます。
  - **[削除]** ボタンを使用して、ユーザーから直接ライセンスを削除できます。 ユーザーは継承されたグループ ライセンスのみ持つようになり、元のサービスだけが有効なままであることを確認できます。

    ![直接割り当てを削除する方法を示すスクリーンショット](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="managing-new-services-added-to-products"></a>製品に追加される新しいサービスを管理する
Microsoft が製品に新しいサービスを追加すると、既定では、製品ライセンスを割り当てたすべてのグループでサービスが有効になります。 製品の変更に関する通知をサブスクライブしているテナントのユーザーは、今後のサービスの追加について事前に通知するメールを受け取ります。

管理者は、変更によって影響を受けるすべてのグループを確認し、各グループで新しいサービスを無効にするなどのアクションを実行できます。 たとえば、デプロイする特定のサービスのみを対象とするグループを作成した場合、それらのグループに再度アクセスし、新しく追加されたサービスが無効になっていることを確認することができます。

このプロセスの例を次に示します。

1. 当初、*Office 365 Enterprise E5* 製品がいくつかのグループに割り当てられています。 "*O365 E5 - Exchange のみ*" と呼ばれるこれらのグループの 1 つは、"*Exchange Online (プラン 2)*" サービスのみをメンバーに対して有効にするよう設計されています。

2. ユーザーは Microsoft から、E5 製品が新しいサービス (*Microsoft Stream*) で拡張される予定であるという内容の通知を受け取ります。 サービスがテナントで利用可能になると、次の操作を行うことができます。

3. [**[Azure Active Directory] > [ライセンス] > [すべての製品]**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) ブレードに移動して、*[Office 365 Enterprise E5]* を選択してから、**[ライセンスされているグループ]** を選択し、その製品を持つすべてのグループの一覧を表示します。

4. 確認するグループをクリックします (この場合は、*[O365 E5 - Exchange のみ]*)。 これにより、**[ライセンス]** タブが開きます。 E5 ライセンスをクリックすると、すべての有効なサービスを一覧表示するブレードが開きます。
> [!NOTE]
> このグループで *Exchange Online* サービスに加え、*Microsoft Stream* サービスも自動的に追加され、有効になっています。

  ![グループ ライセンスに追加された新しいサービスのスクリーン ショット](media/active-directory-licensing-group-advanced/manage-new-services.png)

5. このグループで新しいサービスを無効にする場合は、サービスの横にある **[オン]/[オフ]** の切り替えをクリックし、**[保存]** ボタンをクリックして変更を確認します。 これで、Azure AD はグループ内のすべてのユーザーに対する処理を実行して変更を適用します。新しいユーザーをグループに追加しても、*Microsoft Stream* サービスは有効にならなくなります。

  > [!NOTE]
  > 他のライセンス割り当て (そのユーザーが属する他のグループまたはライセンスの直接割り当て) によって、そのサービスがまだユーザーに対して有効になっている可能性があります。

6. 必要に応じて、この製品が割り当てられている他のグループにも同じ手順を実行します。

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>PowerShell を使用して、継承したライセンスと直接ライセンスを持つユーザーを表示する
グループベースのライセンスのパブリック プレビュー中は、PowerShell を使用してグループのライセンス割り当てを完全に制御することはできません。 ただし、PowerShell を使用して、ユーザーの状態に関する基本情報を検出し、ライセンスがグループから継承されたか直接割り当てられたかを確認することができます。 次のコード例は、管理者がライセンス割り当てについての基本的なレポートを生成する方法を示しています。

1. `connect-msolservice` コマンドレットを実行してテナントを認証し、接続します。

2. `Get-MsolAccountSku` を使用して、テナントにプロビジョニングされているすべての製品ライセンスを検出します。

  ![Get-Msolaccountsku コマンドレットのスクリーンショット](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. この例では、Enterprise Mobility + Security のライセンスが直接、グループから、またはその両方で割り当てられているユーザーを調べます。 次のスクリプトを使用できます。

  ```
  #Returns TRUE if the user has the license assigned directly
  function UserHasLicenseAssignedDirectly
  {
      Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
      foreach($license in $user.Licenses)
      {
          #we look for the specific license SKU in all licenses assigned to the user
          if ($license.AccountSkuId -ieq $skuId)
          {
              #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              #This could be a group object or a user object (contrary to what the name suggests)
              #If the collection is empty, this means the license is assigned directly. This is the case for users who have never been licensed via groups in the past
              if ($license.GroupsAssigningLicense.Count -eq 0)
              {
                  return $true
              }
              \#If the collection contains the ID of the user object, this means the license is assigned directly
              #Note: the license may also be assigned through one or more groups in addition to being assigned directly
              foreach ($assignmentSource in $license.GroupsAssigningLicense)
              {
                  if ($assignmentSource -ieq $user.ObjectId)
                  {
                      return $true
                  }
              }
              return $false
          }
      }
      return $false
  }
  #Returns TRUE if the user is inheriting the license from a group
  function UserHasLicenseAssignedFromGroup
  {
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)
     foreach($license in $user.Licenses)
     {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
          #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
          #This could be a group object or a user object (contrary to what the name suggests)
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
          {
                  #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                  #Note: the license may also be assigned directly in addition to being inherited
                  if ($assignmentSource -ine $user.ObjectId)

            {
                      return $true
            }
          }
              return $false
        }
      }
      return $false
  }
  ```

4. スクリプトの残りの部分ではすべてのユーザーを取得し、各ユーザーにこれらの関数を実行します。 その後、テーブルに出力をフォーマットします。

  ```
  #the license SKU we are interested in
  $skuId = "reseller-account:EMS"
  #find all users that have the SKU license assigned
  Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses.AccountSKUID -eq $skuId} | select `
      ObjectId, `
      @{Name="SkuId";Expression={$skuId}}, `
      @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly $_ $skuId)}}, `
      @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup $_ $skuId)}}
  ```

5. 完全なスクリプトの出力は次の例のようになります。

  ![PowerShell スクリプトの出力のスクリーンショット](media/active-directory-licensing-group-advanced/powershell-script-output.png)

## <a name="limitations-and-known-issues"></a>制限事項と既知の問題

グループベースのライセンスを使用する場合、次の制限事項と既知の問題の一覧について理解することをお勧めします。

- グループベースのライセンスでは、他のグループを含むグループ (入れ子になったグループ) を現在サポートしていません。 入れ子になったグループにライセンスを適用した場合は、グループの第 1 レベルのユーザー メンバーだけにライセンスが適用されます。

- この機能はセキュリティ グループでのみ使用できます。 Office グループでは現在サポートされておらず、ライセンスの割り当て処理で使用することはできません。

- 現在、[Office 365 管理ポータル](https://portal.office.com )では、グループベースのライセンスはサポートされていません。 ユーザーがライセンスをグループから継承する場合、このライセンスは Office 管理ポータルに通常のユーザー ライセンスとして表示されます。 そのライセンスを変更しようとした場合、または削除しようとした場合、ポータルはエラー メッセージを返します。 継承されたグループのライセンスをユーザーで直接変更することはできません。

- ユーザーがグループから削除され、ライセンスを失うと、そのライセンスのサービス プラン (例: SharePoint Online) は、「**中断**」状態に設定されます。 サービス プランは最終的な無効状態には設定されていません。 この予防措置によって、管理者がグループ メンバーシップの管理で間違いを犯した場合にユーザー データが誤って削除されるのを回避することができます。

- 大きなグループ (例: ユーザーが 100,000 人) に対してライセンスを割り当てるまたはライセンスを変更すると、パフォーマンスに影響が及ぶことがあります。 特に、Azure AD のオートメーションによって生成される変更の量によっては、Azure AD とオンプレミスのシステム間のディレクトリの同期のパフォーマンスに悪影響を及ぼす可能性があります。

- ライセンス管理のオートメーションは、環境内のすべての種類の変更に自動的には反応しません。 たとえば、ライセンスの不足により一部のユーザーがエラー状態になる場合があります。 使用可能なシート数を解放するために、他のユーザーから直接割り当てられた一部のライセンスを削除できます。 ただし、システムがこの変更に自動的に反応してそのエラー状態のユーザーを修正することはありません。

  この種の制限の回避策として、Azure AD の 使用可能なシート数を解放するために、**[グループ]** ブレードに移動し、**[再処理]** ボタンをクリックします。 このコマンドにより、そのグループ内のすべてのユーザーが処理され、可能であればエラー状態が解決されます。

## <a name="next-steps"></a>次のステップ

グループベースのライセンスを通じたライセンス管理の他のシナリオについては、以下をご覧ください

* [What is group-based licensing in Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md) (Azure Active Directory のグループベースのライセンスとは)
* [Assigning licenses to a group in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) (Azure Active Directory でのグループへのライセンス割り当て)
* [Azure Active Directory のグループのライセンスに関する問題の特定と解決](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [How to migrate individual licensed users to group-based licensing in Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md) (Azure Active Directory で個別にライセンスを付与されたユーザーをグループベースのライセンスに移行する方法)

