---
title: "オフライン環境に App Service をデプロイする: Azure Stack | Microsoft Docs"
description: "AD FS によって保護されている切断された Azure Stack 環境に App Service をデプロイする方法についての詳細なガイダンスです。"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: anwestg
ms.openlocfilehash: d2214b914899b24dfb36873e0083632a7deaba52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>App Service リソースプロバイダーを AD FS によって保護されている切断された Azure Stack 環境に追加する

この記事の手順に従うと、次のような Azure Stack 環境に [App Service リソースプロバイダー](azure-stack-app-service-overview.md)をインストールできます。
- インターネットに接続されていない
- Active Directory フェデレーション サービス (AD FS) によって保護されている

App Service リソースプロバイダーをオフライン Azure Stack の展開に追加するには、次の最上位のタスクをすべて実行する必要があります。

1. [前提となる手順](azure-stack-app-service-before-you-get-started.md)をすべて実行します (証明書の購入など。受け取るまでに数日かかる場合があります)。
2. インターネットに接続しているマシンに[インストールとヘルパー ファイルをダウンロードして展開](azure-stack-app-service-before-you-get-started.md)します。
3. オフライン インストール パッケージを作成します。
4. appservice.exe インストーラー ファイルを実行します。

## <a name="create-an-offline-installation-package"></a>オフライン インストール パッケージを作成する

オフライン環境で App Service を展開するには、まず、インターネットに接続されているマシンでオフライン インストール パッケージを作成する必要があります。

1. インターネットに接続されているマシンで AppService.exe インストーラーを実行します。

2. **[詳細]** > **[オフライン インストール パッケージを作成する]** の順にクリックします。

    ![App Service インストーラー](media/azure-stack-app-service-deploy-offline/image01.png)   

3. App Service インストーラーにより、オフライン インストール パッケージが作成され、そのパッケージへのパスが表示されます。 **[フォルダーを開く]** をクリックすると、エクスプローラーでフォルダーが開きます。

    ![App Service インストーラー](media/azure-stack-app-service-deploy-offline/image02.png)   

4. インストーラー (AppService.exe) とオフライン インストール パッケージを Azure Stack ホスト マシンにコピーします。

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>App Service on Azure Stack のオフライン インストールを実行する

1. オフラインの Azure Stack ホスト マシンで、azurestack\administrator として appservice.exe を実行します。

2. **[詳細設定]** > **[オフライン インストールが完了しました]** の順にクリックします。

    ![App Service インストーラー](media/azure-stack-app-service-deploy-offline/image03.png)   

3. 先ほど作成したオフライン インストール パッケージの場所を参照し、**[次へ]** をクリックします。

    ![App Service インストーラー](media/azure-stack-app-service-deploy-offline/image04.png)   

4. マイクロソフト ソフトウェア ライセンス条項を確認して同意し、**[次へ]** をクリックします。

5. サード パーティのライセンス条項を確認して同意し、**[次へ]** をクリックします。

6. App Service クラウド構成情報が正しいことを確認します。 Azure Stack Development Kit の展開中に既定の設定を使用した場合は、ここで既定値を受け入れることができます。 ただし、Azure Stack のデプロイ時にオプションをカスタマイズした場合は、そのカスタマイズ内容を反映するように、このウィンドウで値を編集する必要があります。 たとえば、ドメイン サフィックス mycloud.com を使用する場合は、エンドポイントを management.mycloud.com に変更する必要があります。自分の情報を確認したら、**[次へ]** をクリックします。

    ![App Service インストーラー](media/azure-stack-app-service-deploy/image02.png)

7. 次のページで、次の操作を行います。
    1. **[Azure Stack Subscriptions]\(Azure Stack サブスクリプション\)** ボックスの横にある **[接続]** をクリックします。
        - Azure Active Directory (Azure AD) を使用している場合は、Azure Stack の展開時に指定した、Azure AD の管理者アカウントとパスワードを入力します。 **[サインイン]**をクリックします。
        - Active Directory フェデレーション サービス (AD FS) を使用している場合は、ご自分の管理者アカウントを指定します。 たとえば、「 azurestackadmin@azurestack.local」のように入力します。 パスワードを入力し、**[サインイン]** をクリックします。
    2. **[Azure Stack Subscriptions]\(Azure Stack サブスクリプション\)** ボックスで、自分のサブスクリプションを選びます。
    3. **[Azure Stack Locations]\(Azure Stack の場所\)** ボックスで、デプロイしているリージョンに対応する場所を選びます。 たとえば、Azure Stack Development Kit にデプロイしている場合は、**[ローカル]** を選びます。
    4. ご利用の App Service デプロイの **[リソース グループ名]** を入力します。 既定では、**[APPSERVICE\<MOBILE\>]** に設定されています。
    5. インストールの一環として、App Service で作る **[ストレージ アカウント名]** を入力します。 既定では、**[appsvclocalstor]\(appsvclocalstor\)** に設定されています。
    6. **[次へ]** をクリックします。

    ![App Service インストーラー](media/azure-stack-app-service-deploy/image03.png)

