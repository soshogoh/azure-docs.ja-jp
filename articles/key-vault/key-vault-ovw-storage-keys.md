---
ms.assetid: ''
title: Azure Key Vault の管理対象ストレージ アカウント - CLI
description: ストレージ アカウント キーは、Azure Key Vault と Azure Storage アカウントへのキー ベースのアクセス間をシームレスに統合します。
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: prashanthyv
ms.author: pryerram
manager: barbkess
ms.date: 10/03/2018
ms.openlocfilehash: 9b1a4e23ed0da0637b44ac52dd4d1baeb22cd6ce
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118056"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Azure Key Vault の管理対象ストレージ アカウント - CLI

> [!NOTE]
> [現在、Azure ストレージと Azure Active Directory (Azure AD) の統合はプレビュー段階です](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)。 認証と承認には、Azure Key Vault と同様に、Azure ストレージへの OAuth2 トークンベースのアクセスを提供する Azure AD を使用することをお勧めします。 こうすることで以下の操作が可能になります。
> - ストレージ アカウントの資格情報ではなく、アプリケーションまたはユーザーの ID を使用してクライアント アプリケーションを認証する。 
> - Azure 上で実行する場合に [Azure AD マネージド ID](/azure/active-directory/managed-identities-azure-resources/) を使用する。 マネージド ID を使用すると、クライアント認証を併用し、アプリケーションに資格情報を保存する必要がなくなります。
> - 承認の管理にロール ベースのアクセス制御 (RBAC) を使用する (これも Key Vault でサポートされています)。

- Azure Key Vault は、Azure Storage アカウント (ASA) のキーを管理します。
    - 内部的には、Azure Key Vault では、Azure Storage アカウントを使用してキーの一覧表示 (同期) ができます。    
    - Azure Key Vault は定期的にキーを再生成 (回転) します。
    - キーの値は、呼び出し元に応答で返されることはありません。
    - Azure Key Vault では、ストレージ アカウントと従来のストレージ アカウントの両方のキーを管理します。
    
