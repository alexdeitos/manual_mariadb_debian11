# Mariadb no Debian 11
<p>Um manual de instalação do mariadb-10.4.13 no Debian 11</p>

# Validar repositórios e caso necessário atualizá-los: 

<code>cat /etc/apt/sources.list</code>

# Instalar os seguintes pacotes de dependências:

<code> 
  apt -y install vim make cmake gcc g++ htop nmap libncurses5-dev net-tools unzip tar
</code>

# Crie uma pasta e dentro dela faça o download o MariaDb

  
    mkdir instaladores
    cd instaladores
    wget (link para o download)/mariadb-10.4.13.tar.gz
  


# Descompacte o arquivo baixado e acesse o diretório de instalação:
<code>
  tar zxpvf mariadb-10.4.13.tar.gz
  cd mariadb-10.4.13
</code>

# Inicie o processo de configuração do MariaDb

<code>
  cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mariadb -DMYSQL_DATADIR=/usr/local/mariadb/data -DSYSCONFDIR=/etc/my.cnf
</code>

### Se o comando der certo aparece algo como :
	-- Configuring done
	-- Generating done
	-- Build files have been written to: /usr/src/mariadb-10.4.13
  
# Execute o seguinte comando:
<code>
  if [ $(cat /proc/cpuinfo | grep cores | head -1 | awk '{print $4}') <> [0-9] ]; then (for i in `cat /proc/cpuinfo | grep cores | head -1 | awk '{print $4}'`; do   make -j$i VERBOSE=1; done;); else make VERBOSE=1; fi;<br/>
</code>

### Caso de certo aparecerá algo como:
  
    [100%] Built target mariabackup
    make[1]: Leaving directory '/instaladores/mariadb-10.4.13'
    /usr/bin/cmake -E cmake_progress_start /instaladores/mariadb-10.4.13/CMakeFiles 0
# Execute o comando :
<code>
  if [ $(cat /proc/cpuinfo | grep cores | head -1 | awk '{print $4}') <> [0-9] ]; then (for i in `cat /proc/cpuinfo | grep cores | head -1 | awk '{print $4}'`; do   make install -j$i VERBOSE=1 && echo $?; done;); else make install VERBOSE=1 && echo $?; fi;</code>

### Caso de certo aparecerá algo como:
  <pre><code>
  -- Installing: /usr/local/mariadb/lib/pkgconfig/mariadb.pc  
  -- Installing: /usr/local/mariadb/share/aclocal/mysql.m4  
  -- Installing: /usr/local/mariadb/support-files/mysql.server
  0</code></pre>

# Crie o usuário e grupo para o mariadb:
  <pre><code>
  
    userdel mariadb
    groupdel mariadb
    groupadd mariadb
    
    useradd -d /usr/local/mariadb -s /bin/false -g mariadb mariadb
    
  </code></pre>

# Adicionar o conteúdo abaixo dentro do arquivo /etc/mariadb.cnf: 
  <pre><code>
[mariadb]
default-character-set = latin1

[mariadbd]

sql_mode="NO_ENGINE_SUBSTITUTION"
default_storage_engine=myisam
default_tmp_storage_engine=myisam

collation-server = latin1_swedish_ci
character-set-server = latin1
group_concat_max_len=9000
secure-file-priv = ""
interactive_timeout=86000
wait_timeout=86000


## Caso queira alterar a porta do mariadb edite abaixo
port = 3306
socket = /tmp/mariadb.sock

## Diretório de data do mariadb edite-o conforme o sistema em que está sendo instalado
datadir = /usr/local/mariadb/data/

## Diretório base do mariadb edite-o conforme o sistema em que está sendo instalado
basedir = /usr/local/mariadb

## Local de armazenamento do tablespace, onde será criado o ibdata1 (antigo innodbdata). Edite-o conforme o sistema em que está sendo instalado
innodb_data_home_dir = /usr/local/mariadb/data/

## Diretório que serão armazenados os ib_logfiles edite-o conforme o sistema em que está sendo instalado
innodb_log_group_home_dir = /usr/local/mariadb/data/

# Tipo de isolação, anteriormente REPEATABLE-READ
transaction-isolation=READ-COMMITTED

# Linguagem alterada para Pt-br
lc_messages=pt_BR
lc_time_names=pt_BR

# Ignora procura por DNS
skip-name-resolve

#Ignora cache em host, evitando que terminais fiquem bloqueados
skip-host-cache

# Habilita agendador mariadb
event-scheduler=1

# Analise do banco http://dev.mariadb.com/doc/refman/5.5/en/performance-schema.html
performance_schema

# Tamanho máximo da inserção em uma coluna
max_allowed_packet = 32M

# Número Máximo de conexões
max_connections = 150

# Valor Padrão 1 devido a ACID
innodb_flush_log_at_trx_commit=1

# Descomentar linha abaixo caso o mariadb não esteja carregando tabelas que foram marcadas como corrompidas
# innodb_force_load_corrupted

