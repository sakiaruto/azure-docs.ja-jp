---
title: "Azure Active Directory ポータルのサインイン アクティビティ レポートのエラー コード | Microsoft Docs"
description: "サインイン アクティビティ レポートのエラー コードのリファレンス。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: dcdd8b5830edb542cb99d07f1b0087629d374264
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2017
---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Azure Active Directory ポータルのサインイン アクティビティ レポートのエラー コード

ユーザー サインイン レポートによって提供される情報を使用すると、次のような疑問への答えを得ることができます。

- だれが Azure Active Directory を使ってサインインしたか
- どのアプリにサインインしたか
- どのサインインでエラーが発生したか、またその理由は何か

このトピックでは、一連のエラー コードとその説明を掲載しています。 

## <a name="how-can-i-display-failed-sign-ins"></a>失敗したサインインを表示する方法 

すべてのサインイン アクティビティ データへの最初のエントリ ポイントは、**[Azure Active Directory]** の **[アクティビティ]** セクションの **[[サインイン]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)** です。


![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins-errors/61.png "サインイン アクティビティ")


サインイン レポートで、**[サインイン状態]** に **[失敗]** を選ぶと、失敗したすべてのサインインを表示することができます。


![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins-errors/06.png "サインイン アクティビティ")

表示された一覧でいずれかの項目をクリックすると、**[アクティビティの詳細: サインイン]** ブレードが表示されます。 サインインに関して Azure Active Directory が追跡する情報は、**サインインのエラー コード**や**エラーの理由**を含め、すべてこのビューに表示されます。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins-errors/05.png "サインイン アクティビティ")


サインイン データには、Azure Portal の代わりに[レポート API](active-directory-reporting-api-getting-started-azure-portal.md) を使ってアクセスすることもできます。


次のセクションでは、発生する可能性のあるすべてのエラーとその説明を一覧形式で紹介します。 

## <a name="error-codes"></a>エラー コード

| エラー| Description |
| --- | --- |
| 50001| X という名前のサービス プリンシパルが Y という名前のテナントに見つかりませんでした。このエラーは、テナントの管理者によってアプリケーションがインストールされていない場合に発生することがあります。 または、リソースのプリンシパルがディレクトリに見つからないか無効です。|
| 50008| トークンに SAML アサーションが欠落しているか、正しく構成されていません。|
| 50011| 返信アドレスが欠落しているか、正しく構成されていません。または、アプリケーションに対して構成されている返信アドレスと一致しません。|
| 50053| ユーザーが間違った ID またはパスワードで何度もサインインを試みたために、アカウントがロックされています。|
| 50054| 認証に古いパスワードが使用されました。|
| 50055| パスワードが無効です。期限切れのパスワードが入力されました。|
| 50057| ユーザー アカウントが無効にされています。|
| 50058| 入力された資格情報にユーザーの ID に関する情報が見つからないか、テナントにユーザーが見つかりません。またはサイレント サインイン要求が送信されたものの、いずれのユーザーもサインインしていないか、サービスがユーザーを認証できませんでした。|
| 50074| 強力な認証 (第 2 の要素) が必要です。|
| 50079| 第 2 要素認証を行うにはユーザーの登録が必要です。|
| 50126| ユーザー名またはパスワードが無効であるか、オンプレミスのユーザー名またはパスワードが無効です。|
| 50131| さまざまな条件付きアクセス エラーで使用されます  (Windows デバイスの状態が無効である、疑わしいアクティビティ、アクセス ポリシー、セキュリティ ポリシーの判断が原因で要求がブロックされた、など)。|
| 50133| 期限切れまたは最近のパスワード変更が原因でセッションが無効です。|
| 50144| ユーザーの Active Directory パスワードの有効期限が切れています。|
| 65001| アプリケーション X に、アプリケーション Y へのアクセス許可がありません。またはアクセス許可が取り消されました。 または、X という ID でアプリケーションを使用することにユーザーまたは管理者が同意していません。このユーザーとリソースのインタラクティブな承認要求を送信してください。 または、X という ID でアプリケーションを使用することにユーザーまたは管理者が同意していません。リソース Z に対する操作をアプリ Y に代わって行うための承認要求をテナント管理者に送信してください。|
| 65005| アプリケーションの必須リソース アクセス リストに、リソースによって検出可能なアプリケーションが含まれていません。または、必須リソース アクセス リストで指定されていないリソースへのアクセスをクライアント アプリケーションが要求したか、Graph サービスから無効な要求が返されたか、リソースが見つかりません。|
| 70001| X という名前のアプリケーションが Y という名前のテナントに見つかりませんでした。このエラーは、アプリケーションがテナントの管理者によってインストールされていない場合や、アプリケーションがテナント内のいずれのユーザーによっても同意されていない場合に発生することがあります。 間違ったテナントに認証要求を送信した可能性があります。|
| 80001| 利用できる認証エージェントがありません。|
| 80002| 認証エージェントのパスワード検証要求がタイムアウトしました。|
| 80003| 認証エージェントが無効な応答を受信しました。|
| 80004| サインイン要求に使用されたユーザー プリンシパル名 (UPN) が正しくありません。|
| 80005| 認証エージェント: エラーが発生しました。|
| 80007| 認証エージェントが Active Directory に接続できません。|
| 80010| 認証エージェントがパスワードを解読できません。|
| 81001| ユーザーの Kerberos チケットが大きすぎます。|
| 81002| ユーザーの Kerberos チケットを検証できません。|
| 81003| ユーザーの Kerberos チケットを検証できません。|
| 81004| Kerberos 認証を試みましたが失敗しました。|
| 81008| ユーザーの Kerberos チケットを検証できません。|
| 81009| ユーザーの Kerberos チケットを検証できません。|
| 81010| シームレス SSO に失敗しました。ユーザーの Kerberos チケットが期限切れか無効です。|
| 81011| ユーザーの Kerberos チケット内の情報に、ユーザー オブジェクトが見つかりません。|
| 81012| Azure AD にサインインしようとしているユーザーは、デバイスにサインインしているユーザーと異なります。|
| 81013| ユーザーの Kerberos チケット内の情報に、ユーザー オブジェクトが見つかりません。|
| 90014| さまざまなケースで、資格情報に想定されているフィールドが存在しないときに使用されます。|
| 90093| 要求に対して Forbidden エラー コードが Graph から返されました。|



## <a name="next-steps"></a>次のステップ

詳細については、「[Azure Active Directory ポータルのサインイン アクティビティ レポート](active-directory-reporting-activity-sign-ins.md)」を参照してください。

