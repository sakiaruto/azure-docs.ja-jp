---
title: "Azure CLI を使用した Azure Database for MySQL ファイアウォール規則の作成と管理 | Microsoft Docs"
description: "この記事では、Azure CLI コマンド ラインを使って Azure Database for MySQL ファイアウォール規則を作成し、管理する方法について説明します。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: bc7763981c27a3d37cc1bd16c0f8efc0b4c01ce0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Azure CLI を使用した Azure Database for MySQL ファイアウォール規則の作成と管理
サーバーレベルのファイアウォール規則を使用すると、管理者は特定の IP アドレスまたは IP アドレス範囲からの Azure Database for MySQL サーバーへのアクセスを管理できます。 便利な Azure CLI コマンドを使用すると、サーバーを管理するためのファイアウォール規則の作成、更新、削除、一覧化、表示などができます。 Azure Database for PostgreSQL ファイアウォールの概要については、「[Azure Database for MySQL サーバーのファイアウォール規則](./concepts-firewall-rules.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件
* [Azure CLI 2.0 をインストールする](https://docs.microsoft.com/cli/azure/install-azure-cli)。
* PostgreSQL および MySQL サービス用の Azure Python SDK をインストールする。
* PostgreSQL および MySQL サービス用の Azure CLI コンポーネントをインストールする。
* Azure Database for MySQL サーバーを作成します。

## <a name="firewall-rule-commands"></a>ファイアウォール規則のコマンド:
Azure CLI の **az mysql server firewall-rule** コマンドで、ファイアウォール規則を作成、削除、一覧表示、表示、更新します。

コマンド:
- **create**: Azure MySQL サーバーのファイアウォール規則を作成します。
- **delete**: Azure MySQL サーバーのファイアウォール規則を削除します。
- **list**: Azure MySQL サーバーのファイアウォール規則を一覧表示します。
- **show**: Azure MySQL サーバーのファイアウォール規則の詳細を表示します。
- **update**: Azure MySQL サーバーのファイアウォール規則を更新します。

## <a name="log-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Azure にログインして Azure Database for MySQL サーバーを一覧表示する
**az login** コマンドを使用して、ご利用の Azure アカウントで Azure CLI に安全に接続します。

1. コマンド ラインから次のコマンドを実行します。
```azurecli
az login
```
このコマンドにより、次の手順で使用するコードが出力されます。

2. Web ブラウザーを使用してページ [https://aka.ms/devicelogin](https://aka.ms/devicelogin) を開き、コードを入力します。

3. プロンプトで、Azure 資格情報を使用してログインします。

4. ログインの認証が完了すると、サブスクリプションの一覧がコンソールに出力されます。 目的のサブスクリプションの ID をコピーして、使用する現在のサブスクリプションを設定します。
   ```azurecli-interactive
   az account set --subscription {your subscription id}
   ```

5. サブスクリプションとリソース グループの名前がわからない場合は、Azure Databases for MySQL サーバーを一覧表示します。

   ```azurecli-interactive
   az mysql server list --resource-group myResourceGroup
   ```

   この一覧に表示される名前属性を確認します。これは、使用する MySQL サーバーを指定するために必要です。 必要に応じて、そのサーバーの詳細を確認し、名前属性を使用して正しいかどうかを確認します。

   ```azurecli-interactive
   az mysql server show --resource-group myResourceGroup --name mysqlserver4demo
   ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーのファイアウォール規則を一覧表示する 
サーバー名とリソース グループ名を使用して、そのサーバー上で既存のサーバー ファイアウォール規則を一覧表示します。 サーバー名属性は、**--name** スイッチではなく **--server** スイッチで指定されることに注意してください。
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo
```
規則がある場合は、JSON 形式 (既定) で出力として一覧表示されます。 **--output table** スイッチを使用すると、結果をよりわかりやすい表形式で出力できます。
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーのファイアウォール規則を作成する
Azure MySQL サーバー名とリソース グループ名を使用して、サーバーに新しいファイアウォール規則を作成します。 規則の名前に加え、(IP アドレス範囲へのアクセスを提供するための) 開始 IP と終了 IP を指定します。
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
1 つの IP アドレスに対するアクセスを許可するには、次の例のように、開始 IP と終了 IP に同じ IP アドレスを指定します。
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  
--server mysql --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
正常に完了すると、コマンドの出力として、作成したファイアウォール規則の詳細が JSON 形式 (既定) で一覧表示されます。 失敗した場合は、代わりにエラー メッセージ テキストが出力されます。

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーのファイアウォール規則を更新する 
Azure MySQL サーバー名とリソース グループ名を使用して、サーバーの既存のファイアウォール規則を更新します。 入力として既存のファイアウォール規則の名前に加え、更新する開始 IP と終了 IP 属性を指定します。
```azurecli-interactive
az mysql server firewall-rule update --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
正常に完了すると、コマンドの出力として、更新したファイアウォール規則の詳細が JSON 形式 (既定) で一覧表示されます。 失敗した場合は、代わりにエラー メッセージ テキストが出力されます。

> [!NOTE]
> ファイアウォール規則が存在しない場合は、更新コマンドによって規則が作成されます。

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーのファイアウォール規則の詳細を表示する
Azure MySQL サーバー名とリソース グループ名を使用して、サーバーの既存のファイアウォール規則の詳細を表示します。 既存のファイアウォール規則の名前を入力します。
```azurecli-interactive
az mysql server firewall-rule show --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
正常に完了すると、コマンドの出力として、指定したファイアウォール規則の詳細が JSON 形式 (既定) で一覧表示されます。 失敗した場合は、代わりにエラー メッセージ テキストが出力されます。

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーのファイアウォール規則を削除する
Azure MySQL サーバー名とリソース グループ名を使用して、サーバーの既存のファイアウォール規則を削除します。 既存のファイアウォール規則の名前を入力します。
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
正常に完了すると、出力はありません。 失敗した場合は、エラー メッセージ テキストが表示されます。

## <a name="next-steps"></a>次のステップ
- 詳細については、「[Azure Database for MySQL サーバーのファイアウォール規則](./concepts-firewall-rules.md)」を参照してください。
- [Azure Portal を使用した Azure Database for MySQL ファイアウォール規則の作成と管理](./howto-manage-firewall-using-portal.md)