> [!IMPORTANT]
> Azure AD テナントからは、登録された各アプリケーションに**[サービス プリンシパル](/azure/active-directory/develop/developer-glossary#service-principal-object)** が提供されます。これはアプリケーションの ID として機能します。 このサービス プリンシパルのアプリケーション ID は、ロールベースのアクセス制御 (RBAC) を介して、他の Azure リソースにアクセスするための承認を与えるときに使用されます。 Key Vault は Microsoft アプリケーションなので、各 Azure クラウド内のすべての Azure AD テナントでは、次のように同じアプリケーション ID で事前登録されています。
> - Azure Government の場合、Azure AD テナントにはアプリケーション ID `7e7c393b-45d0-48b1-a35e-2905ddf8183c` が使用される可能性があります。
> - Azure パブリック クラウドとその他すべてのクラウドの場合、Azure AD テナントにはアプリケーション ID `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` が使用されます。

<a name="prerequisites"></a>前提条件
--------------
1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) Azure CLI をインストールする   
2. [ストレージ アカウントを作成する](https://azure.microsoft.com/services/storage/)
    - この[ドキュメント](https://docs.microsoft.com/azure/storage/)に載っている手順に従って、ストレージ アカウントを作成してください。  
    - **名前付けのガイダンス:** ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Key Vault を使用してストレージ アカウント キーを管理する手順
--------------------------------------------------------------------------------
概念上は、次に示す手順に従います。
- 最初に (既存の) ストレージ アカウントを取得します。
- 次に、(既存の) キー コンテナーをフェッチします。
- 次に、Key Vault マネージド ストレージ アカウントをコンテナーに追加します。Key1 をアクティブ キーとして設定し、再生成期間は 180 日にします。
- 最後に、指定したストレージ アカウント用のストレージ コンテキストを Key1 を使用して設定します。

以下の手順では、ストレージ アカウントに対するオペレーター アクセス許可を持つサービスとして Key Vault を割り当てます。

> [!NOTE]
> いったん Azure Key Vault マネージド ストレージ アカウント キーを設定した後は、Key Vault を使用しないでキーを変更しては**ならない**ことに注意してください。 マネージド ストレージ アカウント キーとは、Key Vault によってストレージ アカウント キーのローテーションが管理されることを意味します

1. ストレージ アカウントの作成後に、次のコマンドを実行して、管理するストレージ アカウントのリソース ID を取得します。

    ```
    az storage account show -n storageaccountname 
    ```
    上記のコマンドの結果から ID フィールドをコピーします。
    
2. 以下のコマンドを実行して、Azure Key Vault のサービス プリンシパルのオブジェクト ID を取得します。

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
    このコマンドが正常に完了すると、結果にオブジェクト ID が含まれます。
    ```console
        {
            ...
            "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
            ...
        }
    ```
    
3. Azure Key Vault ID に Storage Key Operator ロールを割り当てます。

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. Key Vault の管理対象ストレージ アカウントを作成します。     <br /><br />
   次の例では、再生成期間を 90 日に設定しています。 90 日後、Key Vault は "key1" を再生成し、アクティブ キーを "key2" から "key1" に切り替えます。 Key1 にアクティブ キーのマークが付きます。 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```
    ユーザーがストレージ アカウントを作成しておらず、ストレージ アカウントへのアクセス許可もない場合に備えて、以下の手順で自分のアカウントのアクセス許可を設定して、Key Vault 内のすべてのストレージ アクセス許可を確実に管理できるようにします。
    

<a name="step-by-step-instructions-on-how-to-use-key-vault-to-create-and-generate-sas-tokens"></a>Key Vault を使用して SAS トークンを生成する手順
--------------------------------------------------------------------------------
Key Vault を使用して SAS (Shared Access Signature) トークンを生成することもできます。 Shared Access Signature を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 SAS を使用すると、アカウント キーを共有することなく、ストレージ アカウントのリソースへのアクセス権をクライアントに付与できます。 これは、アプリケーションで Shared Access Signature を使用する際の重要な点になります。SAS は、アカウント キーを損なうことなく、ストレージ リソースを共有する安全な方法です。

上記の手順を完了すると、以下のコマンドを実行して Key Vault で SAS トークンを生成できます。 

以下の手順で行われる処理は次のとおりです。
- コンテナー "<VaultName>" 内の Key Vault マネージド ストレージ アカウント "<YourStorageAccountName>" に "<YourSASDefinitionName>" という名前のアカウントの SAS 定義を設定します。 
- サービス、コンテナー、オブジェクトの各リソースの種類に対して、BLOB、ファイル、テーブル、キューの各サービス用のアカウントの SAS トークンを作成します。すべてのアクセス許可を付与し、https を使用し、開始日と終了日を指定します。
- Key Vault マネージド ストレージの SAS 定義をコンテナーに設定します。上記で作成した SAS トークンをテンプレート URI として指定し、SAS の種類は "account" にして、有効期間は N 日にします。
- SAS 定義に対応する KeyVault シークレットから実際のアクセス トークンを取得します。

1. この手順では、SAS 定義を作成します。 この SAS 定義が作成されたら、Key Vault を使用して追加の SAS トークンを生成できます。 この操作には storage/setsas アクセス許可が必要です。

```
$sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
```
上記の操作に関するその他のヘルプを[ここ](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas)で確認できます。

この操作が正常に実行されると、次のような出力が表示されます。 これをコピーしてください。

```console
   "se=2020-01-01&sp=***"
```

2. この手順では、上記で生成された出力 ($sasToken) を使用して SAS 定義を作成します。 その他のドキュメントについては、[ここ](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters)を参照してください。   

```
az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
```
                        

 > [!NOTE] 
 > ユーザーにストレージ アカウントに対するアクセス許可がない場合に備えて、最初にユーザーのオブジェクト ID を取得します。

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    
    ```
    
## <a name="fetch-sas-tokens-in-code"></a>SAS トークンをコードにフェッチする

このセクションでは、Key Vault から [SAS トークン](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)をフェッチすることによって、ストレージ アカウントに対する操作を実行する方法を説明します

以下のセクションでは、上記のように SAS 定義を作成した後で SAS トークンをフェッチする方法を示します。

> [!NOTE] 
  [基本的な概念](key-vault-whatis.md#basic-concepts)に記載されているように、次の 3 つの方法で Key Vault に対する認証ができます
> - マネージド サービス ID の使用 (強くお勧めします)
> - サービス プリンシパルと証明書の使用 
> - サービス プリンシパルとパスワードの使用 (推奨されません)

```cs
// Once you have a security token from one of the above methods, then create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault. SecretUri is of the format https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

SAS トークンの期限がまもなく切れる場合は、SAS トークンを再度 Key Vault からをフェッチし、コードを更新してください

```cs
// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

### <a name="relevant-azure-cli-commands"></a>関連する Azure CLI コマンド

[Azure CLI ストレージ コマンド](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

## <a name="see-also"></a>関連項目

- [キー、シークレット、証明書について](https://docs.microsoft.com/rest/api/keyvault/)
- [Key Vault チーム ブログ](https://blogs.technet.microsoft.com/kv/)
