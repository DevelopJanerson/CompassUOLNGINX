![image](https://github.com/user-attachments/assets/bcf1aef6-654e-46fc-904d-5c8ea3ce72fa)
# Projeto de Monitoramento de uma instância EC2 na AWS - Compass.UOL

## Objetivo
Desenvolver e testar habilidades em Linux, AWS e automação de processos através da configuração de um ambiente de servidor web monitorado.

## Descrição do Projeto
O Projeto de Monitoramento de uma instância EC2 na AWS cria um servidor web utilizando Nginx para monitoramento automatizado. O sistema reinicia automaticamente o Nginx em caso de falhas e notifica os responsáveis através de um webhook do Discord. Além disso, o projeto inclui uma página HTML que fornece informações acerca deste projeto.

## Arquitetura
A arquitetura do projeto é estabelecida sobre uma VPC na AWS. Esta VPC é disponibilizada em duas Availability Zones, permitindo um nível de disponibilidade maior e garantindo que o sistema permaneça operacional mesmo em caso de falhas em uma das zonas.

Dentro da VPC, foram criadas duas sub-redes públicas para permitir o acesso externo ao servidor web e duas sub-redes privadas que podem ser utilizadas para futuras expansões ou para serviços que não precisam ser acessados diretamente da internet.

### VPC
Para a comunicação exterior à [VPC](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)  é utilizado um Internet Gateway conectado às sub-redes públicas, permitindo que as instâncias EC2 acessem a internet e recebam tráfego externo.

### EC2
A arquitetura foi implantada usando instâncias [EC2](https://docs.aws.amazon.com/pt_br/AWSEC2/latest/UserGuide/concepts.html) com uma [AMI](https://docs.aws.amazon.com/pt_br/AWSEC2/latest/UserGuide/AMIs.html) baseada em Linux - Ubuntu. As instâncias EC2 são criadas dentro da VPC e estão associadas a um [Security Group](https://docs.aws.amazon.com/pt_br/AWSEC2/latest/UserGuide/ec2-security-groups.html) que permite o  tráfego HTTP (porta 80) e SSH (porta 22), garantindo que o servidor web esteja acessível e que a administração remota seja possível.

## Pré-requisitos
- Conta na AWS
- Permissões para criar VPCs, EC2 e Security Groups
- Conhecimento básico em Linux e AWS
  
## Tecnologias Utilizadas
- **Nginx**: Servidor web para gerenciar as requisições e servir a página HTML.
- **Bash**: Scripts de automação para instalação e monitoramento do servidor.
- **HTML**: Página web, exibindo informações sobre o projeto.
- **Discord**: Plataforma utilizada para enviar notificações em tempo real sobre o estado do servidor.
- **Linux (Ubuntu)**: Sistema operacional utilizado para a implementação do servidor.
  
## Funcionalidades
- **Reinicialização Automática**: O Nginx é reiniciado automaticamente em caso de falha.
- **Monitoramento**: O estado do servidor é monitorado a cada minuto, com notificações enviadas via Discord em caso de falhas.
- **Logs de Erros**: Registros de erros são mantidos em `/var/log/nginx_monitor.log` para análise e depuração.
- **Página de Informações**: Uma página HTML que exibe informações sobre o projeto, incluindo o hostname do servidor.

## Instalação
Para implementar a arquitetura na plataforma da AWS, execute o `script_monitoramento_nginx` através do user-data.
O `script_monitoramento_nginx` está disponível neste projeto. Seguem informações sobre o script:

### O que o script faz
O script "script_monitoramento_nginx" realiza as seguintes etapas:

1. **Atualização e Instalação do Nginx**: O script começa atualizando os pacotes do sistema e instalando o servidor web Nginx. Ele também habilita o Nginx para iniciar automaticamente no boot do sistema.

2. **Criação da Página HTML**: Cria uma página HTML simples que fornece informações sobre o projeto, incluindo uma descrição e as tecnologias utilizadas. Esta página é armazenada em /var/www/html/index.html.

3. **Script de Monitoramento**: O script cria um script de monitoramento "restart_nginx.sh" que verifica se o servidor web está online. Se o site estiver offline, o script reinicia o Nginx e envia uma notificação para um webhook do Discord, informando que o site está offline. Caso contrário, ele envia uma notificação informando que o site está online.

4. **Configuração do Systemd**: O script configura um serviço do systemd "restart_nginx.service" que executa o script de monitoramento. Além disso, um timer do systemd "restart_nginx.timer" é criado para executar o monitoramento a cada minuto.

5. **Finalização**: Por fim, o script recarrega o daemon do systemd, habilita e inicia o timer, completando a instalação e configuração do Nginx.

Após a execução do script, o servidor web estará em funcionamento e monitorado automaticamente, com notificações enviadas para o Discord em caso de falhas.

## Testes

Nesta seção, apresentamos algumas capturas de tela que demonstram o funcionamento do projeto.

### Página no Navegador

![Imagem Navegador](https://github.com/user-attachments/assets/e497bea0-237d-4158-b0e4-d2ce6a009caf)


### Logs do Terminal
Nesta próxima imagem, primeiramente mostro os logs de monitoramento que certificam que o site está online. Após isso, paro o servidor propositalmente com o comando "sudo systemctl stop nginx". Perceba que, na segunda vez em que mostro os logs de monitoramento, o site fica offline e reinicia automaticamente, sem que seja necessário executar qualquer outro comando no terminal.

![Imagem Terminal](https://github.com/user-attachments/assets/5f58de0b-c4a5-43a6-ac5e-6f7462ab21a9)


### Notificação do Discord

![Imagem Discord](https://github.com/user-attachments/assets/719308da-b8c6-48d7-91eb-c22ef3f05546)

