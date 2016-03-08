<properties
	pageTitle="Considerações sobre o design da identidade híbrida do Active Directory do Azure - determinar os requisitos de sincronização de diretório | Microsoft Azure"
	description="Identificar quais requisitos são necessários para a sincronização de todos os usuários entre nos locais e nuvem para a empresa."
	documentationCenter=""
	services="active-directory"
	authors="billmath"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="02/02/2016"
	ms.author="billmath"/>

# Determinar os requisitos de sincronização de diretório
Sincronização é fornecer aos usuários uma identidade na nuvem com base em sua identidade local. Em caso positivo ou negativo, usarão a conta sincronizada para autenticação ou autenticação federada, os usuários ainda precisarão ter uma identidade na nuvem. Essa identidade precisará ser mantida e atualizada periodicamente. As atualizações podem ter várias formas, desde alterações de título para alterações de senha.

Comece avaliando a solução de identidade local das organizações e requisitos de usuário. Essa avaliação é importante para definir os requisitos técnicos para como as identidades de usuário serão criadas e mantidas na nuvem. Para a maioria das organizações, o Active Directory está local e será o diretório local que os usuários serão por sincronizado, porém em alguns casos este não será o caso.

Certifique-se de responder às seguintes perguntas:


- Você tem uma floresta do AD, várias ou nenhuma?
 - Quantos diretórios AD do Azure você vai sincronizar?
 
    1. Você está usando a filtragem?
    2. Você tem vários servidores do Azure AD Connect planejados?
  
- Atualmente, você tem uma ferramenta de sincronização local?
  - Em caso positivo, os usuários têm um diretório virtual/integração das identidades?
- Você tem qualquer outro diretório local que deseja sincronizar (por exemplo, diretório LDAP, banco de dados de RH, etc)?
  - Você vai fazer qualquer GALSync?
  - O que é o estado atual do UPNs na sua organização? 
  - Você tem um diretório diferente que os usuários se autenticam?
  - Sua empresa usa o Microsoft Exchange?
    - Planeja de ter uma implantação híbrida de mudança? 
   
Agora que você tem uma ideia sobre os requisitos de sincronização para a sua empresa, você precisa avaliar os aplicativos que usam esses serviços de diretório. Essa avaliação é importante para definir os requisitos técnicos para integrar esses aplicativos para a nuvem. Certifique-se de responder às seguintes perguntas:

- Esses aplicativos serão movidos para a nuvem e usar o diretório?
- Há atributos especiais que precisam ser sincronizados na nuvem para que esses aplicativos possam usá-los com sucesso?
- Esses aplicativos precisarão ser reescritos para tirar proveito da autenticação da nuvem?
- Esses aplicativos continuarão a permanecer local enquanto os usuários os acessam usando a identidade de nuvem?

Você também precisa determinar a sincronização de diretórios de requisitos e restrições de segurança. Essa avaliação é importante para obter uma lista dos requisitos que serão necessários para criar e manter as identidades do usuário na nuvem. Certifique-se de responder às seguintes perguntas:

- Onde o servidor de sincronização estará?
- Será integrado ao domínio?
- O servidor será localizado em uma rede restrita por trás de um firewall, como um DMZ?
  - Você poderá abrir as portas de firewall necessárias para dar suporte à sincronização?
- Você tem um plano de recuperação de desastres para o servidor de sincronização?
- Você tem uma conta com as permissões corretas para todas as florestas que deseja sincronizar?
 - Se sua empresa não sabe a resposta para essa pergunta, leia a seção "Permissões de sincronização de senha" no artigo [Instalar o serviço de sincronização do Active Directory do Azure](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) e determine se você já tiver uma conta com essas permissões, ou se você precisar criar uma.
- Se você tiver sincronização multi-floresta o servidor de sincronização é capaz de obter em cada floresta?
 
>[AZURE.NOTE]
Certifique-se de fazer anotações de cada resposta e entender o raciocínio por trás de resposta. [Determinar os requisitos de resposta a incidentes](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) vai ultrapassar as opções disponíveis. Ao responder essas perguntas, você selecionará a opção que melhor se ajusta às necessidades da sua empresa.

## Próximas etapas
[Determinar os requisitos de autenticação multifator](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## Consulte também
[Visão geral de considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=AcomDC_0204_2016-->