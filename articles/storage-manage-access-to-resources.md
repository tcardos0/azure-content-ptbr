﻿<properties 
	pageTitle="Gerenciar o acesso aos recursos de Armazenamento do Azure" 
	description="Saiba mais sobre as diferentes maneiras que você pode gerenciar o acesso aos recursos de armazenamento do Azure." 
	services="storage" 
	documentationCenter="" 
	authors="micurd,tamram" 
	manager="jahogg" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="micurd"/>

# Gerenciar o acesso aos recursos de Armazenamento do Azure

## Visão geral

Por padrão, somente o proprietário da conta de armazenamento pode acessar blobs, tabelas e filas nessa conta. Se seu serviço ou aplicativo precisar tornar esses recursos disponíveis para outros clientes sem compartilhar sua chave de acesso, você tem as seguintes opções para permitir acesso:

- Definir permissões do contêiner para permitir o acesso de leitura anônimo para o contêiner e seus blobs. Isso não é permitido para tabelas ou filas.

- Você pode expor um recurso através de uma assinatura de acesso compartilhado, que permite que você delegue acesso restrito a um contêiner, blob, tabela ou recurso de fila, especificando o intervalo no qual os recursos estarão disponíveis e as permissões que um cliente terá.

- Você pode usar uma política de acesso armazenado para gerenciar assinaturas de acesso compartilhado para um contêiner ou seus blobs, uma fila ou uma tabela. A política de acesso armazenado fornece uma medida adicional de controle sobre suas assinaturas de acesso compartilhado e também fornece um meio simples de revogá-los.

## Restringir o acesso aos contêineres e blobs

Por padrão, um contêiner e todos os blobs dentro dele podem ser acessados somente pelo proprietário da conta de armazenamento. Se você quiser dar aos usuários anônimos permissões de leitura a um contêiner e seus blobs, é possível definir as permissões de contêiner para permitir acesso público. Usuários anônimos podem ler blobs dentro de um contêiner publicamente acessível sem autenticar a solicitação.

Os contêineres fornecem as seguintes opções para gerenciar o acesso do contêiner:

- Acesso de leitura público completo: Os dados do contêiner e do blob podem ser lidos por solicitação anônima. Os clientes podem enumerar os blobs no contêiner por meio de uma solicitação anônima, mas não podem enumerar os contêineres em uma conta de armazenamento.

- Acesso de leitura público para somente para blobs: Os dados do blob nesse contêiner podem ser lidos por meio de solicitação anônima, mas os dados do contêiner não estão disponíveis. Os clientes não podem enumerar os blobs no contêiner por meio de uma solicitação anônima.

- Nenhum acesso de leitura público: Dados de contêiner e blob podem ser lidos apenas pelo proprietário da conta.

>[AZURE.NOTE]Se seu serviço exigir que você exerça controle mais granular sobre recursos de blob ou se você quiser fornecer permissões para operações diferentes de operações de leitura, é possível usar uma Assinatura de Acesso Compartilhado para tornar um recurso acessível aos usuários. 

### Recursos disponíveis para usuários anônimos
A tabela a seguir mostra quais operações podem ser chamadas por usuários anônimos quando a ACL do contêiner é definida para permitir acesso público.