8. ファイル共有の情報を入力してから、**[次へ]** をクリックします。

    ![App Service インストーラー](media/azure-stack-app-service-deploy/image04.png)

9. 次のページで、次の操作を行います。
    1. **[Identity Application ID]\(ID アプリケーションの ID\)** ボックスで、本人確認のために使っているアプリケーションの GUID を入力します。
    2. **[Identity Application certificate file]\(ID アプリケーションの証明書ファイル\)** ボックスで、証明書ファイルの場所を入力 (または参照) します。
    3. **[Identity Application certificate password]\(ID アプリケーションの証明書パスワード\)** ボックスで、証明書のパスワードを入力します。 このパスワードは、証明書作成のスクリプトを使ったときに書き留めたものです。
    4. **[Azure Resource Manager root certificate file]\(Azure Resource Manager のルート証明書ファイル\)** ボックスで、証明書ファイルの場所を入力 (または参照) します。
    5. **[次へ]** をクリックします。

    ![App Service インストーラー](media/azure-stack-app-service-deploy/image05.png)

10. 3 つの証明書ファイルの各ボックスで、**[参照]** をクリックし、適切な証明書ファイルに移動し、パスワードを入力します。 これらの証明書は、[必要な証明書の作成の手順](azure-stack-app-service-deploy.md)で作成したものです。 すべての情報を入力したら、**[次へ]** をクリックします。

    | Box | 証明書ファイル名の例 |
    | --- | --- |
    | **App Service の既定の SSL 証明書ファイル** | \_.appservice.local.AzureStack.external.pfx |
    | **App Service API の SSL 証明書ファイル** | api.appservice.local.AzureStack.external.pfx |
    | **App Service 公開元の SSL 証明書ファイル** | ftp.appservice.local.AzureStack.external.pfx |

    証明書の作成時に別のドメイン サフィックスを使った場合は、証明書ファイル名で *local.AzureStack.external* を使わないでください。 代わりに、ご自分のカスタム ドメイン情報を使用します。

    ![App Service インストーラー](media/azure-stack-app-service-deploy/image06.png)    

11. App Service リソースプロバイダー データベースをホストするために使うサーバー インスタンスについて、SQL Server の詳細を入力してから、**[次へ]** をクリックします。 インストーラーにより、SQL 接続のプロパティが検証されます。

    ![App Service インストーラー](media/azure-stack-app-service-deploy/image07.png)    

12. ロール インスタンスと SKU のオプションを確認します。 ロールごとに、お勧めの最小インスタンス SKU が既定値として設定されます。 お客様のデプロイの計画に役立つように、コア要件とメモリ要件の概要を説明します。 必要な項目を選んだら、**[次へ]** をクリックします。

    | 役割 | お勧めの最小インスタンス数 | お勧めの最小 SKU | メモ |
    | --- | --- | --- | --- |
    | コントローラー | 1 | Standard_A1 - (1 コア、1792 MB) | App Service クラウドの正常性を管理および維持します。 |
    | 管理 | 1 | Standard_A2 - (2 コア、3584 MB) | App Service Azure Resource Manager および API のエンドポイント、ポータル拡張機能 (管理、テナント、Functions ポータル)、データ サービスを管理します。 フェールオーバーをサポートする場合は、お勧めのインスタンス数は 2 つに増えます。 |
    | 発行元 | 1 | Standard_A1 - (1 コア、1792 MB) | FTP および Web デプロイによってコンテンツを公開します。 |
    | FrontEnd | 1 | Standard_A1 - (1 コア、1792 MB) | App Service アプリケーションに要求をルーティングします。 |
    | 共有 Worker | 1 | Standard_A1 - (1 コア、1792 MB) | Web または API アプリケーション、および Azure Functions アプリをホストします。 より多くのインスタンスの追加が必要になる場合があります。 オペレーターは、サービスを定義することや任意の SKU レベルを選ぶことができます。 レベルには、少なくとも 1 つのコアが必要です。 |

    ![App Service インストーラー](media/azure-stack-app-service-deploy/image08.png)    

    > [!NOTE]
    > テクニカル プレビューでは、App Service リソース プロバイダー インストーラーにより、Azure Resource Manager をサポートする単純なファイル サーバーとして機能するように 1 つの Standard A1 インスタンスもデプロイされます。 このインスタンスは、単一ノード開発キット用に残されています。 実稼働ワークロードについては、一般提供開始時に、App Service インストーラーにより、高可用性ファイル サーバーを使うことができるようになります。

13. **[プラットフォーム イメージの選択]** ボックスで、App Service クラウド用のコンピューティング リソースプロバイダーで選択可能な項目の中から、ご自分のデプロイの Windows Server 2016 仮想マシン イメージを選びます。 **[次へ]** をクリックします。

14. 次のページで、次の操作を行います。
     1. Worker ロールの仮想マシン管理者のユーザー名とパスワードを入力します。
     2. その他のロールの仮想マシン管理者のユーザー名とパスワードを入力します。
     3. **[次へ]** をクリックします。

    ![App Service インストーラー](media/azure-stack-app-service-deploy/image09.png)    

