# Usando a imagem base Ubuntu
FROM ubuntu:latest

# Atualizando o repositório e instalando o firewall iptables
RUN apt-get update && apt-get install -y iptables

# Copiando o script de configuração do firewall para dentro do container
COPY firewall.sh /root/firewall.sh

# Definindo o script de configuração do firewall como executável
RUN  chmod 777 /root/firewall.sh

# Comando para executar o script de configuração do firewall
CMD ["./root/firewall.sh"]