| Operação REST | Permissão com acesso de leitura público completo | Permissão de acesso de leitura público para blobs apenas |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| Listar contêineres | Somente o proprietário | Somente o proprietário                                         |
| Criar Contêiner                                       | Somente o proprietário                              | Somente o proprietário                                        |
| Obter propriedades de contêiner | Todos                                     | Somente o proprietário                                        |
| Obter Metadados do Contêiner | Todos                                     | Somente o proprietário                                        |
| Definir Metadados do Contêiner                                 | Somente o proprietário | Somente o proprietário                                        |
| Obter ACL do Contêiner | Somente o proprietário | Somente o proprietário                                        |
| Definir ACL do Contêiner | Somente o proprietário | Somente o proprietário                                        |
| Excluir Contêiner | Somente o proprietário | Somente o proprietário                                        |
| Listar Blobs                                             | Todos                                     | Somente o proprietário                                        |
| Colocar Blob                                               | Somente o proprietário                               | Somente o proprietário                                        |
| Obter Blob                                               | Todos                                     | Todos                                             |
| Obter Propriedades do Blob | Todos                                     | Todos                                               |
| Definir Propriedades do Blob | Somente o proprietário | Somente o proprietário                                       |
| Obter Metadados do Blob | Todos                                     | Todos                                             |
| Definir Metadados do Blob | Somente o proprietário | Somente o proprietário                                        |
| Colocar Bloco                                              | Somente o proprietário                              | Somente o proprietário                                        |
| Obter Lista de Blocos (somente blocos confirmados) | Todos                                     | Todos                                               |
| Obter Lista de Blocos (somente blocos não confirmados ou todos os blocos) | Somente o proprietário | Somente o proprietário                                        |
| Colocar Lista de Blocos | Somente o proprietário | Somente o proprietário                                        |
| Excluir Blob                                            | Somente o proprietário | Somente o proprietário                                        |
| Copiar Blob                                              | Somente o proprietário | Somente o proprietário                                        |
| Blob de Instantâneo | Somente o proprietário | Somente o proprietário                                        |
| Blob de Concessão                                             | Somente o proprietário | Somente o proprietário                                        |
| Colocar Página                                               | Somente o proprietário | Somente o proprietário                                        |
| Obter Intervalos de Página | Todos                                     | Todos                                                  |

## Criar e usar uma assinatura de acesso compartilhado
Uma assinatura de acesso compartilhado é um URI que concede direitos de acesso restrito para contêineres, blobs, filas e tabelas por um intervalo de tempo específico. Fornecendo um cliente com uma assinatura de acesso compartilhado, você poderá habilitá-los a acessar recursos na sua conta de armazenamento sem compartilhar sua chave de conta com eles.

>[AZURE.NOTE] Para uma visão geral conceitual aprofundada e um tutorial sobre assinaturas de acesso compartilhado, consulte [Assinaturas de acesso compartilhado](storage-dotnet-shared-access-signature-part-1.md).

As operações com suporte para uso de assinaturas de acesso compartilhado incluem:

- Ler e gravar o conteúdo de blob de páginas ou de blocos, listas de blocos, propriedades e metadados

- Exclusão, aluguel e criação de um instantâneo de um blob

- Listagem de blobs em um contêiner

- Adicionar, remover, atualizar e excluir mensagens da fila (na Biblioteca Cliente de Armazenamento 2.0 e mais recente)

- Obter metadados de fila, incluindo a contagem de mensagens (na Biblioteca Cliente de Armazenamento 2.0 e mais recente)

- Consultar, adicionar, atualizar, excluir e inserir entidades de tabela (na Biblioteca Cliente de Armazenamento 2.0 e mais recente)

Os parâmetros de consulta URI de assinatura de acesso compartilhado incorporam todas as informações necessárias para conceder acesso controlado a um recurso de armazenamento. Os parâmetros de consulta URI especificam o intervalo de tempo no qual a assinatura de acesso compartilhado é válida, as permissões concedidas por ela, o recurso que deve ser disponibilizado e a assinatura que os serviços de armazenamento devem usar para autenticar a solicitação.

Além disso, o URI da assinatura de acesso compartilhado pode fazer referência a uma política de acesso armazenado que fornece um nível adicional de controle sobre um conjunto de assinaturas, incluindo a capacidade de modificar ou revogar acesso ao recurso, se necessário. 

