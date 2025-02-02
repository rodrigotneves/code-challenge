## ELT Report for Lighthouse program

### Planning
    
#### Definição das ferramentas
    Docker
        PostgreSQL (2 containers, origem e destino)
    Python 3.11
        Meltano
        Airflow (Instalado e regido pelo Meltano)

#### Reorganizando repositório
    O repositório foi reorganizado para simular uma situação mais real, a fonte dos dados, o meio onde são instaladas as ferramentas de ELT e o destino.

#### Três novas pastas foram criadas na raíz do projeto

##### data_source
    Os dados de origem, tanto do script de criação do banco de dados, quanto o csv provido, foram movidos para esta pasta
##### elt
    Irá abrigar a pasta do meltano e dependencias.
##### destiny_db
    Recebe o volume do banco de dados de destino

### Instalação das ferramentas
    Instalação do python 3.11
        apt update & apt install python3 python3-pip python3-venv
    
    Instalação do tmux
    apt update & apt install tmux


    Dentro da pasta elt:
    
    criado ambiente virtual
        python3 -m venv .venv
    atualizar pip
        pip install -U pip
    ativar ambiente virtual
        source .venv/bin/activate (Linux)
        .\venv\scripts\activate.ps1 (Powershell windows)
    instalar meltano
        pip install meltano
    
    dentro da pasta meltano:
        meltano init .

    adicionar plugins
        meltano add extractor tap-postgres
        meltano add extractor tap-csv
        meltano add loader target-postgres
        meltano add loader target-csv
        meltano add utility airflow
    
    configurar plugins
        Airflow
            meltano config airflow set --interactive (Pular se não precisar alterar nenhuma configuração)
            meltano invoke airflow:initialize
            Criar usuário:
                meltano invoke airflow users create -u admin@localhost -p password --role Admin -e admin@localhost -f admin -l admin
            
            Rodar airflow:
                Em produção o airflow, instalado localmente, deve ter o serviço configurado para subir o webservice e scheduler juntamente com o sistema operacional, para fins do desafio, estou usando o tmux para facilitar a criação de sessões do terminal.

                tmux new -s airflow - criará sessão chamada Airflow e irá entrar na mesma
                Ctrl+b, Shift+F5 - dividirá a tela ao meio verticalmente
                Ctrl+b, " - (Aspas duplas) dividirá a segunda tela ao meio, horizontalmente
                Ctrl+b, Q, 1 - Seleciona a tela 1
                    Ir até a pasta do projeto meltano
                    meltano invoke airflow webserver
                Ctrl+b, Q, 2 - Seleciona a tela 2
                    Ir até a pasta do projeto meltano
                    meltano invoke airflow scheduler
                Ctrl+b, Q, 0 - Seleciona a tela 2
                
                Temos o airflow rodando em localhost:8080
                Fazer o login com user admin@localhost e senha password

### Configurar extratores