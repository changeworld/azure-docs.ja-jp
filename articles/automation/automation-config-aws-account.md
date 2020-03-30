---
title: アマゾン ウェブ サービスで認証を構成する
description: この記事では、AWS リソースを管理する Azure Automation の Runbook 用に AWS 資格情報を作成し検証する方法について説明します。
keywords: AWS の認証, AWS の構成
services: automation
ms.subservice: process-automation
ms.date: 04/17/2018
ms.topic: conceptual
ms.openlocfilehash: 596dc334a412b3e0839d7661a23af771e5cd7394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75366942"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>アマゾン ウェブ サービスによる Runbook の認証

アマゾン ウェブ サービス (AWS) のリソースに関する一般的なタスクを自動化する処理は、Azure の Automation Runbook で実現することができます。 Automation Runbook を使用すれば、Azure のリソースの場合と同様に、AWS でも多くのタスクを自動化することができます。 必要となるのは次の 2 点だけです。

* AWS サブスクリプションと一連の資格情報。 具体的には、AWS アクセス キーと秘密キーです。 詳細については、記事「[Using AWS Credentials (AWS 資格情報の使用)](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html)」を参照してください。
* Azure サブスクリプションと Automation アカウント。

AWS で認証するには、一連の AWS 資格情報を指定して、Azure Automation から実行される Runbook を認証する必要があります。 Automation アカウントを既に作成しており、それを使用して AWS で認証する場合は、次のセクションの手順に従うことができます。アカウントを AWS リソースを対象とする Runbook 専用にする場合は、まず、新しい [Automation アカウント](automation-offering-get-started.md)を作成 (サービス プリンシパルの作成オプションを省略) し、次の手順を使用する必要があります。

## <a name="configure-automation-account"></a>Automation アカウントの構成

Azure Automation が AWS と通信する場合は、まず、AWS 資格情報を取得し、それを資産として Azure Automation に格納する必要があります。 AWS ドキュメント「[Managing Access Keys for your AWS Account (AWS アカウントのアクセス キーを管理する)](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html)」に記載されている以下の手順を実行して、アクセス キーを作成し、**アクセス キー ID** と**シークレット アクセス キー**をコピーします (必要に応じて、キー ファイルをダウンロードし、どこか安全な場所に保存します)。

AWS セキュリティ キーを作成し、コピーしたら、Azure Automation アカウントで資格情報資産を作成し、それらを安全に格納し、Runbook で参照する必要があります。 「**Azure Automation での資格情報資産**」記事の[新しい資格情報資産の作成](shared-resources/credentials.md#to-create-a-new-credential-asset-with-the-azure-portal)に関するセクションの手順に従って、次の情報を入力します。

1. **[名前]** ボックスに「**AWScred**」と入力するか、または、命名規則に従って適切な値を入力します。
2. **[ユーザー名]** ボックスに**アクセス ID** を入力し、 **[パスワード]** および **[パスワードの確認]** ボックスに**シークレット アクセス キー**を入力します。

## <a name="next-steps"></a>次のステップ

* AWS のタスクを自動化する Runbook を作成する方法については、[Amazon Web Services の VM デプロイの自動化](automation-scenario-aws-deployment.md)に関する記事をご覧ください。