Para obter informações sobre o formato de URI de uma assinatura de acesso compartilhado, consulte [Delegando acesso com uma assinatura de acesso compartilhado](https://msdn.microsoft.com/library/ee395415.aspx).

### Uso seguro de assinaturas de acesso compartilhado
Uma assinatura de acesso compartilhado concede acesso ao recurso especificado pelas permissões concedidas do URI. Você deve sempre usar HTTPS para construir um URI de assinatura de acesso compartilhado. Usando HTTP com assinaturas de acesso compartilhado pode tornar sua conta de armazenamento vulnerável ao uso mal-intencionado.

Se uma assinatura de acesso compartilhado concede acesso que não se destina ao público em geral, ela deve ser construída com o menor número possível de permissões. Além disso, uma assinatura de acesso compartilhado deve ser distribuída seguramente aos clientes em uma conexão segura, deve estar associada uma política de acesso armazenado para fins de revogação e deve especificar o menor tempo de vida possível para a assinatura.

>[AZURE.NOTE] Um URI de assinatura de acesso compartilhado é associado com a chave de conta usada para criar a assinatura e a política de acesso armazenado associada (se houver). Se nenhuma política de acesso armazenado for especificada, a única maneira de revogar uma assinatura de acesso compartilhado é alterar a chave da conta. 

### Criar uma assinatura de acesso compartilhado
O exemplo de código a seguir cria uma política de acesso em um contêiner e gera uma assinatura de acesso compartilhado para o contêiner. Essa assinatura de acesso compartilhado pode ser oferecida a clientes:

    // A cadeia de conexão para a conta de armazenamento.  Modificação para sua conta.
    string storageConnectionString =
       "DefaultEndpointsProtocol=https;" +
       "AccountName=myaccount;" +
       "AccountKey=<account-key>";
    
    // Como alternativa, você pode recuperar informações da conta de armazenamento de um arquivo app.config. 
    Essa é uma maneira de armazenar e recuperar uma cadeia de conexão se você estiver 
    // escrevendo um aplicativo que será executado localmente, em vez de no Microsoft Azure.
    
    // string storageConnectionString = ConfigurationManager.AppSettings["StorageAccountConnectionString"];
    
    // Crie a conta de armazenamento com a cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);
       
    // Crie o objeto de cliente do blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Obtenha uma referência para o contêiner para o qual a assinatura de acesso compartilhado será criada.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    container.CreateIfNotExists();
    
    // Crie permissões do contêiner do blob, que consistem em uma política de acesso compartilhado 
    // e uma configuração de acesso público. 
    BlobContainerPermissions blobPermissions = new BlobContainerPermissions();
    
    // A política de acesso compartilhado fornece 
    // acesso de leitura e gravação ao contêiner por 10 horas.
    blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
    {
       // Para garantir que o SAS seja válido imediatamente, não defina a hora de início.
       // Dessa forma, você pode evitar falhas causadas por pequenas diferenças no relógio.
       SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
       Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
    });
    
    // A configuração de acesso público especifica explicitamente que 
    // o contêiner é privado, de modo que não pode ser acessado anonimamente.
    blobPermissions.PublicAccess = BlobContainerPublicAccessType.Off;
    
    // Defina a política de permissão no contêiner.
    container.SetPermissions(blobPermissions);
    
    // Obtenha a assinatura de acesso compartilhado para compartilhar com usuários.
    string sasToken =
       container.GetSharedAccessSignature(new SharedAccessBlobPolicy(), "mypolicy");

### Usando uma assinatura de acesso compartilhado
Um cliente que recebe uma assinatura de acesso compartilhado pode usá-la no seu código para construir um objeto do tipo [StorageCredentials](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.auth.storagecredentials.aspx). Essas credenciais podem então ser usadas para construir um objeto [CloudStorageAccount](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.cloudstorageaccount.aspx) ou [CloudBlobClient](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx) para trabalhar com o recurso, como mostra este exemplo:

    Uri blobUri = new Uri("https://myaccount.blob.core.windows.net/mycontainer/myblob.txt");
    
    // Crie credenciais com o token SAS. O token SAS foi criado no exemplo anterior.
    StorageCredentials credentials = new StorageCredentials(sasToken);
    
    // Crie um novo blob.
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, credentials);
    
    // Carregar o blob. 
    // Se o blob ainda não existir, ele será criado. 
    // Se o blob existir, seu conteúdo existente será substituído.
    using (var fileStream = System.IO.File.OpenRead(@"c:\Test\myblob.txt"))
    {
    blob.UploadFromStream(fileStream);
    }

## Usar uma política de acesso armazenado
Uma política de acesso armazenado fornece um nível adicional de controle sobre assinaturas de acesso compartilhado no lado do servidor. Estabelecer uma política de acesso armazenado serve para agrupar assinaturas de acesso compartilhado e fornecer restrições adicionais para assinaturas que são associadas pela política. Você pode usar uma política de acesso armazenada para alterar a hora de início, a hora de término ou as permissões para uma assinatura ou para revogá-la depois que tiver sido emitida.

