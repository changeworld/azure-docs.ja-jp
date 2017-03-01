---

title: "Azure Active Directory のグループベースのライセンスが使用されるその他のシナリオ | Microsoft Docs"
description: "Azure Active Directory のグループベースのライセンスが使用されるその他のシナリオ"
services: active-directory
keywords: "Azure AD のライセンス"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dad9a176f5f60b9165c5a55628929460047e5fdd
ms.openlocfilehash: a1510240350d88ee140acb11b3f86fd7985b9427
ms.lasthandoff: 02/22/2017


---

# <a name="azure-active-directory-group-based-licensing-additional-scenarios"></a>Azure Active Directory のグループベースのライセンスが使用されるその他のシナリオ

## <a name="group-based-licensing-using-dynamic-groups"></a>動的グループを使用したグループベースのライセンス

グループベースのライセンスはどのセキュリティ グループでも使用できるため、Azure AD 動的グループと組み合わせることができます。 動的グループでは、ユーザー オブジェクト属性に対して規則が実行されて、グループのユーザーが自動的に追加、削除されます。

たとえば、複数の動的グループ (ユーザーに割り当てる製品のセットごとに&1; つ) を作成できます。 各グループには、ユーザーが受け取る必要のあるライセンスのセットを示す特定の属性を参照する規則が含まれます。 属性は、オンプレミスで割り当てて Azure AD と同期するか、クラウドで直接管理することができます。

属性を指定すると、ユーザーがこれらの動的ライセンス グループの&1; つ以上に追加されます。 その後まもなく、ユーザーにライセンスが割り当てられます。 属性が削除されると、ユーザーはグループを離れ、ライセンスが削除されます。

### <a name="example"></a>例

この例では、どのユーザーがどの Microsoft Online Services にアクセスできる必要があるかを決定するオンプレミスの ID管理ソリューションがあります。 extensionAttribute1 を使用して、ユーザーが必要とするライセンスを表す文字列値を格納し、Azure AD Connect がそれを Azure AD と同期します。 Office 365 E5 および EMS のライセンスをユーザーに配布します。 Azure AD で、製品ごとに&1; つずつ、2 つの動的グループを作成しました。一部のユーザーは一方の製品のみ必要とし、もう一方は必要としない場合があるためです。 各グループに対して動的メンバーシップの規則とライセンス設定を指定しました。 これは次にようになります。

#### <a name="o365-e5--base-services"></a>O365 E5 – 基本サービス

![O365 E5 基本サービス](media/active-directory-licensing-group-advanced/o365-e5-base-services.png)

#### <a name="ems-e5--licensed-users"></a>EMS E5 – ライセンス ユーザー

![O365 E5 ライセンス ユーザー](media/active-directory-licensing-group-advanced/o365-e5-licensed-users.png)

オンプレミスで、1 人のユーザーを変更し、extensionAttribute1 を `EMS;E5_baseservices;` の値に設定しました。このユーザーが両方のライセンスを持つようにするためです。 変更がクラウドと同期された後、ユーザーは両方のグループに自動的に追加され、ライセンスが割り当てられました。

![ユーザーの extensionAttribute1 を設定する](media/active-directory-licensing-group-advanced/user-set-extensionAttribute1.png)

### <a name="modifying-a-dynamic-group-membership-rule"></a>動的グループ メンバーシップ規則の変更

既存のグループのメンバーシップ規則を変更するときには注意が必要です。 規則が変更されると、すべてのユーザーがグループから削除され、規則が評価されてユーザーが新しい条件に基づいてグループに追加されます。

すべてのユーザーが持つようになるライセンスがグループに割り当てられている場合、そのライセンスはプロセス中に削除されます。 新しい規則が評価され、ユーザーがもう一度追加された後でのみ、新しいライセンスが適用されます。 これは、ユーザーに対してサービスの損失、または場合によってはデータの損失が発生することを意味します。

