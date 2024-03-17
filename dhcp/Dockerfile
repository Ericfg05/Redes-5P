FROM dockerscripts/linuxmint:xfce-minimal

# Atualizando o repositório e instalando o servidor DHCP
RUN apt-get update && apt-get install -y isc-dhcp-server

# Copiando o arquivo de configuração do DHCP para dentro do container
COPY  isc-dhcp-server /etc/default/isc-dhcp-serve
COPY /dhcp/dhcpd.conf /etc/dhcp/dhcpd.conf
COPY /dhcpd.leases /var/lib/dhcp
# Expondo a porta usada pelo servidor DHCP
#RUN apt-get install apt-utils
#RUN /etc/init.d/isc-dhcp-server restart
RUN apt install net-tools -y
# Comando para iniciar o servidor DHCP
CMD ["dhcpd", "-f", "-d", "--no-pid"]