Uma política de acesso armazenado proporciona maior controle sobre assinaturas de acesso compartilhado que você lançou. Em vez de especificar as permissões e o tempo de vida da assinatura na URL, você pode especificar esses parâmetros dentro da política de acesso armazenado no blob, contêiner, fila ou tabela que está sendo compartilhada. Para alterar esses parâmetros para uma ou mais assinaturas, você pode modificar a política de acesso armazenado, em vez de emitir novamente as assinaturas. Você também pode revogar rapidamente a assinatura modificando a política de acesso armazenado.

Por exemplo, suponha que você tenha emitido uma assinatura de acesso compartilhado associada a uma política de acesso armazenado. Se você tiver especificado o tempo de término na política de acesso armazenado, é possível modificar a política de acesso para aumentar a vida da assinatura, sem precisar reemitir uma nova assinatura.

Práticas recomendadas aconselham especificar uma política de acesso armazenado para qualquer recurso assinado para o qual você esteja emitindo uma assinatura de acesso compartilhado, porque a política armazenada pode ser usada para modificar ou revogar a assinatura depois que ela tiver sido emitida. Se você não especificar uma política armazenada, é recomendável que você limite o tempo de vida da sua assinatura para minimizar qualquer risco aos seus recursos da conta de armazenamento. 

### Associar uma assinatura de acesso compartilhado a uma política de acesso armazenado
Uma política de acesso armazenado inclui um nome de até 64 caracteres que é exclusivo dentro do contêiner, fila ou tabela. Para associar uma assinatura de acesso compartilhado com uma política de acesso armazenado, você especificar esse identificador ao criar a assinatura de acesso compartilhado. No URI de assinatura de acesso compartilhado, o *signedidentifier* campo especifica o identificador da política de acesso armazenado.

Um contêiner, fila ou tabela pode incluir até 5 políticas de acesso armazenado. Cada política pode ser usada por qualquer número de assinaturas de acesso compartilhado.

>[AZURE.NOTE]Quando você estabelece uma política de acesso armazenado em um contêiner, fila ou tabela, ela pode levar até 30 segundos para entrar em vigor. Durante esse intervalo, uma assinatura de acesso compartilhado que está associada com a política de acesso armazenado falhará com o código de status 403 (proibido) até que a política de acesso se torne ativa.

### Especificando parâmetros da política de acesso para uma política de acesso compartilhado
A política de acesso armazenado pode especificar os seguintes parâmetros da política de acesso para as assinaturas às quais ela está associada:

- Hora de início

- Hora de expiração

- Permissões

Dependendo de como você deseja controlar o acesso ao recurso de armazenamento, é possível especificar todos esses parâmetros na política de acesso armazenado e omitir da URL para a assinatura de acesso compartilhado. Isso permite modificar o comportamento da assinatura associada a qualquer momento, bem como revogá-lo. Ou é possível especificar um ou mais dos parâmetros da política de acesso na política de acesso armazenada e os outros na URL. Finalmente, você pode especificar todos os parâmetros na URL. Nesse caso, você pode usar a política de acesso armazenado para revogar a assinatura, mas não para modificar seu comportamento.

Juntas, a assinatura de acesso compartilhado e a política de acesso armazenado devem incluir todos os campos necessários para autenticar a assinatura. Se os campos necessários estiverem ausentes, a solicitação falhará com o código de status 403 (proibido). Da mesma forma, se um campo for especificado na URL da assinatura de acesso compartilhado e na política de acesso armazenado, a solicitação falhará com o código de status 403 (solicitação incorreta). Consulte Criar e usar uma assinatura de acesso compartilhado para obter mais informações sobre os campos que compõem a assinatura.

### Modificando ou revogando uma política de acesso armazenado

Para revogar o acesso a assinaturas de acesso compartilhado que usam a mesma política de acesso armazenado, remova a política armazenada do recurso de armazenamento substituindo a lista de políticas armazenadas por uma nova lista que não contém o nome da política. Para alterar as configurações de acesso de uma política de acesso armazenado, substitua a lista de políticas armazenadas por uma nova lista que contenha uma política de mesmo nome que tenha detalhes do novo controle de acesso.


<!--HONumber=52-->