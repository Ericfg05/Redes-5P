FROM ubuntu:latest

# Atualizando o repositório e instalando o servidor DNS BIND9
RUN apt-get update && apt-get install -y bind9

# Copiando o arquivo de configuração do DNS para dentro do container
COPY named.conf /etc/bind/named.conf

# Expondo a porta usada pelo servidor DNS
EXPOSE 53/tcp
EXPOSE 53/udp

# Comando para iniciar o servidor DNS
CMD ["/usr/sbin/named", "-g", "-c", "/etc/bind/named.conf", "-u", "bind"]

