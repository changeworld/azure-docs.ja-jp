<properties
	pageTitle="Azure Active Directory B2C プレビュー: Multi-Factor Authentication | Microsoft Azure"
	description="Azure Active Directory B2C によってセキュリティ保護されているコンシューマー向けアプリケーションで Multi-factor Authentication を有効にする方法"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/04/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C プレビュー: コンシューマー向けアプリケーションで Multi-factor Authentication を有効にする

Azure Active Directory (AD) B2C は [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) と直接統合しているので、コンシューマー向けアプリケーションのサインアップおよびサインイン エクスペリエンスに第 2 のセキュリティ層を簡単に追加できます。また、コードを 1 行も記述することなく、これを実現できます。現在、検証オプションとしては電話およびテキスト メッセージがサポートされています。サインアップおよびサインイン ポリシーを既に作成してある場合 ([この記事]()を参照)、後のセクションで示すように Multi-Factor Authentication を有効にできます。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]Multi-Factor Authentication は、既存のポリシーを編集することによってだけでなく、サインアップおよびサインイン ポリシーの作成時に有効にすることもできます。

この機能を利用すると、次のようなシナリオをアプリケーションで処理できます。

- あるアプリケーションにアクセスするには Multi-Factor Authentication は必要ないが、別のアプリケーションにアクセスするには必要な場合。たとえば、コンシューマーは自動車保険アプリケーションにはソーシャルまたはローカル アカウントでサインインできますが、同じディレクトリに登録されている住宅保険アプリケーションにアクセスするには事前に電話番号の確認が必要であるような場合です。
- アプリケーションへの通常のアクセスには Multi-Factor Authentication は必要ないが、アプリケーション内の機密性が高い部分へのアクセスには必要である場合。たとえば、コンシューマーはソーシャルまたはローカル アカウントを使用して銀行取引アプリケーションにサインインし、口座の残高を確認できますが、ネット送金を行うには事前に電話番号の確認が必要になるような場合です。

## サインアップ ポリシーを変更して Multi-factor Authentication を有効にする

1. [この手順に従って、Azure ポータルで B2C 機能ブレードに移動します。](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)
2. **[サインアップ ポリシー]** をクリックします。
3. クリックしてサインアップ ポリシーを開きます (例: "B2C\_1\_SiUp")。
4. **[Multi-Factor Authentication]** をクリックし、**[状態]** を **[オン]** にします。**[OK]** をクリックします。
5. ブレードの上部にある **[保存]** をクリックします。以上で終わりです。

ポリシーで [今すぐ実行] 機能を使用して、コンシューマー エクスペリエンスを確認できます。次のような結果になるはずです。

Multi-Factor Authentication の手順が実行される前に、コンシューマー アカウントがディレクトリに作成されます。この手順の過程で、コンシューマーは自分の電話番号を示し、確認することを求められます。検証が成功した場合、後で使用できるように電話番号がコンシューマー アカウントに関連付けられます。コンシューマーがキャンセルまたは中止した場合であっても、次にサインインするときに電話番号の確認を再度求められる場合があります (Multi-Factor Authentication が有効になっている場合。次のセクションを参照)。

## サインイン ポリシーを変更して Multi-factor Authentication を有効にする

1. [この手順に従って、Azure ポータルで B2C 機能ブレードに移動します。](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)
2. **[サインイン ポリシー]** をクリックします。
3. クリックしてサインイン ポリシーを開きます (例: "B2C\_1\_SiIn")。ブレードの上部にある **[編集]** をクリックします。
4. **[Multi-Factor Authentication]** をクリックし、**[状態]** を **[オン]** にします。**[OK]** をクリックします。
5. ブレードの上部にある **[保存]** をクリックします。以上で終わりです。

ポリシーで [今すぐ実行] 機能を使用して、コンシューマー エクスペリエンスを確認できます。次のような結果になるはずです。

コンシューマーがソーシャルまたはローカル アカウントを使用してサインインするとき、検証済みの電話番号がコンシューマー アカウントに関連付けられている場合は、コンシューマーはその確認を求められます。電話番号が関連付けられていない場合は、コンシューマーは電話番号を指定して確認するよう求められます。検証が成功した場合、後で使用できるように電話番号がコンシューマー アカウントに関連付けられます。

<!---HONumber=AcomDC_0107_2016-->