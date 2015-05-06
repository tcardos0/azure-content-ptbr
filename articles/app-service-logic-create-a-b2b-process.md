﻿<properties 
   pageTitle="Criando um processo B2B no Serviço de Aplicativo do Microsoft Azure" 
   description="Visão geral da criação de um processo B2B" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="harishkragarwal" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/22/2015"
   ms.author="hariag"/>


# Criando um processo B2B


## Cenário empresarial 
Contoso e Northwind são dois parceiros comerciais.  A Contoso (varejista) envia ordens de compra para a Northwind (fornecedor) por um transporte de nível industrial, como o AS2.  A Northwind armazena todas as ordens recebidas em seu armazenamento na nuvem.  As ordens de compra são mensagens XML entre esses dois parceiros.  Depois que a mensagem é armazenada no armazenamento em nuvem da Northwind, os processos internos da Northwind lidam com a ordem daí por adiante.
 
O objetivo deste tutorial é estabelecer como a Northwind pode estabelecer um processo de negócios por meio da qual ela possa receber as mensagens (ordens de compra em XML) de seu parceiro Contoso via AS2 e mantê-las em seu armazenamento em nuvem.


## Recursos demonstrados 
Este tutorial ajuda a apresentar os seguintes recursos: 

- **Transporte de mensagens**:  O varejista e o fornecedor podem estar em plataformas diferentes, mas ainda podem se comunicar.  Neste tutorial, eles estão se comunicando via AS2 (Applicability Statement 2).  O AS2 é uma maneira popular de transportar dados entre parceiros comerciais em comunicações B2B.
- **Persistência de dados**:  Após a mensagem ser recebida via AS2, a Northwind deseja mantê-la antes de continuar o processamento.  Ela pode usar um conector para manter as mensagens em seu armazenamento em nuvem.  Neste tutorial, os Blobs do Azure são utilizados como armazenamento em nuvem da Northwind.
- **Criar um processo empresarial**:  Em um fluxo, diversos aplicativos de API podem ser reunidos para obter um resultado empresarial, conforme demonstrado aqui.


## Antes de começar
Este tutorial supõe que você tenha uma compreensão básica dos Serviços de Aplicativos do Azure e saiba como criar aplicativos de API e compor um fluxo.


## Etapas para atingir o cenário empresarial
**Criar e configurar os aplicativos de API necessários**

1. Crie uma instância do **Conector de Blob de Armazenamento do Azure**.  Isso exige credenciais para uma conta de Armazenamento do Azure.  Certifique-se de que elas estejam prontas antes de começar a criar a instância.
2. Crie uma instância do **Gerenciamento de Parceiros Comerciais do BizTalk**.  Ela precisa de um Banco de Dados SQL em branco para funcionar.  Certifique-se de que ele esteja pronto antes de começar a criar a instância.
3. Crie uma instância do **Conector AS2**.  Ela também precisa de um Banco de Dados SQL em branco para funcionar.  Certifique-se de que ele esteja pronto antes de começar a criar a instância.  Além disso, se desejar arquivar mensagens como parte do processamento AS2, você pode fornecer credenciais para um Blob do Azure durante sua criação.
4. Configure o serviço de TPM (Gerenciamento de Parceiro Comercial) que é criado:
	1. Navegue até a instância do serviço de TPM criado como parte das etapas acima.
	2. Use a opção **Parceiros** em *Components* para **Adicionar** um novo parceiro chamado **Contoso** e, em seu perfil, adicione a identidade AS2 necessária.
	3. Use a opção **Parceiros** em *Components* para **Adicionar** um novo parceiro chamado **Northwind** e, em seu perfil, adicione a identidade AS2 necessária.
	4. Use a opção **Contratos** em *Components* para **Adicionar** um novo contrato AS2 entre a Northwind e a Contoso.  A Northwind será o parceiro hospedado e a Contoso será o parceiro convidado.  Conforme apropriado, configure a autenticação, criptografia, compactação e as confirmações durante a criação do contrato.  Caso certificados precisem ser usados, eles podem ser carregados por meio da opção **Certificados** ao procurar pelo serviço de TPM que foi criado.


## Criar um fluxo/processo empresarial
1. Crie um novo fluxo no qual a primeira etapa é AS2.  Arraste e solte o **Conector AS2** e escolha a instância já criada.  Escolha o gatilho como funcionalidade

![][1]

2. Em seguida, arraste e solte o **Conector do Blob de Armazenamento do Azure** e escolha a instância criada.  Escolha ação como funcionalidade e, dentro dela, selecione Carregar Blob como a funcionalidade desejada.  Configurar conforme apropriado

3. Agora, crie/implante o fluxo


## Processamento de mensagens e solução de problemas
1. É hora de testar o fluxo que implantamos.  Envie mensagens XML encapsuladas no AS2 (de acordo com o contrato AS2 criado acima) ao ponto de extremidade AS2 fornecido pela instância do AS2Connector que foi criada.  Talvez seja necessário configurar a autenticação do ponto de extremidade para que eles fique acessível publicamente.
2. Informações de execução sobre o fluxo são exibidas navegando para o fluxo e acessando a instância do fluxo que foi executada
3. Para informações de processamento de AS2, navegue até a instância AS2Connector envolvida e acesse a seção de Acompanhamento.  Você pode usar os filtros envolvidos para restringir a exibição às informações que desejar.

![][2]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-b2b-process/Flow.jpg
[2]: ./media/app-service-logic-create-a-b2b-process/Tracking.jpg

<!--HONumber=49-->