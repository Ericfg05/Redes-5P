*Documentação*

**0-Inicio**
Para criarmos um servidor de redes, em que, o serviço DHCP, DNS e o de segurança Firewall em docker precisamos configurar alguns arquivos.

***1-Introdução***

DHCP: é um protocolo que consede endereços de ips para as maquinas que conectam nas redes.
DNS: é um protocolo que converte o ip da maquina em um domínio, assim facilitando a memorização para acessar o seu site favorito.
Firewall: Um software em que controla o acesso a rede, ele tem como função bloquear ou aceitar tudo que entra no seu computador ou roteador (Famosos Porteiro)

Para testarmos ambos os serviço de redes, precisamos instalar o docker. E para isso utilizamos o comando: sudo apt install docker-io. Logo após, iremos utilizar alguns comando de configuração para Dockerfile, que podem ser visto no item 2(Desenvolvimento)

***2-Desenvolvimento***

Para iniciar os teste você deve criar uma pastar com o comando "mkdir nome_pasta", assim pode abrir o visual studio code nessa pasta, com o comando "code .". No code, crie um arquivo com o nome *Dockefile* (Esse nome e dado para todos os arquivos que serão feitos as configurações) e cole o codigo abaixo:

FROM ubuntu:latest

#Atualizando o repositório e instalando o servidor DHCP
RUN apt-get update && apt-get install -y isc-dhcp-server

#Copiando os arquivos de configurações do DHCP para dentro do container
COPY  isc-dhcp-server /etc/default/isc-dhcp-server
COPY /dhcpd.conf /etc/dhcp/dhcpd.conf
COPY /dhcpd.leases /var/lib/dhcp
COPY dhclient.conf /etc/dhcp/dhclient.conf
#Expondo a porta usada pelo servidor DHCP
EXPOSE 67
#Comando para iniciar o servidor DHCP
CMD ["dhcpd", "-f", "-d", "--no-pid"]

Lembrando que deve ser criado mais alguns arquivos para que funcione, e no proprio code pode ser criados. Crie o dhcpd.conf e cole o codigo de configuração do dhcp:

subnet 172.17.0.0 netmask 255.255.0.0 {
    range 172.17.0.10 172.17.0.100;
    option routers 172.17.0.10;
    option subnet-mask 255.255.0.0;
    option broadcast-address 172.17.255.255;
    default-lease-time 10;
    max-lease-time 10;
}

subnet 192.168.0.0 netmask 255.255.255.0 {
  range 192.168.0.100 192.168.0.200;
  option routers 192.168.0.1;
  option domain-name-servers 8.8.8.8;
  option domain-name "ericfg3.com";
}

 *crie também o isc-dhcp-server e cole o codigo a abaixo:*
 
 #Defaults for isc-dhcp-server (sourced by /etc/init.d/isc-dhcp-server)

#Path to dhcpd's config file (default: /etc/dhcp/dhcpd.conf).
#DHCPDv4_CONF=/etc/dhcp/dhcpd.conf
#DHCPDv6_CONF=/etc/dhcp/dhcpd6.conf

#Path to dhcpd's PID file (default: /var/run/dhcpd.pid).
#DHCPDv4_PID=/var/run/dhcpd.pid
#DHCPDv6_PID=/var/run/dhcpd6.pid

#Additional options to start dhcpd with.
#Don't use options -cf or -pf here; use DHCPD_CONF/ DHCPD_PID instead
#OPTIONS=""

#On what interfaces should the DHCP server (dhcpd) serve DHCP requests?
#Separate multiple interfaces with spaces, e.g. "eth0 eth1".
INTERFACESv4="enp4s1"
INTERFACESv6=""

Crie o dhclient.conf:

# Configuration file for /sbin/dhclient.
# This is a sample configuration file for dhclient. See dhclient.conf's
#man page for more information about the syntax of this file
#and a more comprehensive list of the parameters understood by
#dhclient.

#Normally, if the DHCP server provides reasonable information and does
#not leave anything out (like the domain name, for example), then
#few changes must be made to this file, if any.
#

option rfc3442-classless-static-routes code 121 = array of unsigned integer 8;

send host-name = gethostname();
request subnet-mask, broadcast-address, time-offset, routers,
	domain-name, domain-name-servers, domain-search, host-name,
	dhcp6.name-servers, dhcp6.domain-search, dhcp6.fqdn, dhcp6.sntp-servers,
	netbios-name-servers, netbios-scope, interface-mtu,
	rfc3442-classless-static-routes, ntp-servers;

#send dhcp-client-identifier 1:0:a0:24:ab:fb:9c;
#send dhcp-lease-time 3600;
#supersede domain-name "fugue.com home.vix.com";
#prepend domain-name-servers 127.0.0.1;
#require subnet-mask, domain-name-servers;
timeout 300;
#retry 60;
#reboot 10;
#select-timeout 5;
#initial-interval 2;
#script "/sbin/dhclient-script";
#media "-link0 -link1 -link2", "link0 link1";
#reject 192.33.137.209;

#alias {
# interface "eth0";
# fixed-address 192.5.5.213;
# option subnet-mask 255.255.255.255;
#}

lease {
 interface "enp4s1";
 fixed-address 192.168.0.111;
 medium "link0 link1";
 option host-name "andare.swiftmedia.com";
 option subnet-mask 255.255.255.0;
 option broadcast-address 192.168.1.200;
 option routers 192.168.0.110;
 option domain-name-servers 127.0.0.1;
 renew 2 2000/1/12 00:00:01;
 rebind 2 2000/1/12 00:00:01;
 expire 2 2000/1/12 00:00:01;
}

E para finalizar crie o dhcpd.leases:

lease 192.168.0.111{

}
lease 192.168.0.112{
    
}

Assim que finalizar, no teminar crie digite "sudo docker build -f Dockerfile -t nome_da_imagem .". Dessa forma, a sua imagem do DHCP foi criado, e para executar use o comando "sudo docker run --name nome_do_container --network=bridge  id_imagem"
se acontecer da forma correta ele ficará assim:

![ Redes-5P
/print_dhcp.png
](print_dhcp.png)

