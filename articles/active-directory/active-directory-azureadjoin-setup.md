<properties 
	pageTitle="ユーザーの Azure AD 参加の設定 | Microsoft Azure" 
	description="管理者が組織内のエンド ユーザー (従業員、学生、その他のユーザー) の Azure AD への参加を設定する方法を説明します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/09/2015" 
	ms.author="femila"/>

# 組織での Azure AD 参加の設定

Azure AD 参加を設定する前に、ユーザーのオンプレミスのディレクトリをクラウドに同期するか、Azure AD に管理アカウントを手動で作成する必要があります。

オンプレミスのユーザーを Azure AD に同期するための詳しい手順については、[オンプレミスの ID と Azure Active Directory との統合](active-directory-aadconnect.md)に関するページを参照してください。


Azure AD にユーザーを手動で作成して管理する方法については、「[Azure AD でのユーザー管理](https://msdn.microsoft.com/library/azure/hh967609.aspx)」を参照してください。

## デバイスの登録の設定 
1. Azure ポータルに管理者としてサインインします。
2. 左ウィンドウで、[Active Directory] を選択します。
3. **[ディレクトリ]** タブで、ディレクトリを選択します。
4. **[構成]** タブをクリックします。
5. **[デバイス]** セクションまでスクロールします。
6. **[デバイス]**タブで、次のように設定します。  
   * **[ユーザーあたりのデバイスの最大数]**: Azure AD でユーザーが持つことができるデバイスの最大数を選択します。ユーザーがこのクォータに達した場合、1 つ以上の既存のデバイスを削除するまでデバイスを追加できなくなります。
   * **[デバイスを参加させるには Multi-factor Auth が必要]**: 有効にした場合、ユーザーはデバイスを Azure AD に参加させるために 2 つ目の認証要素を提供する必要があります。多要素認証の詳細については、「[クラウドでの Azure Multi-Factor Authentication の概要](multi-factor-authentication-get-started-cloud/)」を参照してください。
   *  **[ユーザーはデバイスの Azure AD 参加を実行できます]**: デバイスを Azure AD に参加させることができるユーザーとグループを選択します。   
    * **[Azure AD 参加済みデバイスの追加の管理者]**: Azure AD Premium または Enterprise Mobility Suite (EMS) では、デバイスに対するローカル管理者権限が付与されるユーザーを選択できます。全体管理者とデバイスの所有者には、既定でローカル管理者権限が付与されます。
   
>[AZURE.NOTE]ユーザーにエラー "**Device registration limit reached**" (エラー コード: **0x801C000E - DSREG\_ E\_ DEVICE\_ REGISTRATION\_ QUOTA\_EXCCEEDED**) が発生する場合は、ユーザーごとに許可されている最大デバイスを変更する必要があります。**[ユーザーはデバイスの Azure AD 参加を実行できます]** セクションで **[追加]** を選択し、ユーザーごとに許可するデバイスの数を設定します。
      

    
<center>![](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>
ユーザーの Azure AD 参加を設定すると、ユーザーは、会社のデバイスまたは個人のデバイスから Azure AD に接続できます。

Azure AD 参加をユーザーが設定できるようにする 3 つのシナリオを次に示します。

- ユーザーが会社所有のデバイスを直接 Azure AD に参加させる。
- ユーザーが、会社所有のデバイスをオンプレミスの Active Directory にドメイン参加させ、Azure AD に拡張する。
- ユーザーが個人のデバイス上の Windows に仕事用アカウントを追加する。 

## 追加情報
* [Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-overview.md)
* [Azure AD 参加の使用シナリオについて](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Azure AD 参加の設定](active-directory-azureadjoin-setup.md)

<!----HONumber=Oct15_HO3-->