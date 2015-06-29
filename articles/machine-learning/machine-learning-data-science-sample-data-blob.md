<properties 
	pageTitle="Dados de exemplo no Armazenamento de Blobs do Azure| Azure" 
	description="Dados de exemplo no Armazenamento de Blobs do Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="sunliangms,fashah,msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="sunliangms,fashah,msolhab,garye" /> 

#<a name="heading"></a>Dados de exemplo no Armazenamento de Blobs do Azure

Este documento aborda os dados de amostragem armazenados no Armazenamento do Blobs do Azure, baixando-o por meio de programação e realizando amostragem com um código Python de exemplo.  As etapas para isso são os seguintes:

1. Baixe os dados do Armazenamento do Blobs do Azure usando o serviço blob com o código Python de exemplo a seguir: 

	    from azure.storage import BlobService
    	import tables
    	
		STORAGEACCOUNTNAME= <storage_account_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		LOCALFILENAME= <local_file_name>		
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

    	#download from blob
    	t1=time.time()
    	blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    	blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    	t2=time.time()
    	print(("It takes %s seconds to download "+blobname) % (t2 - t1))

2. Leia os dados em um quadro de dados Pandas do arquivo baixado anteriormente.

		import pandas as pd

	    #directly ready from file on disk
    	dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Selecione amostras dos dados usando o `numpy`do `random.choice` da seguinte maneira:

	    # A 1 percent sample
    	sample_ratio = 0.01 
    	sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    	sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    	dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

	Agora você pode trabalhar com o quadro de dados acima com a amostra de 1 por cento para exploração e geração de recursos adicional.

##<a name="heading"></a>Conectar ao Aprendizado de Máquina do Azure

Você pode usar o seguinte código de exemplo para os reduzir os dados de exemplo e usá-los diretamente no AM do Azure:

1. Escrever o quadro de dados em um arquivo local

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Carregue o arquivo local para um blob do Azure usando o seguinte código de exemplo:

		from azure.storage import BlobService
    	import tables

		STORAGEACCOUNTNAME= <storage_account_name>
		LOCALFILENAME= <local_file_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

	    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
	    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
	    
	    try:
	   
	    #perform upload
	    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
	    
	    except:	        
		    print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Leia os dados de blob do Azure usando o AM do Azure *Reader Module* como mostra a captura de tela abaixo:
 
![reader blob][1]

[1]: ./media/machine-learning-data-science-sample-data-blob/reader_blob.png

<!--HONumber=49--> 