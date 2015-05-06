﻿<properties 
   pageTitle="Segurança StorSimple" 
   description="Descreve os recursos de segurança e privacidade que protegem o serviço, os dispositivos e os dados StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="AdinaH" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="03/19/2015"
   ms.author="v-sharos"/>

# Segurança StorSimple 

## Visão geral

A segurança é uma preocupação importante para qualquer pessoa que esteja adotando uma nova tecnologia, especialmente quando a tecnologia é usada com dados confidenciais ou proprietários. Ao avaliar as diferentes tecnologias, você deve considerar o aumento dos riscos e dos custos para a proteção dos dados. O Microsoft Azure StorSimple fornece uma solução tanto de segurança quanto de privacidade para proteção de dados, ajudando a garantir: 

- **Confidencialidade** - somente entidades autorizadas podem exibir seus dados.
- **Integridade** - apenas as entidades autorizadas podem modificar ou excluir seus dados.

A solução Microsoft Azure StorSimple consiste em quatro componentes principais que interagem entre si:

- **Serviço do gerenciador do StorSimple hospedado no Microsoft Azure** - o serviço de gerenciamento que você usa para configurar e provisionar o dispositivo StorSimple.
- **Dispositivo StorSimple** - um dispositivo físico instalado no datacenter. Todos os hosts e clientes que geram dados conectam-se ao dispositivo StorSimple, e o dispositivo gerencia os dados e move-os na nuvem do Azure conforme apropriado.
- **Clientes/hosts conectados ao dispositivo** - os clientes em sua infraestrutura que se conectam ao dispositivo StorSimple e geram dados que precisam ser protegidos.
- **Armazenamento em nuvem** - o local na nuvem do Azure em que os dados são armazenados.

As seções a seguir descrevem os recursos de segurança do StorSimple que ajudam a proteger cada um desses componentes e os dados armazenados neles. Também incluem uma lista de perguntas sobre segurança do Microsoft Azure StorSimple e as respostas correspondentes.

## Proteção de serviço do gerenciador do StorSimple

O Serviço do gerenciador do StorSimple é um serviço de gerenciamento hospedado no Microsoft Azure usado para gerenciar todos os dispositivos StorSimple que sua organização adquiriu. Você pode acessar o serviço do Gerenciador do StorSimple usando suas credenciais organizacionais para fazer logon no Portal de Gerenciamento do Azure por meio de um navegador da Web. 

O acesso ao serviço do Gerenciador do StorSimple requer que a organização tenha uma assinatura do Azure que inclua o StorSimple. Sua assinatura determina os recursos que você pode acessar no portal do Azure. Se a organização não tiver uma assinatura do Azure e você desejar saber mais sobre ele, consulte [Inscrever-se para o Azure como uma organização](sign-up-organization.md). 

