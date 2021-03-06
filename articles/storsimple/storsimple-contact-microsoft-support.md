<properties 
   pageTitle="Contatar o Suporte da Microsoft | Microsoft Azure"
   description="Saiba como criar uma solicitação de suporte e iniciar uma sessão de suporte em seu dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/14/2016"
   ms.author="alkohli" />

# Contatar o Suporte da Microsoft

Se tiver problemas com sua solução Microsoft Azure StorSimple, você poderá criar uma solicitação de serviço de suporte técnico. Em uma sessão online com seu engenheiro de suporte, talvez você precise iniciar uma sessão de suporte em seu dispositivo StorSimple. Este artigo orienta você sobre:

- Como criar uma solicitação de suporte.
- Como iniciar uma sessão de suporte na interface do Windows PowerShell de seu dispositivo StorSimple.

Examine os [SLAs e informações de suporte ao StorSimple 8000 Series](https://msdn.microsoft.com/library/mt433077.aspx) antes de criar uma solicitação de suporte.

## Criar uma solicitação de suporte

Execute as seguintes etapas para criar uma solicitação de suporte:

#### Para criar uma solicitação de suporte

1. Uma solicitação de suporte pode ser criada por meio do [portal clássico do Azure](https://manage.windowsazure.com/). No [portal clássico](https://manage.windowsazure.com/), clique em seu **Nome de conta** e em **Contatar o suporte da Microsoft**.

	![Contate o Suporte da MS por meio do Portal de Gerenciamento](./media/storsimple-contact-microsoft-support/IC777286.png)

2. Na caixa de diálogo **Contatar o Suporte da Microsoft**:

	1. Na lista suspensa, selecione a **Assinatura** de destino associada ao serviço StorSimple Manager. Especifique **Tipo de Suporte** como **Técnico**. É necessário um plano de suporte pago para habilitar o Suporte Técnico.

	2. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-contact-microsoft-support/IC740895.png) para **Criar Tíquete**.

3. Na janela **Suporte da Microsoft**, na lista suspensa **Produto**, escolha **StorSimple**.

	![Contatar o Suporte da Microsoft - Produto](./media/storsimple-contact-microsoft-support/IC777288.png)

4. Siga as instruções na tela para classificar corretamente sua solicitação e forneça uma descrição clara e específica do problema.

Depois que você enviar sua solicitação, um engenheiro de Suporte entrará em contato com você assim que possível para prosseguir com sua solicitação.

## Iniciar uma sessão de suporte no Windows PowerShell para StorSimple

Para solucionar problemas que possam ocorrer com o dispositivo StorSimple, você precisará contatar a equipe de Suporte da Microsoft. O Suporte da Microsoft talvez precise usar uma sessão de suporte para fazer logon em seu dispositivo.

Execute as seguintes etapas para iniciar uma sessão de suporte:

#### Para iniciar uma sessão de suporte

1. Acesse o dispositivo diretamente usando o console serial ou por meio de uma sessão de telnet de um computador remoto. Para fazer isso, execute as etapas em [Usar o PuTTY para conectar-se ao console serial do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Na sessão que será aberta, pressione a tecla **Enter** para obter um prompt de comando.

3. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**.

4. No prompt, digite a seguinte senha:

	`Password1`

5. No prompt, digite o seguinte comando:

	`Enable-HcsSupportAccess`

6. Uma cadeia de caracteres criptografada será apresentada a você. Copie a cadeia de caracteres para um editor de texto como o Bloco de Notas.

7. Salve a cadeia de caracteres e envie-a em uma mensagem de email ao Suporte da Microsoft.

> [AZURE.IMPORTANT]Você pode desabilitar o acesso ao suporte executando `Disable-HcsSupportAccess`. O dispositivo StorSimple também tentará desabilitar o acesso ao suporte oito horas após a sessão ser iniciada. É uma prática recomendada alterar as credenciais de seu dispositivo StorSimple após iniciar uma sessão de suporte.

<!---HONumber=AcomDC_0121_2016-->