# Mariadb no Debian 11
        
# Validar repositórios e caso necessário atualizá-los: 
<code>cat /etc/apt/sources.list</code>
        
# Instalar os seguintes pacotes de dependências:
<code>apt -y install vim wget make cmake gcc g++ htop nmap libncurses5-dev net-tools unzip tar</code>
        
# Crie uma pasta e dentro dela faça o download o MariaDb
        
<pre><code>mkdir instaladores
cd instaladores
scp -P44422 root@200.250.141.179:/compartilhado/deitos/mariadb-10.4.13.tar.gz .</code></pre>
        
# Descompacte o arquivo baixado e acesse o diretório de instalação:
<pre><code>tar zxpvf mariadb-10.4.13.tar.gz
cd mariadb-10.4.13</code></pre>
        
# Inicie o processo de configuração do MariaDb
<pre><code>cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mariadb -DMYSQL_DATADIR=/usr/local/mariadb/data -DSYSCONFDIR=/etc/mariadb.cnf</code></pre>
        
### Se o comando der certo aparece algo como :
<pre><code>-- Configuring done
-- Generating done
-- Build files have been written to: /usr/src/mariadb-10.4.13</code></pre>

# Execute o seguinte comando para preparar a instalação:
<pre><code>if [ $(cat /proc/cpuinfo | grep cores | head -1 | awk '{print $4}') &lt&gt [0-9] ]; then (for i in `cat /proc/cpuinfo | grep cores | head -1 | awk '{print $4}'`; do   make -j$i VERBOSE=1; done;); else make VERBOSE=1; fi;</code></pre>
        
### Caso de certo aparecerá algo como:
<pre><code>[100%] Built target mariabackup
make[1]: Saindo do diretório '/instaladores/mariadb-10.4.13'
/usr/bin/cmake -E cmake_progress_start /instaladores/mariadb-10.4.13/CMakeFiles 0</code></pre>
        
# Execute o comando para instalar:
<pre><code>if [ $(cat /proc/cpuinfo | grep cores | head -1 | awk '{print $4}') &lt&gt [0-9] ]; then (for i in `cat /proc/cpuinfo | grep cores | head -1 | awk '{print $4}'`; do   make install -j$i VERBOSE=1 && echo $?; done;); else make install VERBOSE=1 && echo $?; fi;</code></pre>
        
### Caso de certo aparecerá algo como:
<pre><code>-- Installing: /usr/local/mariadb/lib/pkgconfig/mariadb.pc
-- Installing: /usr/local/mariadb/share/aclocal/mysql.m4
-- Installing: /usr/local/mariadb/support-files/mysql.server
0</code></pre>
        
# Crie o usuário e grupo para o mariadb:
<pre><code>userdel mariadb
groupdel mariadb
groupadd mariadb
useradd -d /usr/local/mariadb -s /bin/false -g mariadb mariadb</code></pre>
        
# Adicionar o conteúdo abaixo dentro do arquivo /etc/mariadb.cnf: 
<pre><code>[mysql]
default-character-set = latin1

[mysqld]
sql_mode="NO_ENGINE_SUBSTITUTION"
default_storage_engine=myisam
default_tmp_storage_engine=myisam
transaction-isolation=READ-COMMITTED
 ### definições de diretórios ###
datadir=/usr/local/mariadb/data/
basedir=/usr/local/mariadb/data/
port = 3306
socket = /tmp/mysql.sock
 ### opções de performance e idioma ###
key_buffer=16M
lc_messages=pt_BR
lc_time_names=pt_BR
skip-name-resolve
skip-host-cache
event-scheduler=1
performance_schema
max_allowed_packet = 512M
max_connections = 150
read_rnd_buffer_size = 2M
join_buffer_size = 98M
sort_buffer_size = 4M
query_prealloc_size = 16M
query_cache_type=1
query_cache_limit = 48M
open_files_limit = 4096
thread_cache_size=7
tmp_table_size = 128M
max_heap_table_size = 128M

[mysqldump]
quick
</code></pre>
        
# Salvar o arquivo mariadb.cnf e configurar as permissões necessárias :
<pre><code>chown mariadb.mariadb /etc/mariadb.cnf
chmod 660 /etc/mariadb.cnf
cd /usr/local/mariadb
chown mariadb.mariadb * -R
chmod 777 * -R</code></pre>

# A seguir execute o seguinte comando :
<pre><code>scripts/mariadb-install-db --user=mariadb --basedir=/usr/local/mariadb --datadir=/usr/local/mariadb/data
rm -rf /usr/local/mariadb/mariadb.cnf
chown mariadb.mariadb * -R
chmod 777 * -R
bin/mariadbd-safe --user=mariadb &</code></pre>

### Pressione Enter e acompanhe o log com:
<pre><code>tail -f data/$(hostname).err</code></pre>

### Se ocorrer tudo ok aparecerá algo como:
<pre><code>2021-11-09 15:20:17 0 [Note] /usr/local/mariadb/bin/mysqld: ready for connections.
Version: '10.4.13-MariaDB'  socket: '/tmp/mysql.sock'  port: 3306  Source distribution</code></pre>

Pressione Ctrl + c para voltar a linha de comando
        
# Copie o arquivo de inicialização para o diretório de inicialização do Linux:
<pre><code>cp support-files/mysql.server /etc/init.d/mariadb</code></pre>

# Agende o arquivo de inicialização do Mariadb juntamente ao Linux:
<pre><code>update-rc.d mariadb defaults 98 98</code></pre>

# Reinicie o banco de dados:
<pre><code>/etc/init.d/mariadb restart</code></pre>

#Acesse o banco de dados para criar os usuários para acesso e restaurar dumps:
<pre><code>./mysql -u root -p (dar enter sem senha mesmo)</code></pre>
        
#### Dúvidas: alexsandro.deitos@linx.com.br
     