# Linha que define force para iniciar banco de dados, usado somente
# quando o banco de dados esta com problemas. Pode ser usado de 1 a 6
# no lugar do X. Comece usando o 4 que e um dos melhores
# innodb_force_recovery=X

# Buffer para grandes consultas que contenham order BY, agilizando o processo, por Default é 256k
read_rnd_buffer_size = 2M

# Alocacao de memoria para grandes joins, so plausivel quando os joins nao tem index
join_buffer_size = 98M

# Buffer para grandes consultas que contenham order BY, agilizando o processo, por Default é 2M
sort_buffer_size = 4M

# Método de Escrita de dados e escrita de logs. Tenta O_DIRECT - Forma padrão fdatasync
# innodb_flush_method=O_DSYNC

# Pré-alocação de Memória para grandes querys
query_prealloc_size = 16M

# Habilitar cache para todos os SELECTS menos para os que informam SQL_NO_CACHE
query_cache_type=1

# Tamanho do maximo de cache para querys SELECT
query_cache_size = 80M

# Tamanho maximo de cache para querys individuais
query_cache_limit = 48M

# Usando um arquivo por tabela
innodb_file_per_table = 1

# Maximo de tabela abertas pelo mariadb
innodb_open_files = 1000

# Maximo de arquivos abertos pelo mariadb
open_files_limit = 4096

# Salva no log de erros os deadlocks ocorridos
innodb_print_all_deadlocks

## Memória destinada ao cache de dados e index. Quanto maior for este parametro, menos recursos de I/O serão utilizados do HD.
## Utilizar 60 % da memória fisica do servidor
innodb_buffer_pool_size = 1G

## ib_logfile(0/1): São as alterações que estão feitas nas transações innodb
## Utilizar 25% do informado do innodb_buffer_pool_size
innodb_log_file_size = 100M

# Tamanho do cache que o innodb vai armazenar para gravação dos ib_logfiles.
innodb_log_buffer_size = 8M

# Tempo que o banco irá esperar até informar o timeout
innodb_lock_wait_timeout= 180

## Concorrência entre Threads(cpu). É recomendado 2x o número de núclos do processador. Por padrão é 10
## Para ver o número de cores no Linux digite: cat /proc/cpuinfo | grep "cores" | head -1
innodb_thread_concurrency=4

## Número de Threads(cpu) que poderão ser utilizadas para leitura. Utilizar 3x o número de núcleos do processador
## Para ver o número de cores no Linux digite: cat /proc/cpuinfo | grep "cores" | head -1
innodb_read_io_threads=8

## Número de Threads(cpu) que poderão ser utilizadas para escrita. Utilizar 3x o número de núcleos do processador
## Para ver o número de cores no Linux digite: cat /proc/cpuinfo | grep "cores" | head -1
innodb_write_io_threads=8

# Número de reutilização de threads, o recomendado é 5% do max_connections
thread_cache_size=7

## Capacidade IO do HD, por padrão é 200. Se SATA = 100. Se SAS = 200. Se RAID 5, 10 = 500
innodb_io_capacity=200

# Tamanho da tabela temporária em disco
tmp_table_size = 256M

# Máximo que uma tabela temporária pode crescer
max_heap_table_size = 1000M

[mariadbdump]
quick
max_allowed_packet = 32M
  </code></pre>
### Na linha :
  <pre><code>innodb_buffer_pool_size</code></pre> 
### altere para a quantidade de memória compatível com o servidor recomendado ser 60% da memória física.

# Salvar o arquivo mariadb.cnf e configurar as permissões necessárias :
  <pre><code>
      chown mariadb.mariadb /etc/my.cnf
      chmod 660 /etc/my.cnf

      cd /usr/local/mariadb
      chown mariadb.mariadb * -R
      chmod 777 * -R  
  </code></pre>
  
  # A seguir execute o seguinte comando :
  <pre><code>
     scripts/mariadb-install-db --user=mariadb --basedir=/usr/local/mariadb --datadir=/usr/local/mariadb/data
     rm -rf /usr/local/mariadb/my.cnf
     chown mariadb.mariadb * -R
     chmod 777 * -R
     bin/mariadbd-safe --user=mariadb &
  </code></pre>
  
  ### Acompanhe o log com:
  <pre><code>
  tail -f data/$(hostname).err
  </code></pre>
  
  ### Se ocorrer tudo ok aparecerá algo como:
  <pre><code>
    2013-04-17 16:33:43 19134 [Note] Event Scheduler: scheduler thread started with id 1
  </code></pre>
  
  # Copie o arquivo de inicialização para o diretório de inicialização do Linux:
  
  <pre><code>cp support-files/mysql.server /etc/init.d/mariadb</code></pre>
  
  # Agende o arquivo de inicialização do Mariadb juntamente ao Linux:
  <pre><code>update-rc.d mariadb defaults 98 98</code></pre>
  
  # Reinicie o banco de dados:
  <pre><code>/etc/init.d/mariadb restart</code></pre>
  
  

  
  