ライセンスの割り当てに使用されるグループのメンバーシップ規則は変更しないことをお勧めします。 代わりに、新しい規則で新しいグループを作成し、元のグループと同じライセンス設定を指定します。 メンバーが追加され、すべてのユーザーにライセンスが割り当てられるまで待ちます。 その後でのみ、先に進んで元のグループを削除できます。 この方法では、ユーザーのアクセス権やデータを失わずに、新しいメンバーシップ規則に安全かつ段階的に切り替えることができます。


## <a name="multiple-groups-and-multiple-licenses"></a>複数のグループと複数のライセンス

ユーザーは、ライセンスを持つ複数のグループのメンバーになることができます。 考慮事項をいくつか以下に示します。

1. 同じ製品の複数のライセンスが重複することがあり、その結果、有効になっているすべてのサービスがユーザーに適用されることがあります。 たとえば、2 つのライセンス グループを作成しました。"*E3 – 基本サービス*" には、最初にすべてのユーザーにデプロイする基本サービスが含まれ、"*E3 – 拡張サービス*" には、Yammer を今後のデプロイで無効にしたまま、一部のユーザーで試したい追加サービス (Sway と Planner) が含まれています。 この例では、ユーザーは両方のグループに追加されました。

  ![有効になっているサービスのビュー](media/active-directory-licensing-group-advanced/view-enabled-services.png)

  その結果、この製品のライセンスを 1 つしか消費せずに、製品の 12 のサービスのうち 7 つが有効になっています。

2. "*E3*" ライセンスを選ぶと、どのグループが原因でどのサービスがユーザーに対して有効になったかという情報などの詳細が表示されます。

  ![グループによって有効化されたサービスのビュー](media/active-directory-licensing-group-advanced/view-enabled-service-by-group.png)

## <a name="direct-licenses-coexisting-with-group-licenses"></a>グループ ライセンスと共存する直接ライセンス

ユーザーがグループからライセンスを継承する場合、ユーザー オブジェクトに対する直接ライセンス割り当てを削除または変更することはできません。 代わりに、グループで変更を行い、システムによってすべてのユーザーに反映する必要があります。

ただし、継承されたライセンスに加えて、ユーザーに対して同じ SKU ライセンスを直接割り当てることができます。 たとえば、これを使用して、他のユーザーに影響を与えずに、1 人のユーザーについてのみ SKU の追加サービスを有効にすることができます。

直接割り当てられたライセンスは削除できますが、継承されたライセンスには影響しません。 グループから "*Office 365 Enterprise E3*" ライセンスを継承する次のユーザーについて考えてみましょう。これにより、いくつかのサービスが有効になります。

1. 最初に、ユーザーは "*E3 – 基本サービス*" グループからのみライセンスを継承します。 これにより、次に示す 4 つのサービス プランが有効になります。

  ![E3 グループで有効にされたサービス](media/active-directory-licensing-group-advanced/e3-group-enabled-services.png)

2. **[割り当て]** ボタンをクリックすると、E3 ライセンスをユーザーに直接割り当てることができます。この場合は、Yammer Enterprise 以外のすべてのサービス プランを無効にします。

  ![ユーザーにライセンスを割り当てる](media/active-directory-licensing-group-advanced/assign-license-to-user.png)

3. その結果、ユーザーは E3 製品のライセンスを 1 つだけ消費しますが、直接割り当てにより、そのユーザーに対してのみ "*Yammer Enterprise*" サービスが有効になります。 グループ メンバーシップと直接割り当てによって有効になっているサービスの一覧を確認できます。

  ![継承と直接割り当て](media/active-directory-licensing-group-advanced/direct-vs-inherited-assignment.png)

4. 次の操作は、直接割り当てを使用して行うことができます。

  a. "*Yammer Enterprise*" はユーザー オブジェクトに対して直接無効にできます。 他のサービス切り替えとは対照的に、オン/オフ切り替えが有効になっていることにご注意ください。このサービスはユーザーに対して直接有効化されたため、変更できます。

  b. 直接割り当てられたライセンスの一部として、追加サービスも有効にすることができます。

  c. **[削除]** ボタンを使用して、ユーザーから直接ライセンスを削除できます。 ユーザーは継承されたグループ ライセンスのみ持つようになり、元のサービスだけが有効なままであることを確認できます。

    ![直接割り当ての削除](media/active-directory-licensing-group-advanced/remove-direct-license.png)

