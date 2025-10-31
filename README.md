# aws-cloudformation-lab

🏆 Desafio DIO: Implementação de Infraestrutura com AWS CloudFormation

Este projeto documenta a implementação de uma Máquina Virtual (VM) na AWS EC2 utilizando o conceito de Infraestrutura como Código (IaC) através do serviço AWS CloudFormation. O objetivo final é ter um servidor Ubuntu acessível via SSH e HTTP, demonstrando o uso de templates, pares de chaves e Grupos de Segurança.

 ⚙️ 1.Template CloudFormation (ec2-instance.yaml)

   O template YAML define a configuração da instância EC2. A utilização de IaC garante a repetibilidade e a consistência do ambiente.

   ![template yaml](image.png)

Ensights do Template (IaC):
Tipagem de Recursos: A propriedade Type: AWS::EC2::Instance 
instrui o CloudFormation a provisionar um recurso EC2.

Usuário Padrão: O ImageId da AMI do Ubuntu define que o usuário padrão 
para acesso SSH é ubuntu.

Gestão de Acesso: O KeyName vincula a chave pública do par à instância no 
momento da criação. A chave privada (my-first-key.pem) é a única forma de acesso.   


🛠️ 2. Passos de Configuração no Console AWS

A implantação do template e a configuração do firewall foram realizadas através do console para garantir a correta interação entre os serviços.

A. Implantação do Template (CloudFormation)

    1- Acesse o serviço AWS CloudFormation no console.

    2- Clique em "Criar Stack" e faça o upload do arquivo ec2-instance.yaml.

    3- Defina o nome da Stack e conclua o assistente.

    4- Após o status da Stack ser CREATE_COMPLETE, a instância EC2 foi provisionada.

B. Recuperação do Endereço de Conexão (EC2)

    1- Acesse o serviço EC2 no console.

    2- Clique em "Instâncias" e localize a instância criada pelo CloudFormation.

    3- Na aba Detalhes, anote o Endereço IPv4 público (Exemplo: 23.20.115.88).  

C. Configuração do Firewall (Grupo de Segurança)
   Um dos principais desafios foi garantir que o acesso HTTP (web) estivesse liberado. Por padrão, o grupo de segurança anexado pelo CloudFormation só permitia SSH (Porta 22).  

   1- No console EC2, navegue até "Grupos de Segurança".

   2- Edite as Regras de Entrada do grupo anexado à instância.

   3- Adicionada a regra HTTP:

        Tipo: HTTP

        Porta: 80

        ![Regras de Entrada](images/RegrasEntrada.png)

💻 3. Acesso e Validação (Terminal)

A. Preparação da Chave
    Insight de Segurança: A chave privada (my-first-key.pem) deve ter as permissões restritas para que o SSH a aceite. O acesso bem-sucedido via SSH só foi possível após a configuração:
    chmod 400 my-first-key.pem

B. Conexão SSH ao Servidor 

   A conexão foi realizada utilizando o usuário ubuntu e o IP público, ignorando os placeholders (< e >) que causaram erros iniciais.
   ssh -i my-first-key.pem ubuntu@23.20.115.88

   ![Conexão SSH Bem-sucedida](images/AwsConfigure.png)

C. Instalação do Servidor Web (Nginx) 
   Para validar o acesso HTTP, o servidor web Nginx foi instalado:
   sudo apt update
   sudo apt install nginx -y

D. Validação do Acesso Web
   Após a instalação do Nginx e a liberação da Porta 80 no Grupo de Segurança, o acesso foi validado via navegador.
     
    ![Página de Boas-vindas do Nginx](images/welcomeNginex.png)

💡 4. Principais Insights e Aprendizados

     Infraestrutura como Código (IaC): O CloudFormation demonstrou ser uma ferramenta poderosa para provisionamento automatizado, garantindo que as propriedades da instância sejam definidas de forma declarativa (código).

     Grupos de Segurança: A importância do firewall da AWS ficou evidente. Mesmo com o Nginx instalado e rodando, o acesso HTTP era impossível até que o Grupo de Segurança liberasse explicitamente a Porta 80.

     Conectividade SSH: O processo reforçou a necessidade de permissões corretas (chmod 400), a sintaxe exata do comando (ssh -i), e o conhecimento do usuário padrão (ubuntu para esta AMI).


📄 Seção Adicional para o README.md: Configuração da AWS CLI


💻 5. Configuração da AWS CLI para Acesso Programático
      A Interface de Linha de Comando da AWS (AWS CLI) foi configurada localmente para permitir o gerenciamento de recursos diretamente pelo terminal, demonstrando acesso programático à nuvem.

      A. Pré-requisito: Credenciais de Acesso
         Para configurar a CLI, é necessário ter as chaves de acesso (Access Key ID e Secret Access Key) de um usuário IAM (Gerenciamento de Identidade e Acesso) com as permissões necessárias.
    1- No Console AWS, navegue até o serviço IAM.

    2- Crie um novo usuário ou selecione o usuário existente.

    3- Vá para "Credenciais de segurança" e crie uma nova Chave de acesso (Access Key).

    4- Importante: Copie a Access Key ID e a Secret Access Key e armazene-as em um local seguro 
       (elas só são   mostradas uma vez).

B. Instalação (No seu Sistema Ubuntu Local)
   Embora a CLI estivesse disponível, o processo padrão de instalação em distribuições Linux é o seguinte:

    # 1. Atualizar a lista de pacotes
         sudo apt update

    # 2. Instalar dependências (Python e unzip)
         sudo apt install -y python3 python3-pip unzip

    # 3. Baixar e descompactar o arquivo de instalação oficial
         curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
         unzip awscliv2.zip

    # 4. Executar a instalação
         sudo ./aws/install

    # 5. Verificar a versão instalada
         aws --version

C. Configuração das Credenciais (Comando aws configure)
   O comando aws configure armazena as credenciais
   no seu arquivo local ~/.aws/credentials, 
   permitindo que os comandos da AWS CLI funcionem.

   aws configure

    Solicitação	                              Valor Inserido

   AWS Access Key ID [None]:	           AKIAxxxxxxxxxxxxxx (Sua chave de acesso pública)

   AWS Secret Access Key [None]:	         wJalrXUtnFEMIx/K7MDENGBxRfiCYEXAMPLEKEY (Sua chave secreta)

   Default region name [None]:	            us-east-1 (A região onde sua instância EC2 foi criada)

   Default output format [None]:         json (Padrão para facilitar o parsing de scripts)

C. Validação da CLI exemplo

  # Valida a versão instalada
    aws --version

  # Valida o acesso à conta (Exemplo: listar buckets S3)
    aws s3 ls

 7. 🔗 Referências e Documentação Adicional

       Para um aprofundamento nos conceitos abordados neste desafio:

       Documentação Oficial AWS CloudFormation:
       https://docs.aws.amazon.com/pt_br/AWSCloudFormation/latest/UserGuide/Welcome.html


       https://www.google.com/search?q=https://docs.aws.amazon.com/pt_br/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-instance.html
    
       Conectividade e Segurança: 
       https://docs.aws.amazon.com/pt_br/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html

       https://docs.aws.amazon.com/pt_br/AWSEC2/latest/UserGuide/ec2-security-groups.html

       AWS CLI:
       https://docs.aws.amazon.com/pt_br/cli/latest/userguide/getting-started-quickstart.html

      Servidor Web:
      https://www.google.com/search?q=https://www.nginx.com/resources/documentation/


      Conceitos de IaC:
      https://aws.amazon.com/pt/what-is/iac/