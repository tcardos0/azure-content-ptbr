<properties 
    pageTitle="Notas de versão do StorSimple - janeiro de 2015"
    description="Descreve os novos recursos, problemas e soluções alternativas da versão de Janeiro de 2015 do Microsoft Azure StorSimple."
    services="storsimple"
    documentationCenter="NA"
    authors="SharS"
    manager="adinah"
    editor="tysonn" /> <tags 
    ms.service="storsimple"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="TBD"
    ms.date="04/13/2015"
    ms.author="v-sharos" />


# Notas de versão do StorSimple - janeiro de 2015

## Visão geral

As notas de versão a seguir identificam as questões críticas em aberto para a versão de janeiro de 2015 do Microsoft Azure StorSimple. Elas também contêm uma lista das atualizações de firmware e software do StorSimple incluídas nesta versão. Esta é a segunda versão após o lançamento de Disponibilidade Geral \(GA\) do Microsoft Azure StorSimple.
  
Esta atualização não altera a versão do software do dispositivo físico da atualização de outubro. Ela continua a ser a versão 6.3.9600.17312. A imagem usada pela imagem do dispositivo virtual foi alterada nesta versão. Portanto, todos os novos dispositivos virtuais criados após 20/1/2015 mostrarão a versão 6.3.9600.17361.

Examine as seguintes informações contidas nas notas de versão para a atualização de janeiro de 2015.

> [AZURE.IMPORTANT] 

- Esta atualização não está disponível no Windows Update e não pode ser instalada da mesma forma que outras atualizações. O dispositivo não receberá essa atualização mesmo se você tiver aplicado as atualizações usando o Portal de Gerenciamento. Esta atualização será aplicada somente a dispositivos virtuais criados depois de 20 de Janeiro de 2015. 
 
- A versão de janeiro do StorSimple não contém nenhuma atualização para o dispositivo físico do StorSimple. Você ainda poderá aplicar quaisquer atualizações disponíveis do Windows para o dispositivo virtual, incluindo correções de segurança recentes, mas não verá uma alteração na versão para o dispositivo físico.

## O que há de novo na versão de janeiro

