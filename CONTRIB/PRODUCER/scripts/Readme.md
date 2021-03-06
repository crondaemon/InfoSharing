# Script PRODUCER

## Generazione formati STIX 1.2 e 2
Lo script python _CS\_build\_stix-from\_files.py_ legge un file di testo contenente gli IoC (sha256, sha1, md5, domain, url, ip, email) e genera i file STIX 1.2 (XML) e STIX 2 (JSON) da iniettare sulla rete STIX/TAXII della community Cyber Saiyan (al momento la rete supporta esclusivamente il formato STIX 1.2)

Per poter utilizzare lo script è necessario installare le seguenti dipendenze (testato su Ubuntu Server LTS 16.04 e 18.04)
```
# cabby 
sudo pip install cabby

mkdir src
cd src/

# python-cybox
git clone https://github.com/CybOXProject/python-cybox.git
cd python-cybox/
sudo python setup.py install

cd ..

# python-stix
git clone https://github.com/STIXProject/python-stix.git
cd python-stix
sudo python setup.py install 

# stix
sudo pip install stix #già installato come dipendenza cabby, just for...
sudo pip install stix2

# validators
sudo pip install validators
```

Prima di eseguire lo script effettuare le seguenti operazioni
* nel file _CS\_build\_stix-from\_files.py_ aggiornare le variabili MyTITLE, DESCRIPTION, IDENTITY
* fare un clear del file _CS-ioc.txt_ ```~$ > CS-ioc.txt```
* inserire gli IoC supportati (sha256, sha1, md5, domain, ipv4, url) nel file _CS-ioc.txt_. Lo script parsa il file linea per linea ed usa delle espressioni regolari per validare gli IoC; commenti o IoC malformati vengono ignorati
* eseguire lo script  ```python -W ignore CS_build_stix.py```

Dopo l'esecuzione sono generati due file (esempi nella dir)
* _package.stix_: file STIX 1.2 [XML](/CONTRIB/PRODUCER/scripts/package.stix)
* _package.stix2_: file STIX 2 [JSON](/CONTRIB/PRODUCER/scripts/package.stix2)

## PUSH STIX 1.2
Dopo aver creato i file STIX è necessario fare il PUSH (via Cabby in questo esempio) del file STIX 1.2 (unico formato supportato al momento) sulla rete STIX/TAXII della community Cyber Saiyan.
Di seguito il comando da eseguire che richiesde una password (il PUSH è autenticato); nel caso in cui si volesse contribuire unirsi al [gruppo Telegram](https://t.me/joinchat/Av4DDFjVkRC60YH_Lq-WVw)
```
taxii-push --discovery https://infosharing.cybersaiyan.it:9000/services/discovery --dest community --username community --password <TO-BE-SENT> --content-file package.stix
```

La verifica dell'effettivo caricamento degli IoC sulla rete può essere fatta con Cabby in maniera non autenticata (tempo massimo di update 10 minuti)
```
taxii-poll --host infosharing.cybersaiyan.it --https --collection CS-COMMUNITY-TAXII --discovery /taxii-discovery-service
```
