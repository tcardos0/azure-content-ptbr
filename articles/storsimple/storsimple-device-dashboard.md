<properties 
   pageTitle="Usar o painel de dispositivo do StorSimple Manager"
   description="Descreve o painel de dispositivo do StorSimple Manager e como usá-lo para exibir iniciadores conectados e localizar o número de série e o IQN do dispositivo."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/21/2015"
   ms.author="alkohli" />

# Usar o painel de dispositivo do StorSimple Manager

## Visão geral

O painel de dispositivo do StorSimple Manager proporciona uma visão geral das informações de um dispositivo StorSimple específico, ao contrário do painel de serviço, que fornece informações sobre todos os dispositivos incluídos em sua solução Microsoft Azure StorSimple.

![Página Painel de dispositivo](./media/storsimple-device-dashboard/HCS_DeviceDashboardPage.png)

O painel contém as seguintes informações:

- **Área do gráfico**: você pode ver as métricas de armazenamento relevantes na área do gráfico, na parte superior do painel. Nesse gráfico, você pode exibir métricas para o armazenamento principal total (a quantidade de dados gravados pelos hosts no seu dispositivo) e o armazenamento em nuvem total consumido pelo dispositivo em um período.

     Nesse contexto, o *armazenamento principal* se refere ao volume total de dados gravados pelo host. Ele pode incluir dados armazenados localmente e dados em camadas na nuvem. O *armazenamento em nuvem*, por outro lado, é uma medição do volume total de dados armazenados na nuvem. Isso inclui backups e dados em camadas. Tanto para o armazenamento principal, quanto para o armazenamento em nuvem, os volumes mostrados serão baseados na frequência de rastreamento que você configura. Por exemplo, se você escolher a frequência de uma semana, o gráfico mostrará dados para cada dia da semana anterior.
 
	 É possível configurar o gráfico como se segue:

	 - Para ver o volume de armazenamento em nuvem consumido ao longo do tempo, selecione a opção **ARMAZENAMENTO EM NUVEM USADO**. Para ver o armazenamento total que foi escrito pelo host, selecione a opção **ARMAZENAMENTO PRINCIPAL USADO**. Na ilustração, ambas as opções são selecionadas. Portanto, o gráfico mostra volumes para armazenamento principal e em nuvem. 
	 - Use o menu suspenso no canto superior direito do gráfico para especificar um período de 1 semana, 1 mês, 3 meses ou 1 ano. Observe que o gráfico no nível superior é atualizado apenas uma vez por dia e, portanto, refletirá os totais do dia anterior.

- **Visão geral de uso**: na área de visão geral de uso, você pode ver o volume de armazenamento principal usado, o volume do armazenamento provisionado e a capacidade máxima de armazenamento do seu dispositivo. Ao comparar esses números de uso com o volume máximo de armazenamento disponível, você pode ver instantaneamente se precisa de mais armazenamento. Observe que essa visão geral é atualizada a cada 15 minutos e, devido à diferença na frequência de atualização, ela pode mostrar diferentes números dos mostrados na área do gráfico acima, que é atualizada diariamente. Para obter mais informações, consulte a seção [Monitor](https://msdn.microsoft.com/library/dn757759.aspx).

- **Alertas**: a área de alertas inclui uma visão geral dos alertas de seu dispositivo. Os alertas são agrupados por severidade, e uma contagem do número de alertas é fornecida em cada nível de severidade. Clicar na severidade do alerta abre uma exibição da guia de alertas em escopo, mostrando apenas os alertas desse nível de severidade para esse dispositivo.

- **Trabalhos**: a área de trabalhos mostra o resultado da atividade de trabalho recente. Isso pode assegurar que o sistema está funcionando conforme esperado ou pode informar que você precisa tomar uma medida corretiva. Para obter mais informações sobre trabalhos concluídos recentemente, clique em **Trabalhos bem-sucedidos nas últimas 24 horas**.

- A área **visão rápida** à direita do painel fornece informações úteis, como modelo do dispositivo, número de série, status, descrição e número de volumes.

Você também pode configurar o failover e exibir os iniciadores conectados no painel de dispositivo.

As tarefas comuns que podem ser executadas nessa página são:

- Exibir iniciadores conectados

- Localizar o número de série do dispositivo

- Localizar o IQN de destino do dispositivo

## Exibir iniciadores conectados

Você pode exibir os iniciadores iSCSI conectados ao seu dispositivo clicando no link **Exibir iniciadores conectados** fornecido na área **visão rápida** do painel de dispositivo. Essa página fornece uma listagem tabular dos iniciadores que se conectaram com êxito ao dispositivo. Para cada iniciador, você pode ver:

- O IQN (Nome Qualificado iSCSI) do iniciador conectado.

- O nome do ACR (registro de controle de acesso) que permite esse iniciador conectado.

- O endereço IP do iniciador conectado.

- As interfaces de rede às quais o iniciador está conectado no seu dispositivo de armazenamento. Isso pode variar de DATA 0 a DATA 5.

- Todos os volumes que o iniciador conectado tem permissão para acessar de acordo com a configuração ACR atual.

Caso você veja iniciadores inesperados nessa lista ou não veja os esperados, revise a configuração ACR. Um máximo de 512 iniciadores pode se conectar ao dispositivo.

## Localizar o número de série do dispositivo

O número de série do dispositivo pode ser necessário quando você configura o MPIO (Microsoft Multipath I/O) no dispositivo. Execute as etapas a seguir para localizar o número de série do dispositivo.

#### Para localizar o número de série do dispositivo

1. Navegue para **Dispositivos** > **Painel**.

2. No painel à direita do painel, localize a área **visão rápida**.

3. Role para baixo e localize o número de série.

## Localizar o IQN de destino do dispositivo

Talvez você precise do IQN de destino do dispositivo ao configurar o protocolo CHAP (Challenge Handshake Authentication Protocol) no seu dispositivo StorSimple. Execute as etapas a seguir para localizar o IQN de destino do dispositivo.

### Para localizar o IQN de destino do dispositivo

1. Navegue para **Dispositivos** > **Painel**.

1. No painel à direita do painel, localize a área **visão rápida**.

1. Role para baixo e localize o IQN de destino.

## Próximas etapas

[Saiba mais sobre o painel de serviço do StorSimple Manager](storsimple-service-dashboard.md).

<!---HONumber=July15_HO4-->