15. 概要ページで、次のことを行います。
    1. 選択した内容を確認します。 変更を加えるには、**[前へ]** を使って前のページに戻ります。
    2. 構成が正しい場合は、チェック ボックスをオンにします。
    3. デプロイを始めるには、**[次へ]** をクリックします。

    ![App Service インストーラー](media/azure-stack-app-service-deploy/image10.png)    

16. 次のページで、次の操作を行います。
    1. インストールの進行状況を追跡します。 App Service on Azure Stack を既定の選択内容に基づいてデプロイするには、60 分ほどかかります。
    2. インストーラーが正常に完了したら、**[終了]** をクリックします。

    ![App Service インストーラー](media/azure-stack-app-service-deploy/image11.png)    


## <a name="validate-the-app-service-on-azure-stack-installation"></a>App Service on Azure Stack インストールを検証する

1. Azure Stack 管理ポータルで、インストーラーによって作られたリソース グループを参照します。 既定では、このグループは **APPSERVICE-LOCAL** です。

2. **CN0-VM** を見つけます。 仮想マシンに接続するには、**[仮想マシン]** ブレードで **[接続]** をクリックします。

3. この VM のデスクトップで、**[Web Cloud Management Console]\(Web クラウド管理コンソール\)** をダブルクリックします。

4. **[管理されたサーバー]** に移動します。

5. すべてのマシンで、1 つ以上の Worker について **[準備完了]** と表示されたら、手順 6 に進みます。

6. リモートのデスクトップ マシンを終了して、App Service インストーラーを実行したマシンに戻ります。

    ![App Service インストーラー](media/azure-stack-app-service-deploy/managed-servers.png)    


## <a name="test-drive-app-service-on-azure-stack"></a>App Service on Azure Stack を試してみる

App Service リソースプロバイダーをデプロイして登録したら、テストしてユーザーが Web アプリと API アプリをデプロイできることを確認します。

> [!NOTE]
> プラン内に Microsoft.Web 名前空間があるサービスを作る必要があります。 その後、このオファーをサブスクライブするテナント サブスクリプションが必要となります。 詳しくは、「[Azure Stack でのオファーの作成](azure-stack-create-offer.md)」および「[Azure Stack でのプランの作成](azure-stack-create-plan.md)」をご覧ください。
>
App Service on Azure Stack を使うアプリケーションを作るには、テナント サブスクリプションが*必要です*。 管理ポータル内でサービス管理者が実行できる機能は、App Service のリソースプロバイダー管理に関連するものだけです。 これらの機能には、容量の追加、デプロイ ソースの構成、Worker 層と SKU の追加などがあります。
>
3 番目のテクニカル プレビューの時点では、Web アプリ、API アプリ、Azure Functions アプリを作るには、テナント ポータルを使う必要があり、テナント サブスクリプションがある必要があります。

1. Azure Stack テナント ポータルで、**[新規]**  >  **[Web + モバイル]**  >  **[Web アプリ]** の順にクリックします。

2. **[Web アプリ]** ブレードで、**[Web アプリ]** ボックスに名前を入力します。

3. **[リソース グループ]** の下にある **[新規]** をクリックします。 **[リソース グループ]** ボックスに名前を入力します。

4. **[App Service プラン/場所]** > **[新規作成]** の順にクリックします。

5. **[App Service プラン]** ブレードで、**[App Service プラン]** ボックスに名前を入力します。

6. **[価格レベル]**  >  **[Free-Shared]\(無料 - 共有\)** または **[Shared-Shared]\(共有 - 共有\)**  >  **[選択]**  >  **[OK]**  >  **[作成]** の順にクリックします。

7. 1 分以内に、ダッシュボードに新規 Web アプリのタイルが表示されます。 タイルをクリックします。

8. **[Web アプリ]** ブレードで、**[参照]** をクリックして、このアプリの既定の Web サイトを表示します。

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>WordPress、DNN、Django の Web サイトをデプロイする (省略可能)

1. Azure Stack テナント ポータルで、**[+]** をクリックして Azure Marketplace にアクセスし、Django Web サイトをデプロイして、正常に完了するまで待ちます。 Django Web プラットフォームでは、ファイル システム ベースのデータベースが使われます。 SQL や MySQL など、追加のリソース プロバイダーは必要ありません。

2. MySQL リソース プロバイダーもデプロイした場合は、Marketplace から WordPress Web サイトをデプロイできます。 データベース パラメーターを求められたら、お好きなユーザー名とサーバー名で、ユーザー名を *User1@Server1* の形式で入力します。

3. SQL Server リソース プロバイダーもデプロイした場合は、Marketplace から DNN Web サイトをデプロイできます。 データベース パラメーターを求められたら、使用しているリソース プロバイダーに接続された SQL Server を実行しているコンピューター内のデータベースを選びます。

## <a name="next-steps"></a>次のステップ

その他の [Platform as a Service (PaaS) サービス](azure-stack-tools-paas-services.md)を試してみることもできます。

- [SQL Server リソースプロバイダー](azure-stack-sql-resource-provider-deploy.md)
- [MySQL リソースプロバイダー](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
