# desafio-bruteforce

1. Visão Geral do Projeto

Este projeto tem como objetivo simular cenários de testes de força bruta (Brute Force) e pulverização de senhas (Password Spraying) em ambiente controlado e isolado, além de propor medidas defensivas e técnicas de mitigação.

- Sistema Operacional: Kali Linux
- Alvo Vulnerável: Metasploitable 2 (contendo serviços de rede e aplicação DVWA)
- Rede: Rede NAT / Host-Only no VirtualBox
- Ferramentas utilizadas: Medusa, Hydra, Enum4linux, Smbclient, cliente FTP nativo.

2. Preparação do Ambiente e Wordlists

Foram elaboradas listas simples contendo usuários e senhas fracas comuns:

- Lista de Usuários (users.txt):
  user
  msfadim
  admin
  root

- Lista de senhas (pass.txt)
  123456
  password
  qwerty
  msfadmin

3. Cenários de Ataque e Validação

Brute Force com FTP.
O objetivo era descobrir credenciais válidas no serviço vsftpd (porta 21).

Comando utilizado
**medusa -h IP_DO_METASPLOITABLE -U users.txt -P pass.txt -M ftp -t 6**

A validão do acesso foi feita por meio do comando **ftp -n IP_DO_METASPLOITABLE**, conforme imagem anexada.

4. Password Spraying em SMB com Enumeração de Usuários

O objetivo era mapear usuários reais do sistema e testar uma única senha fraca contra várias contas, desviando de mecanismos convencionais de bloqueio de conta.

Segue as etapas do que foi realizado:

   1. Enumeração de usuários
     - comando: **enum4linux -U IP_DO_METASPLOITABLE**
        
   2. Ataque com medusa no módulo SAMBA (SMB)
      - comando: **medusa -h IP_DO_METASPLOITABLE -U smb_users.txt -p "msfadmin" -M smbnt**

A validação do acesso foi feita pelo comando **smbclient -L //IP_DO_METASPLOITABLE/ -U msfadmin%msfadmin**, conforme imagem anexada.

4. Cenário de Automação em Formulário Web (DVWA)
O objetivo era automatizar o teste de credenciais na página de login web (/dvwa/login.php).

    1. Análise de Falso Positivo:
       - Durante os testes de automação foi percebido a geração de diversos falsos positivos ao utilizar a ferramenta **medusa**, utilizei o **Dio Agent** para obter suporte mais direcionado.
Também foi observado que a utilização de módulos HTTP genéricos com base em respostas de código de status (200 OK), o resultado analisado em todas as senhas deve-se por conta de não havido análise contextual do corpo HTML da resposta.

    2. Execução Eficaz com Hydra (Requisição POST com Validação de Erro):
       Após diversas tentativas a ferramenta que trouxe o resultado esperado foi a **hydra**

       Comando utilizado:
       **hydra -l admin -P pass.txt IP_DO_METASPLOITABLE http-post-form "/dvwa/login.php:Username=^USER^&Password=^PASS^&Login=Login:F=Login failed"**

5. Recomendação de mitigação

   - Requisição de senhas fortes: exigir senhas com número mínimo de caracteres e com grau elevado de complexidade.
   - Bloqueio de contas: determinar o número de vezes necessário para que a conta seja bloqueado após diversas tentativas incorretas.
   - Múltiplo fator de autenticação: para que, mesmo se a senha seja descoberta através do *brute force* ou *spraying*, o acesso seja negado.
   - Uso de CAPTCHA
   - 