Esta atualização contém uma correção relacionada aos volumes que ficam offline no dispositivo virtual. \(Consulte [Problemas corrigidos nesta versão](#issues-fixed-in-the-january-release).\)

Esta atualização não contém novos recursos ou funcionalidades.

## Problemas corrigidos na versão de Janeiro

A tabela a seguir descreve o problema que foi corrigido nessa atualização.

|Nº|Recurso|Problema|Aplica-se ao dispositivo físico|Aplica-se ao dispositivo virtual 
|---|-------|-----|--------------------------|-------------------------
|1|Volumes offline|Quando as latências de nuvem altas persistirem por vários minutos, os volumes do dispositivo virtual StorSimple ficarão offline nos hosts. Essa correção aumenta o limite de latências de nuvem, minimizando as situações que fazem com que os volumes fiquem offline nos hosts.|Não|Sim  

## Problemas conhecidos na versão de janeiro

A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.
 
|Nº|Recurso|Problema|Comentários/soluções alternativas|Aplica-se ao dispositivo físico|Aplica-se ao dispositivo virtual 
|---|-------|-----|-------------------|--------------------------|-------------------------
|1|	Redefinição de fábrica|	Em alguns casos, quando você executa uma redefinição de fábrica, o dispositivo StorSimple pode estar travado e exibe esta mensagem: **a redefinição de fábrica está em andamento \(fase 8\)**. Isso acontece se você pressionar CTRL + C enquanto o cmdlet estiver em andamento.|	Não pressione CTRL + C após iniciar uma redefinição de fábrica. Se você já estiver nesse estado, entre em contato com o Suporte da Microsoft para as próximas etapas.|Sim|Não|
|2|Quorum de disco|	Em casos raros, se a maioria dos discos no compartimento de EBOD de um dispositivo 8600 for desconectada, fazendo com que não haja quorum de disco, em seguida, o pool de armazenamento ficará offline. Permanecerá offline, mesmo que os discos sejam reconectados.|Você precisará reiniciar o dispositivo. Se o problema persistir, entre em contato com o Suporte da Microsoft para as próximas etapas.|Sim |Não
|3|Falhas de instantâneo de nuvem|Em casos raros, um instantâneo de nuvem pode falhar com o erro **Limite máximo de backup atingido**. Isso ocorre se você exceder 255 clones online no mesmo dispositivo, a partir do mesmo volume original que foi excluído.||Sim|Sim
|4|ID de controlador incorreta|Quando a substituição do controlador é executada, o controlador 0 pode aparecer como controlador 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó par, a ID do controlador pode ser exibida inicialmente como a ID do controlador de pares. Em casos raros, esse comportamento pode ser percebido após uma reinicialização do sistema.|Nenhuma ação do usuário é necessária. Esta situação se resolverá depois que a substituição do controlador for concluída.|Sim|Não 
|5|	Gráficos de monitoramento de dispositivo|No serviço StorSimple Manager, os gráficos de monitoramento de dispositivos não funcionam quando a autenticação Básica ou NTLM é habilitada na configuração do servidor proxy para o dispositivo.|Modifique a configuração de proxy da Web para o dispositivo registrado no serviço StorSimple Manager para que a autenticação seja definida como NENHUMA. Para fazer isso, execute o Windows PowerShell para o cmdlet do StorSimple Set-HcsWebProxy.|Sim|Sim
|6|	Contas de armazenamento|Usar o serviço de Armazenamento para excluir a conta de armazenamento é um cenário sem suporte. Isso levará a uma situação na qual os dados do usuário não podem ser recuperados.|| Sim |	Sim
|7|Failover de dispositivo|	Não há suporte para vários failovers de um contêiner de volume do mesmo dispositivo de origem para diferentes dispositivos de destino.|	O failover de um único dispositivo inativo para vários dispositivos fará com que os contêineres de volume no primeiro dispositivo com failover percam a propriedade dos dados. Após o failover, esses contêineres de volume serão exibidos ou se comportarão de maneira diferente quando forem exibidos no Portal de Gerenciamento.|Sim|Não
|8|	Instalação|Durante o Adaptador StorSimple para instalação do SharePoint, você precisa fornecer um IP do dispositivo para que a instalação seja concluída com êxito.||Sim|Não
|9|	Proxy Web|Se a configuração de proxy Web tiver HTTPS como o protocolo especificado, a comunicação de serviço do dispositivo será afetada e o dispositivo ficará offline. Pacotes de suporte também serão gerados no processo, consumindo recursos significativos em seu dispositivo.|Verifique se a URL do proxy Web possui HTTP como o protocolo especificado. Para obter mais informações sobre como [Configurar proxy Web para seu dispositivo](https://msdn.microsoft.com/library/azure/dn764937.aspx).|Sim |Não
|10|Proxy Web|	Ao configurar e habilitar o proxy Web em um dispositivo registrado, você precisará reiniciar o controlador ativo em seu dispositivo.||	Sim |Não
|11|Latência de nuvem alta e alta carga de trabalho de E/S|Quando seu dispositivo StorSimple encontra uma combinação de latências de nuvem muito altas \(ordem de segundos\) e alta carga de trabalho de E/S, os volumes do dispositivo entram em um estado degradado e as E/Ss podem falhar com o erro "o dispositivo não está pronto".|Você precisará reiniciar os controladores de dispositivo manualmente ou executar um failover do dispositivo para se recuperar dessa situação.|Sim|Não

## Atualizações de dispositivo físico na versão de janeiro

Esta atualização não contém nenhuma outra alteração para o dispositivo StorSimple.

## Controlador SCSI \(SAS\) anexado em série e atualizações de firmware na versão de Janeiro

Esta versão não contém nenhuma atualização para o controlador SCSI \(SAS\) anexado em série ou para o firmware. A atualização de driver foi em outubro, versão 2014.

## Atualizações de dispositivo virtual na versão de janeiro

Esta versão contém uma imagem atualizada para o dispositivo virtual. Portanto, todos os dispositivos virtuais criados após 20 de janeiro de 2015 mostrarão a versão de software 6.3.9600.17361.


<!--HONumber=52-->