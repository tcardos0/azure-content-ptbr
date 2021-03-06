
| **Identificador de limite** | **Limite** | **Comentários** |
|-----------------------------------------------------------------------------------------------|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Capacidade total (incluindo nuvem) | Até 64 TB por dispositivo virtual |
| Número máximo de credenciais de conta de armazenamento por dispositivo | 1 | |
| Número máximo de volumes/compartilhamentos | 16 | |
| Tamanho mínimo de volume/compartilhamento em camadas | 500 GB | |
| Tamanho máximo de volume/compartilhamento em camadas | 20 TB | |
| Tamanho mínimo de volume/compartilhamento fixado localmente | 50 GB | |
| Tamanho máximo de volume/compartilhamento fixado localmente | 2 TB | |
| Número máximo de conexões iSCSI dos iniciadores | 512 | |
| Número máximo de registros de controle de acesso por dispositivo | 64 | |
| Número máximo de backups retidos pelo dispositivo virtual na pasta *.backups* do servidor de arquivos | 5 | Isso inclui os backups manuais e os backups agendados mais recentes (gerados pela política de backup padrão). |
| Número máximo de backups agendados mantidos pelo dispositivo | 55 | 30 backups diários<br>12 backups mensais<br>13 backups anuais |
| Número máximo de backups manuais mantidos pelo dispositivo | 45 | |
| Número máximo de volumes que podem ser processados paralelamente para backup ou restauração | 3 | Se houver mais de 3 volumes, eles serão processados sequencialmente conforme os slots de processamento ficarem disponíveis. |
| Tempo de recuperação de restauração | Restauração rápida | A restauração é baseada no mapa de calor e depende do tamanho do volume.<br>Operações de backup podem ocorrer enquanto uma operação de restauração está em andamento. |

<!---HONumber=AcomDC_0121_2016-->