---
title: Teams からの Azure Lab Services ラボの作成および概要
description: Teams からの Azure Lab Services ラボの作成およびその概要について説明します。
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 0604e2934ff6b011acfa9dd4a4b25fa58193e69b
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044447"
---
# <a name="get-started-and-create-a-lab-services-lab-from-teams"></a>Teams からの Lab Services ラボの作成および概要

この記事では、Teams に **Azure Lab Services** アプリを追加する方法と、MS Teams 環境にラボを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、自分のチームに仮想マシンを使用してラボを設定します。 ラボ アカウントにラボを設定するには、ラボ アカウントにおいて、あなたは所有者、ラボの作成者、または共同作成者ロールのメンバーである必要があります。 ラボ アカウントを作成するために使用したアカウントは、所有者ロールに自動的に追加されます。 そのため、ラボの作成には、ラボ アカウントの作成に使用したユーザー アカウントを使用できます。

Teams 内で Azure Lab Services を使用する際の一般的なワークフローは次のとおりです。

1. ユーザーが Azure portal で[ラボ アカウントを作成](tutorial-setup-lab-account.md#create-a-lab-account)します。
1. [ラボ アカウントの作成者が、他のユーザー](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)を**ラボの作成者**ロールに追加します。 たとえば、教師が自分の担当するクラスのラボを作成できるよう、ラボ アカウントの作成者または管理者が**ラボの作成者**ロールに教師を追加します。
1. 次いで、教師がラボの作成、テンプレート VM の事前構成を行い、チームの全員に VM をプロビジョニングするためにラボを公開します。
1. ラボが公開されると、Teams(SSO) の **Azure Lab Services** アプリを含むタブをクリックするか、[Lab Web サイト](https://labs.azure.com)にアクセスすることによって、チーム メンバーシップ リスト上のすべてのユーザーが Azure Lab Services に初めてログインするときに、VM が割り当てられます。 ユーザーはその後 VM を使用して、クラスでの作業や宿題を行えるようになります。

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>チームに Azure Lab Services アプリをタブとして追加する

チーム所有者が、自分の Teams チャネルに **Azure Lab Services** アプリを直接追加すると、チーム内のすべてのユーザーがそのアプリを使用できるようになります。 以下の 3 つの手順に従ってください。

1. アプリを追加する Teams チャネルに移動し、 **+** を選択してタブを追加します。 
1. タブ オプションから **Azure Lab Services** を検索し、このアプリを追加します。 

    > [!NOTE]
    > チームのラボは、チームの**所有者**のみが作成できます。
1. このチームのクラスルーム ラボの作成に使用する Lab Services アカウントを選択します。 

    Azure Lab Services では、シングル サインオンを使用し、[Azure Lab Services の Web サイト](https://labs.azure.com)からアクセス権を持つすべてのラボ アカウントをプルします。 

    Teams と同じテナントに属する、**所有者**、**共同作成者**、または**作成者**のアクセスがあるアカウントが表示されます。 

   ![ALS へようこそ](./media/integrate-with-teams/welcome.png) 
1. **[保存]** を押すと、タブがチャンネルに追加されます。

    これで、お使いのチャネルから **[Azure Lab Services]** タブを選択し、次の手順に従ってラボの管理を開始できます。

ラボ アカウントの選択後、チーム所有者はチームのラボを作成できるようになります。 ラボを作成する一連のすべての作業およびラボ レベルのすべてのタスクを、Teams 内で実行できるようになります。 ユーザーは、同じチーム内に複数のラボを作成できます。ラボ アカウント レベルで適切なアクセス権があるチーム所有者には、そのチームに関連付けられているラボのみが表示されます。

## <a name="deleting-classroom-labs"></a>クラスルーム ラボを削除する

Teams に作成したラボは、「[Azure Lab Services でクラスルーム ラボを管理する](how-to-manage-classroom-labs.md)」の説明に従い、[Lab Services の Web サイト](https://labs.azure.com)で削除できます。 

チームを削除する場合にも、ラボの削除がトリガーされます。 ラボが作成されているチームが削除されると、24 時間後にユーザー一覧の同期が自動でトリガーされるとき、ラボが自動的に削除されます。 

タブの削除またはアプリのアンインストールで、ラボが削除されることはありません。 タブが削除された場合でも、Web サイトでラボを削除したり、チームを削除したりすることによって、ラボの削除が明示的にトリガーされていない限り、チーム メンバーシップ リスト上のユーザーは、[Lab Services の Web サイト](https://labs.azure.com)の VM にアクセスできます。 

## <a name="next-steps"></a>次の手順

Teams 内にラボを作成すると、チーム メンバーシップが使用され、ラボのユーザー リストが自動入力および同期されます。 所有者、メンバー、ゲストなどのチームのすべてのユーザーは、ラボ ユーザー一覧に自動的に追加されます。 Azure Lab Services はチーム メンバーシップと同期され続け、自動での同期は 24 時間ごとにトリガーされます。 詳細については、次の情報を参照してください。

[Teams を使用して Lab Services ユーザー リストを管理する](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>関連項目

また、次の記事を参照してください。

- [Teams での Azure Lab Services の使用の概要](lab-services-within-teams-overview.md)
- [Teams でのラボ ユーザー リストの管理](how-to-manage-user-lists-within-teams.md)
- [Teams でのラボ VM プールの管理](how-to-manage-vm-pool-within-teams.md)
- [Teams でのラボ スケジュールの作成と管理](how-to-create-schedules-within-teams.md)
- [Teams 内での VM へのアクセス – 学生側のビュー](how-to-access-vm-for-students-within-teams.md)

