<properties
   pageTitle="Transferindo uma assinatura do Azure | Microsoft Azure"
	description="Como transferir uma assinatura do Azure para outro usuário e algumas Perguntas Frequentes (FAQ) sobre o processo"
	services="billing"
	documentationCenter=""
	authors="curtand"
	manager="msmStevenPo"
	editor=""/>

<tags
   ms.service="billing"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="billing"
	ms.date="08/19/2015"
	ms.author="curtand;ruchic"/>

# Transferindo uma assinatura do Azure

Você:

- Precisa passar a cobrança de propriedade de sua assinatura do Azure para outra pessoa?
- Deseja alterar a conta usada para se inscrever no Azure? Talvez tenha usado sua conta da Microsoft, mas deve usar sua conta do trabalho ou da escola em vez disso?
- Deseja mover sua assinatura do Azure de um diretório para outro?
- Ter o Azure e o Office 365 em diferentes locatários e quer consolidar?

Se sua conta estiver nos EUA, agora você pode fazer isso facilmente no Centro de Contas do Microsoft Azure - para assinaturas pré-pagas. Adicionamos a capacidade de transferir sua assinatura para outro usuário. Em outras palavras, agora você pode alterar o administrador da conta em qualquer assinatura pré-paga que possua.

## Como transferir uma assinatura do Azure

1.  Entre em <https://account.windowsazure.com/Subscriptions>

2.  Selecione a assinatura para transferir.

3.  Clique na opção **Transferir assinatura**.

    ![Guia assinaturas de conta do Azure](./media/billing-subscription-transfer/image1.png)

4.  Siga os prompts para especificar o destinatário.

    ![Caixa de diálogo de assinatura de transferência](./media/billing-subscription-transfer/image2.PNG)

5.  O destinatário obtém automaticamente um email com um link de aceitação.

    ![Email de transferência de assinatura para o destinatário](./media/billing-subscription-transfer/image3.png)

6.  O destinatário clica no link e segue as instruções, incluindo inserir suas informações de pagamento.

    ![Primeira página da Web de transferência de assinatura](./media/billing-subscription-transfer/image4.PNG)

    ![Segunda página da Web de transferência de assinatura](./media/billing-subscription-transfer/image5.PNG)

7. Sucesso! Agora a assinatura será transferida.

## Perguntas frequentes (FAQ)

-   **Uma transferência de assinatura resulta em qualquer tempo de inatividade do serviço?**

    Não há nenhum impacto no serviço. Isso efetivamente cancela a assinatura do atual administrador da conta e cria uma nova na conta do destinatário, mas associa os serviços do Azure subjacentes à nova assinatura. A ID da assinatura permanece a mesma.

-   **Se eu assumir a propriedade de cobrança de uma assinatura de outra organização, eles continuarão a ter acesso aos meus recursos?**

    Se a assinatura é transferida para outro locatário, os usuários associados ao locatário anterior perdem o acesso à assinatura. Mesmo que um usuário não seja mais um administrador ou coadministrador de serviços, ele ainda terá acesso à assinatura por meio de outros mecanismos de segurança. Eles incluem:-certificados de gerenciamento que concedem ao usuário direitos de administrador para os recursos de assinatura. Para obter mais informações, consulte [Criar e carregar um certificado de gerenciamento para o Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx) - teclas de acesso para serviços como armazenamento. Para obter mais informações, consulte [Exibir, copiar e regenerar chaves de acesso de armazenamento](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys) -credenciais de acesso remoto para serviços como máquinas virtuais do Azure

    Esta não é uma lista completa. O destinatário deve considerar a atualização de qualquer segredo associado ao serviço, se for necessário restringir o acesso a seus recursos. A maioria dos recursos pode ser atualizada da seguinte maneira:

    1.   Vá para o portal do Azure: [**https://portal.azure.com*](https://portal.azure.com)

    2.    Clique em Procurar tudo - & gt; Todos os recursos

    3.    Selecione o recurso. Isso abrirá a folha de recursos.

    4.    Na folha do recurso, clique em **Configurações**. Aqui você pode exibir e atualizar os segredos existentes.


-   **Se eu transferir a assinatura no meio do ciclo de cobrança, o destinatário paga por todo o ciclo de cobrança?**

    O remetente é responsável pelo pagamento por qualquer uso que foi relatado até o ponto no qual a transferência foi concluída. O destinatário é responsável por uso relatado a partir do momento da transferência em diante. Pode haver algum uso que ocorreu antes da transferência, mas foi relatado posteriormente. Isso será incluído na lista do destinatário.

-   **O destinatário tem acesso ao histórico de cobrança e de uso?**

    Neste momento, as únicas informações reveladas para o destinatário são o valor da última conta (ou o saldo atual, se a assinatura foi transferida antes da geração da primeira lista). O restante do histórico de cobrança e uso não é transferido com a assinatura.

-   **A oferta pode ser alterada durante uma transferência?**

    A oferta deve permanecer a mesma. Para alterar sua oferta, você deve [entrar em contato com o suporte](http://go.microsoft.com/fwlink/?LinkID=619338).

-   **Posso transferir uma assinatura para uma conta de usuário em outro país?**

    Não, desta vez. Não há suporte para isso. A conta de usuário do destinatário deve estar no mesmo país.

-   **O destinatário pode usar um mecanismo de pagamento diferente?**

    Sim e, de fato, você pode usar esse mecanismo para alterar o método de pagamento em sua assinatura de fatura para cartão de crédito. Transfira apenas para outra conta que você possua e insira seu cartão de crédito ao receber a assinatura. Há limitações aqui: agora o histórico de cobrança da assinatura é dividido em duas contas. Mas a vantagem é que você pode fazer isso sem precisar [entrar em contato com o suporte](http://go.microsoft.com/fwlink/?LinkID=619338).

<!---HONumber=September15_HO1-->