## <a name="usage-location"></a>利用場所

一部の Microsoft サービスは場所によっては使用できません。ライセンスをユーザーに割り当てる前に、管理者はユーザーの [利用場所] プロパティを指定する必要があります。 これは、[Azure Portal](https://portal.azure.com) の [ユーザー] &gt; [プロファイル] &gt; [設定] セクションで行います。

グループ ライセンス割り当てを使用するとき、利用場所が指定されていないユーザーは、ディレクトリの場所を継承します。 異なる場所にユーザーがいる場合、そのことをユーザー オブジェクトに正しく反映させてから、ライセンスが付与されたグループにユーザーを追加するようにしてください。

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>PowerShell を使用して、継承したライセンスと直接ライセンスを持つユーザーを表示する

パブリック プレビュー中は、PowerShell を使用してグループのライセンス割り当てを完全に制御することはできません。 ただし、ユーザーの状態やライセンスがグループから継承されたか直接割り当てられたかに関する基本情報の検出には使用できます。 下のコード サンプルは、管理者がライセンス割り当てについての基本的なレポートを生成する方法を示しています。

1. `connect-msolservice` コマンドレットを実行してテナントを認証し、接続します。

2. `Get-MsolAccountSku` を使用して、テナントにプロビジョニングされているすべての SKU ライセンスを検出します。

  ![Get-Msolaccountsku コマンドレットを使用する](media/active-directory-licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. この例では、"*EMS*" ライセンスが直接、グループから、またはその両方で割り当てられているユーザーを調べます。 ユーザー オブジェクトと SKU についてこの質問に回答できる&2; つの関数を含む PowerShell スクリプトを使用します
  ```
  \# Returns TRUE if the user has the license assigned directly

  function UserHasLicenseAssignedDirectly
  {
      Param(\[Microsoft.Online.Administration.User\]\$user, \[string\]\$skuId)

      foreach(\$license in \$user.Licenses)
      {
          \# we look for the specific license SKU in all licenses assigned to the user
          if (\$license.AccountSkuId -ieq \$skuId)
          {
              \# GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              \# This could be a group object or a user object (contrary to what the name suggests)
              \# If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past

              if (\$license.GroupsAssigningLicense.Count -eq 0)
              {
                  return \$true
              }
              \# If the collection contains the ID of the user object, this means the license is assigned directly
              \# Note: the license may also be assigned through one or more groups in addition to being assigned directly
              foreach (\$assignmentSource in \$license.GroupsAssigningLicense)
              {
                  if (\$assignmentSource -ieq \$user.ObjectId)
                  {
                      return \$true
                  }

              }
              return \$false
          }
      }
      return \$false
  }
  \# Returns TRUE if the user is inheriting the license from a group
  function UserHasLicenseAssignedFromGroup
  {
      Param(\[Microsoft.Online.Administration.User\]\$user, \[string\]\$skuId)
      foreach(\$license in \$user.Licenses)
      {
          \# we look for the specific license SKU in all licenses assigned to the user
          if (\$license.AccountSkuId -ieq \$skuId)
          {
              \# GroupsAssigningLicense contains a collection of IDs of objects assigning the license
              \# This could be a group object or a user object (contrary to what the name suggests)
              foreach (\$assignmentSource in \$license.GroupsAssigningLicense)
              {
                  \# If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                  \# Note: the license may also be assigned directly in addition to being inherited
                  if (\$assignmentSource -ine \$user.ObjectId)
                  {
                      return \$true
                  }
              }
              return \$false
          }
      }
      return \$false
  }
  ```
4. スクリプトの残りの部分は、すべてのユーザーを取得し、各ユーザーに対してこれらの関数を実行し、出力をテーブルに書式化します。

  ```
  \# the license SKU we are interested in
  \$skuId = "reseller-account:EMS"
  \# find all users that have the SKU license assigned
  Get-MsolUser -All | where {\$\_.isLicensed -eq \$true -and \$\_.Licenses.AccountSKUID -eq \$skuId} | select \`
      ObjectId, \`
      @{Name="SkuId";Expression={\$skuId}}, \`
      @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly \$\_ \$skuId)}}, \`
      @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup \$\_ \$skuId)}}
  ```

5. 次のように、完全なスクリプトの出力が表示されます。

  ![PowerShell スクリプトの出力](media/active-directory-licensing-group-advanced/powershell-script-output.png)

## <a name="limitations-and-known-issues"></a>制限事項と既知の問題

1. グループベースのライセンスでは、「入れ子になったグループ」(他のグループを含むグループ) を現在サポートしていません。 入れ子になったグループにライセンスを適用した場合は、グループの第&1; レベルのユーザー メンバーだけにライセンスが適用されます。

2. 現在、グループベースのライセンスは、[Azure Portal](https://portal.azure.com) を介してのみ公開されます。 現時点では、PowerShell を使用してグループのライセンスを設定または変更することはできません。

3. 現在、[Office 365 管理ポータル](https://portal.office.com )では、グループベースのライセンスはサポートされていません。 ユーザーがライセンスをグループから継承する場合、このライセンスは Office 管理ポータルに通常のユーザー ライセンスとして表示されます。 そのライセンスを変更しようとした場合 (たとえば、ライセンスのサービスを無効にするか、ライセンスを削除しようとした場合)、(継承されたグループ ライセンスはユーザーに対して直接変更できないため) ポータルはエラー メッセージを返します。

  `To assign a license that contains Azure Information Protection Plan 1, you must also assign one of the following service plans: Azure Rights Management.`

4. ユーザーがグループから削除され、ライセンスを失うと、そのライセンスのサービス プラン (たとえば、Exchange Online や SharePoint Online) は、最終的な無効状態ではなく「中断」状態に設定されます。 これは、管理者がグループ メンバーシップの管理で間違いを犯した場合にユーザー データが誤って削除されるのを回避するための予防措置として行われます。

  これらのサービス プランの状態がこれらのユーザーに対して最終的に完全に無効になるワークフローを実装しようとしています。 これが使用可能になるまで、一部のサービスは、グループから削除されてライセンスを持たなくなったユーザーに対して動作し続ける可能性があります。

5. 非常に大きなユーザー グループ (たとえば、100,000 ユーザー) でライセンスが割り当てまたは変更された場合、Azure AD Automation によって多数の変更が生成されると、Azure AD とオンプレミスのシステム間のディレクトリ同期のパフォーマンスに悪影響が及ぶことがあります。 これは、環境内のディレクトリ同期の遅延の原因となることがあります。

6. ライセンス管理のオートメーションは、環境内のすべての種類の変更に自動的には反応しません。 たとえば、ライセンスが不足する場合や、一部のユーザーが「十分なライセンスがありません」エラー状態になる場合があります 他のユーザーから直接割り当てられたライセンスをいくつか削除して、使用可能なシート数を解放できます。 ただし、システムがこの変更に自動的に反応してそのエラー状態のユーザーを修正することはありません。

  この種の制限の回避策として、Azure AD のグループ ブレードに移動し、**[再処理]** ボタンをクリックします。 これにより、そのグループ内のすべてのユーザーが処理され、可能であればエラー状態が解決されます。

## <a name="next-steps"></a>次のステップ

グループベースのライセンスを通じたライセンス管理の他のシナリオについては、以下をご覧ください

* [What is group-based licensing in Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md) (Azure Active Directory のグループベースのライセンスとは)
* [Assigning licenses to a group in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) (Azure Active Directory でのグループへのライセンス割り当て)
* [Identifying and resolving license problems for a group in Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md) (Azure Active Directory のグループのライセンスに関する問題の特定と解決)
* [How to migrate individual licensed users to group-based licensing in Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md) (Azure Active Directory で個別にライセンスを付与されたユーザーをグループベースのライセンスに移行する方法)

