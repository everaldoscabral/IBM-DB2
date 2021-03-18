# IBM-DB2
Neste tutorial vamos abordar as etapas de como monitorar o banco de dados DB2 da IBM através do Zabbix.

# 1 – Instalação do UnixODBC
 
De início devemos instalar o pacote UnixODBC, lembrando que devemos está com permissão de “root”.

yum install -y unixODBC

# 2 – Baixando o pacote de Instalação do DB2

Para facilitar criei um repositório com o pacote de Instalação e vamos descompactar. Mas pode ser baixado
direto do site da IBM (https://www-304.ibm.com/support/docview.wss?uid=swg21418043)

wget servicos.itpsolucoes.com.br/downloads/ibm_data_server_runtime_client_linuxx64_v11.1.tar.gz

tar xvf ibm_data_server_runtime_client_linuxx64_v11.1.tar.gz

# 3 – Realizando a Instalação do DB2

Após baixarmos e descompactarmos agora chegou a hora de realizarmos a Instalação.

rtcl/db2_install –p

Caso no momento da Instalação dê erro, basta executar o procedimento abaixo e tente continuar a Instalação
novamente:

vim /etc/hosts e adicionar 127.0.0.1 NomedoHost

# 4 – Criando um Usuário DB2

Vamos criar um Usuário específico para o monitoramento.

* mkdir -p /home/db2
* adduser -m -d /home/db2/db2inst db2inst
* passwd db2inst (informar uma senha para o usuário)
* /opt/ibm/db2/V11.1/instance/db2icrt db2inst

# 5 – Editando os Arquivos de conexão ODBC

O monitoramento do DB2 tem uma peculariadade, ele faz uma chamada de um outro arquivo que é onde
passamos os dados de conexão.

vim /home/db2/db2inst/sqllib/cfg/db2cli.ini

[TOOLSDB]

Database = TOOLSDB (nomedaestancia)

Protocol = TCPIP

Hostname = IP do Servidor DB2

ServiceName = 50000 (porta de conexão)

# 6 – Editando os parâmetros de conexão ODBC

Agora iremos editar os parâmetros de conexão ODBC.

vim /etc/odbc.ini


[TOOLSDB]

Description = DB2 Driver

Driver = DB2

Server = IP do Servidor DB2

vim /etc/odbcinst.ini


[DB2]

Description = DB2 Driver

Driver = /opt/ibm/db2/V11.1/lib32/libdb2.so


Driver64 = /opt/ibm/db2/V11.1/lib64/libdb2.so

FileUsage = 1

DontDLClose = 1


Por último executar:

export DB2INSTANCE="db2inst"

# 7 – Testando a conexão com o Servidor DB2

Após realizarmos todas as configurações chegou o momento de realizarmos o teste de conexão. Lembrando que,
o Usuário e Senha é de conexão do banco, sugerido que o Usuário tenha permissão FULL de consulta.

isql -v nomedaestanacia usuario senha

Se o teste de conexão tiver êxito receberá a mensagem abaixo:

IMAGEM!

# 8 – Associando o host ao Template

Após criarem o host é necesário passar alguns parâmetros como macro no host. E devemos associar o host ao
template TEMPLATE-IBM-DB2

(https://www.facebook.com/download/475123866226854/zbx_export_templates.xml?hash=Acp_IlJHPkByUGe
u)

* {$DB2_PASSWORD}
* {$DB2_USER}
* {$DSN1} (mesmo nome da estância)
