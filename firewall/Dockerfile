# Usando a imagem base Ubuntu
FROM ubuntu:latest

# Atualizando o repositório e instalando o firewall iptables
RUN apt-get update && apt-get install -y iptables
COPY firewall.sh /root/firewall.sh
RUN  chmod +x /root/firewall.sh
# Copiando o script de configuração do firewall para dentro do container


# Definindo o script de configuração do firewall como executável


# Comando para executar o script de configuração do firewall