Como o serviço do Gerenciador do StorSimple está hospedado no Azure, ele é protegido pelos recursos de segurança do Azure. Para obter mais informações sobre os recursos de segurança fornecidos pelo Microsoft Azure, acesse a [Central de Confiabilidade do Microsoft Azure](http://azure.microsoft.com/support/trust-center/security/).

## Proteção de dispositivos StorSimple

O dispositivo StorSimple é um dispositivo de armazenamento híbrido local que contém unidades de estado sólido (SSDs) e unidades de disco rígido (HDDs), juntamente com controladores redundantes e recursos de failover automático. Os controladores de gerenciam o armazenamento hierárquico, colocando dados usados no momento (ou mais acessados) no armazenamento local (no dispositivo StorSimple ou servidores locais), ao mesmo tempo movendo dados menos dados para a nuvem.

## Controlar o acesso ao StorSimple por meio do registro do dispositivo

Apenas dispositivos StorSimple autorizados têm permissão para ingressar no serviço do Gerenciador do StorSimple que você criou na sua assinatura do Azure. 

Para autorizar um dispositivo, registre-o com o serviço do Gerenciador do StorSimple fornecendo a chave de registro. A chave de registro é uma chave aleatória de 128 bits gerada no portal. Para saber como obter uma chave de registro de serviço, vá para a [Etapa 2: Obtenha a chave de registro](https://msdn.microsoft.com/library/azure/dn772346.aspx).

> [AZURE.NOTE] A chave de registro de serviço é uma chave longa que contém 100 caracteres. Recomendamos copiar a chave e salvá-la em um arquivo de texto em um local seguro para poder usá-la para autorizar dispositivos adicionais conforme necessário.
> 
> * Se a chave de registro de serviço for perdida depois de registrar o primeiro dispositivo, você pode gerar uma nova chave usando o serviço do Gerenciador do StorSimple. Isso não afetará a operação dos dispositivos existentes. 
> * Depois que um dispositivo for registrado, ele utilizará tokens para se comunicar com o Microsoft Azure. A chave de registro de serviço não é usada depois do registro do dispositivo.


## Proteja sua solução StorSimple por meio de senhas

As senhas são um aspecto importante da segurança do computador e são usadas amplamente na solução Microsoft Azure StorSimple para ajudar a garantir que os dados fiquem acessíveis apenas a usuários autorizados. O StorSimple permite que você configure as seguintes senhas:

- Senha do Windows PowerShell para StorSimple
- Senha do administrador de dispositivo do StorSimple
- Senhas do iniciador e de destino do protocolo CHAP
- Senha do Gerenciador de instantâneos do StorSimple

### Senhas do Windows PowerShell para StorSimple e do administrador de dispositivo do StorSimple

O Windows PowerShell para StorSimple é uma interface de linha de comando que você pode usar para gerenciar o dispositivo StorSimple. O Windows PowerShell para StorSimple tem recursos que permitem registrar o dispositivo, configurar a interface de rede no dispositivo, instalar determinados tipos de atualizações, solucionar problemas do dispositivo acessando a sessão de suporte e alterar o estado do dispositivo. Você pode acessar o Windows PowerShell para StorSimple conectando-se ao console serial ou usando o Windows PowerShell remotamente. 

A comunicação remota do PowerShell pode ser feita por meio de HTTPS ou HTTP. Se o gerenciamento remoto por HTTPS estiver habilitado, você precisará baixar o certificado de gerenciamento remoto do dispositivo e instalá-lo no cliente remoto. 

> [AZURE.IMPORTANT] 

> * O gerenciamento remoto está desativado por padrão. Você pode usar o serviço do Gerenciador do StorSimple para habilitá-lo. Como melhor prática de segurança, o acesso remoto deve ser habilitado somente durante o período de tempo realmente necessário.
> * Se você alterar a senha, certifique-se de notificar todos os usuários de acesso remoto para que eles não tenham uma perda de conectividade inesperada.
> * O serviço do Gerenciador do StorSimple não pode recuperar senhas existentes, apenas redefini-las. É recomendável armazenar todas as senhas em um local seguro para não precisar redefinir uma senha se ela for esquecida. Se você precisar redefinir uma senha, certifique-se de notificar todos os usuários antes de fazer isso. 
> * Você pode acessar a interface do Windows PowerShell usando uma conexão serial com o dispositivo. Você também pode acessá-la remotamente usando HTTP ou HTTPS, o que fornece segurança adicional. HTTPS oferece um nível mais alto de segurança que uma conexão HTTP ou serial. No entanto, para usar HTTPS, você deve primeiro instalar um certificado no computador cliente que acessará o dispositivo. Baixe o certificado de acesso remoto na página de configuração do dispositivo no serviço do Gerenciador do StorSimple. Se o certificado para acesso remoto for perdido, baixe um novo certificado e propague-o a todos os clientes autorizados a usar o gerenciamento remoto.

Depois de usar o Windows PowerShell para StorSimple para se conectar ao dispositivo, forneça a senha de administrador do dispositivo para fazer logon no dispositivo.

### Senhas do iniciador e de destino do protocolo CHAP

O CHAP é um esquema de autenticação usado pelo dispositivo StorSimple para validar a identidade de clientes remotos. A verificação se baseia em uma senha compartilhada. O protocolo CHAP pode ser unidirecional ou bidirecional (mútuo). Com o CHAP unidirecional, o destino (o dispositivo StorSimple) autentica um iniciador (host). O CHAP mútuo requer que o destino autentique o iniciador e, em seguida, que o iniciador autentique o destino. O Azure StorSimple pode ser configurado para usar qualquer um dos métodos.

> [AZURE.IMPORTANT] 
> 
> * O nome de usuário do CHAP deve conter menos de 233 caracteres.
> * A senha do CHAP deve conter entre 12 e 16 caracteres. Tentar usar um nome de usuário ou senha mais longo resultará em falha de autenticação no host do Windows.
> * Você não pode usar a mesma senha para o iniciador do CHAP e o destino do CHAP.
> * Depois de definir a senha, ela pode ser alterada, mas não recuperada. Se a senha for alterada, certifique-se de notificar todos os usuários de acesso remoto para que eles possam se conectar com sucesso ao dispositivo StorSimple.

### Senha do Gerenciador de instantâneos do StorSimple

O Gerenciador de instantâneos do StorSimple é um snap-in do MMC (Console de Gerenciamento Microsoft) que usa grupos de volume e o serviço de cópias de sombra de volume do Windows para gerar backups consistentes com o aplicativo. Além disso, você pode usar o Gerenciador de instantâneos do StorSimple para criar o clone e agendamentos de backup ou restaurar volumes.

Ao configurar um dispositivo para usar o Gerenciador de instantâneos do StorSimple, forneça a senha do Gerenciador de instantâneos do StorSimple. Esta senha é definida pela primeira vez no Windows PowerShell para StorSimple durante o registro. A senha também pode ser definida e alterada usando o serviço do Gerenciador do StorSimple. Essa senha autentica o dispositivo com o Gerenciador de instantâneos do StorSimple.

> [AZURE.IMPORTANT] <ul><li>A senha deve ter de 14 a 15 caracteres e conter três ou mais de uma combinação de letra maiúscula, letra minúscula, caractere numérico e caractere especial.</li><li>Depois de definir a senha do Gerenciador de instantâneos do StorSimple, ela pode ser alterada, mas não recuperada. Se você alterar a senha, certifique-se de notificar todos os usuários remotos.</li></ul>


### Práticas recomendadas de senha

É recomendável usar as seguintes diretrizes para ajudar a garantir que as senhas do Azure StorSimple sejam fortes e bem protegidas:

- Altere suas senhas a cada três meses.
- Use uma senha forte. Para obter mais informações, consulte Criar senhas fortes.
- Sempre use senhas diferentes para diferentes mecanismos de acesso; cada uma das senhas especificadas deve ser exclusiva.
- Não compartilhe senhas com ninguém que não esteja autorizado a acessar o dispositivo StorSimple.
- Não fale sobre uma senha na frente de outras pessoas nem dê dicas do formato de uma senha.
- Se você suspeitar que uma conta ou senha foi comprometida, relate o incidente ao departamento de segurança da informação.
- Trate todas as senhas como informações sigilosas e confidenciais. 

## Proteção de dados do StorSimple

Esta seção descreve os recursos de segurança do Azure StorSimple que protegem os dados em trânsito e armazenados.

Conforme descrito em outras seções, as senhas são usadas para autorizar e autenticar os usuários antes que eles possam obter acesso à sua solução Microsoft Azure StorSimple. Outra consideração de segurança é proteger os dados contra usuários não autorizados durante a transferência entre sistemas de armazenamento e enquanto estão sendo armazenados. As seções a seguir descrevem os recursos de proteção de dados fornecidos com o Azure StorSimple.

> [AZURE.NOTE] Eliminação de duplicação fornece proteção adicional para dados armazenados no dispositivo StorSimple e no Armazenamento do Microsoft Azure. Quando ocorre a eliminação de duplicação dos dados, os objetos de dados são armazenados separadamente dos metadados usados para mapeá-los e acessá-los: não há nenhum contexto no nível de armazenamento disponível para reconstruir os dados com base na estrutura de volume, sistema de arquivos ou nome de arquivo.

## Proteger dados que fluem através do serviço

É o objetivo principal do serviço do Gerenciador do StorSimple gerenciar e configurar o dispositivo StorSimple. O serviço do Gerenciador do StorSimple é executado no Microsoft Azure. Você usa o Portal de Gerenciamento do Azure para inserir dados de configuração de dispositivo e, em seguida, Microsoft Azure usa o serviço do Gerenciador do StorSimple para enviar os dados para o dispositivo. O serviço do Gerenciador do StorSimple usa um sistema de pares de chaves assimétricas para ajudar a garantir que o comprometimento do serviço do Azure não resulte no comprometimento das informações armazenadas. O sistema de chaves assimétricas ajuda a proteger os dados que fluem através do serviço da seguinte maneira:

1. Um certificado de criptografia de dados que usa um par de chaves pública e privada assimétricas é gerado no dispositivo e usado para proteger os dados. As chaves são geradas quando o primeiro dispositivo é registrado. 
2. As chaves do certificado de criptografia de dados são exportadas para um arquivo de troca de informações pessoais (.pfx) protegido pela chave de criptografia de dados de serviço, que é uma chave forte de 128 bits aleatoriamente gerada pelo dispositivo durante o registro.
3. A chave pública do certificado é disponibilizada para o serviço do Gerenciador do StorSimple e a chave privada permanece com o dispositivo.
4. Os dados que entram no serviço são criptografados usando a chave pública e descriptografados usando a chave privada armazenada no dispositivo, garantindo que o serviço do Azure não possa descriptografar os dados que fluem para o dispositivo.

> [AZURE.IMPORTANT]
> 
> * A chave de criptografia de dados de serviço é gerada apenas no primeiro dispositivo registrado com o serviço. Todos os dispositivos subsequentes registrados com o serviço devem usar a mesma chave de criptografia de dados de serviço. É muito importante fazer uma cópia dessa chave e salvá-la em um local seguro. Uma cópia da chave de criptografia de dados de serviço deve ser armazenada de forma que possa ser acessada por uma pessoa autorizada e facilmente comunicada ao administrador do dispositivo.
> * Você pode alterar a chave de criptografia de dados de serviço e o certificado de criptografia de dados correspondente selecionando a opção Alterar chave de criptografia de dados de serviço no painel de serviço. Alterar as chaves de criptografia requer que todos os dispositivos sejam atualizados com a nova chave. Portanto, é recomendável que você altere a chave quando todos os dispositivos estiverem online. Se os dispositivos estiverem offline, suas chaves podem ser alteradas em um momento diferente. Os dispositivos com chaves desatualizadas ainda poderão executar backups, mas não poderão restaurar dados até que a chave seja atualizada. Para obter mais informações, acesse o [Painel serviço](https://msdn.microsoft.com/library/azure/dn772326.aspx).
> * Para garantir que a segurança dos dados não seja comprometida, use um dispositivo StorSimple físico para alterar a chave de criptografia de dados de serviço.
> * Se a chave de criptografia de dados de serviço for perdida, um profissional de suporte da Microsoft pode ajudar a recuperá-la, desde que você tenha pelo menos um dispositivo no estado online. É recomendável alterar a chave de criptografia de dados de serviço depois de recuperá-la. Para obter instruções, acesse [Alterar a chave de criptografia de dados de serviço](https://msdn.microsoft.com/library/azure/8158cbe9-1f26-4513-a031-49f88bb3d481#sec01).
> * A chave de criptografia de dados de serviço e o certificado de criptografia de dados não expiram. No entanto, é recomendável alterar a chave de criptografia de dados de serviço regularmente para ajudar a impedir o comprometimento da chave.</li></ul>


## Proteger dados em repouso

O dispositivo StorSimple gerencia dados armazenando-os em camadas localmente e na nuvem, dependendo da frequência de uso. Todos os computadores host conectados ao dispositivo enviam dados para o dispositivo, que então os transfere para a nuvem, conforme apropriado. Os dados são transferidos do dispositivo para a nuvem por meio do protocolo iSCSI. Cada dispositivo tem um destino iSCSI que mostra todos os volumes compartilhados naquele dispositivo. Todos os dados são criptografados antes de serem enviados ao armazenamento em nuvem. Para ajudar a garantir a segurança e a integridade dos dados movidos para a nuvem, o Azure StorSimple permite definir chaves de criptografia de armazenamento em nuvem da seguinte maneira:

- Especifique a chave de criptografia de armazenamento em nuvem ao criar um contêiner de volume. A chave não pode ser modificada nem adicionada posteriormente. 
- Todos os volumes em um contêiner de volume compartilham a mesma chave de criptografia. Se você quiser uma forma diferente de criptografia para um volume específico, é recomendável criar um novo contêiner de volume para hospedar esse volume.
- Ao inserir a chave de criptografia de armazenamento em nuvem no serviço do Gerenciador do StorSimple, ela criptografada usando a parte pública da chave de criptografia de dados de serviço e, em seguida, enviada para o dispositivo.
- A chave de criptografia de armazenamento em nuvem não é armazenada em qualquer lugar no serviço e é conhecida apenas para o dispositivo.
- Especificar uma chave de criptografia de armazenamento em nuvem é opcional. Você pode enviar dados que foram criptografados no host para o dispositivo.

## Proteger dados por meio de contas de armazenamento

Cada assinatura do Microsoft Azure pode criar uma ou mais contas de armazenamento. (Uma conta de armazenamento fornece um namespace exclusivo para trabalhar com dados armazenados na nuvem do Azure.) O acesso a uma conta de armazenamento é controlado pelas chaves de assinatura e de acesso associadas à conta de armazenamento. 

Quando você cria uma conta de armazenamento, o Microsoft Azure gera duas chaves de acesso de armazenamento de 512 bits, uma das quais é usada para autenticação quando o dispositivo StorSimple acessa a conta de armazenamento. Observe que apenas uma dessas chaves está em uso. A outra chave é mantida em reserva, permitindo alternar as chaves periodicamente. Para alternar as chaves, ative a chave secundária e, em seguida, exclua a chave primária. Você pode criar uma nova chave para uso durante o próximo rodízio. (Por motivos de segurança, muitos data centers exigem a rotação de chaves.) 

> [AZURE.IMPORTANT] 
> 
> Recomendamos seguir estas práticas recomendadas para a rotação de chaves:
> 
> * Alterne as chaves da conta de armazenamento regularmente para ajudar a garantir que sua conta de armazenamento não seja acessada por usuários não autorizados.
> * Periodicamente, o administrador do Azure deve alterar ou regenerar a chave primária ou secundária usando a seção de armazenamento do Portal de Gerenciamento para acessar diretamente a conta de armazenamento.


## Proteger dados por meio de criptografia

O StorSimple usa os seguintes algoritmos de criptografia para proteger os dados armazenados ou em trânsito entre os componentes da solução StorSimple.

| Algoritmo | Comprimento da chave | Aplicativos/protocolos/comentários |
| --------- | ---------- | ------------------------------- |
| RSA       | 2048       | RSA PKCS 1 v1.5 é usada pelo Portal de Gerenciamento para criptografar dados de configuração enviados para o dispositivo: por exemplo, credenciais da conta de armazenamento, configuração do dispositivo StorSimple e chaves de criptografia de armazenamento em nuvem. |
| AES       | 256        | AES com CBC é usado para criptografar a parte pública da chave de criptografia de dados de serviço antes de ela ser enviada para o Portal de Gerenciamento do dispositivo StorSimple. Também é usado pelo dispositivo StorSimple para criptografar os dados antes sejam enviados para a conta de armazenamento em nuvem. |
| RSA       | 2048       | RSA PKCS 1 v1.7 é usado pelo Portal de Gerenciamento para criptografar a parte pública da chave de criptografia de dados de serviço antes de armazená-la no armazenamento local de portal. |


## Perguntas frequentes (FAQ)

A seguir estão algumas perguntas e respostas sobre segurança e o Microsoft Azure StorSimple.

**P:** Meu serviço está comprometido. Quais deveriam ser minhas próximas etapas?

**R:** Você deve alterar a chave de criptografia de dados de serviço e as chaves de conta de armazenamento para a conta de armazenamento que está sendo usada para colocar os dados em camadas. Para obter instruções, vá para:

- [Alterar a chave de criptografia de dados do serviço](https://msdn.microsoft.com/library/azure/8158cbe9-1f26-4513-a031-49f88bb3d481#sec01)
- [Rotação de chave de contas de armazenamento](https://msdn.microsoft.com/library/azure/1747f56e-858a-4cfe-a020-949d7db23b8b#rotate)

**P:** Eu tenho um dispositivo StorSimple novo que está solicitando a chave de registro. Como fazer para recuperá-la?

**R:** Essa chave foi criada quando você criou o serviço do Gerenciador do StorSimple. Quando você usa o serviço do Gerenciador do StorSimple para se conectar ao dispositivo, pode usar a página de início rápido do serviço para exibir ou regenerar a chave de registro. Gerar uma nova chave de registro do serviço não afetará os dispositivos registrados existentes. Para obter instruções, vá para:

- [Exibir ou regenerar a chave de registro](https://msdn.microsoft.com/library/azure/8158cbe9-1f26-4513-a031-49f88bb3d481#BKMK_viewsrk)

**P:** Perdi minha chave de criptografia de dados de serviço. O que devo fazer?

**R:** Entre em contato com o suporte da Microsoft. Os funcionários podem fazer logon em uma sessão de suporte no seu dispositivo e ajudá-lo a recuperar a chave. Imediatamente depois de obter a chave de criptografia de dados de serviço, altere-a para garantir que a nova chave seja conhecida apenas por você. Para obter instruções, vá para:

- [Alterar a chave de criptografia de dados do serviço](https://msdn.microsoft.com/library/azure/8158cbe9-1f26-4513-a031-49f88bb3d481#sec01)

**P:** Autorizei um dispositivo para uma alteração de chave de criptografia de dados serviço, mas ele não iniciou o processo de alteração de chave. O que devo fazer?

**R:** Se o período de tempo limite tiver expirado, você precisará autorizar novamente o dispositivo para a alteração de chave de criptografia de dados serviço e reiniciar o processo.

**P:** Alterei a chave de criptografia de dados de serviço, mas não consegui atualizar outros dispositivos em quatro horas. É necessário iniciar novamente?

**R:** O período de tempo de quatro horas é apenas para iniciar a alteração. Depois de iniciar o processo de atualização no dispositivo StorSimple autorizado, a autorização será válida até que todos os dispositivos sejam atualizados.

**P:** Nosso administrador StorSimple saiu da empresa. O que devo fazer?

**R:** Altere e redefina as senhas que permitem o acesso ao dispositivo StorSimple e altere a criptografia de dados do serviço de chave para garantir que as novas informações não sejam conhecidas por pessoal não autorizado. Para obter instruções, vá para:

- [Configurar o Gerenciador de instantâneos do StorSimple](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec08)
- [Configurar a senha de administrador do dispositivo](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec09)
- [Configurar o gerenciamento remoto](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec10)
- [Configurar o iniciador do CHAP](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec06)
- [Configurar o destino do CHAP](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec07)

**P:** Desejo fornecer a senha do Gerenciador de instantâneos do StorSimple para um host que está se conectando ao dispositivo StorSimple, mas a senha não está disponível. O que posso fazer?

**R:** Se você tiver esquecido a senha, deve criar uma nova. Em seguida, informe todos os usuários existentes de que a senha foi alterada e eles devem atualizar seus clientes para usar a nova senha. Para obter instruções, vá para:

- [Configurar o Gerenciador de instantâneos](https://msdn.microsoft.com/library/azure/02f1412f-e196-4a88-8eda-2113247ea47c#sec08)
- [Autenticar um dispositivo](https://msdn.microsoft.com/library/azure/dn790537.aspx)

**P:** O certificado para o acesso remoto do Windows PowerShell para StorSimple foi alterado no dispositivo. Como fazer para atualizar meus clientes de acesso remoto?

**R:** Você pode baixar o novo certificado de serviço do Gerenciador do StorSimple e depois fornecê-lo para ser instalado no repositório de certificados de clientes de acesso remoto. Para obter instruções, vá para:

- [Cmdlet Import-Certificate](https://technet.microsoft.com/library/hh848630.aspx)

**P:** Meus dados ficarão protegidos se o serviço do Gerenciador do StorSimple estiver comprometido?

**R:** Os dados de configuração de serviço sempre são criptografados com a chave pública quando você os exibe em um navegador da Web. Uma vez que o serviço não tem acesso à chave privada, ele não poderá ver os dados. Se o serviço do Gerenciador do StorSimple estiver comprometido, não há qualquer impacto, pois nenhuma chave é armazenada no serviço do Gerenciador do StorSimple.

**P:** Se alguém obtiver acesso ao certificado de criptografia de dados, meus dados serão comprometidos?

**R:** O Microsoft Azure armazena a chave de criptografia de dados do cliente (arquivo .pfx) em um formato criptografado. O arquivo .pfx é criptografado e o serviço StorSimple tem a chave de criptografia de dados de serviço para descriptografar o arquivo .pfx. Simplesmente obter acesso ao arquivo .pfx não revelará nenhum segredo.

**P:** O que acontece se uma entidade governamental solicitar à Microsoft os meus dados?

**R:** Uma vez que todos os dados são criptografados no serviço e a chave privada é mantida com o dispositivo, a entidade governamental deverá solicitar os dados ao cliente.

## Próximas etapas

[Introdução ao dispositivo físico](https://msdn.microsoft.com/library/azure/dn772410.aspx)


<!--HONumber=52-->