Sistema de Estacionamento

Aplicação desktop em Java (Swing) para gerenciar um estacionamento: cadastra a entrada dos veículos, registra a saída com cálculo e pagamento do valor, mantém o registro de veículos e controla o fluxo de caixa (receitas, despesas e saldo). Os dados ficam em um banco MySQL, e o projeto inclui o script de criação desse banco.

Funcionalidades
Login com dois perfis: administrador (acesso completo) e funcionário (sem acesso à aba Caixa).
Cadastrar (entrada): registra placa, marca e modelo, com data e hora de entrada. Ao cadastrar, gera um ticket de entrada em PDF.
Saída: pesquisa o veículo pela placa, calcula o valor pelo tempo de permanência (R$ 15,00 por hora, proporcional aos minutos) e confirma o pagamento em Dinheiro, Pix, Cartão de Crédito ou Cartão de Débito.
Registro: consulta um veículo pela placa (modelo, marca e status) e permite alterar marca e modelo.
Caixa: lista as entradas (pagamentos recebidos), permite lançar saídas (contas e despesas com descrição, forma de pagamento e valor) e mostra o saldo, o total que entrou e o total que saiu.
Tela de abertura (splash) com barra de carregamento antes do login.
Tecnologias
Java 8 (projeto NetBeans com Ant)
Java Swing (interface gráfica)
MySQL (conector JDBC mysql-connector)
JPA / EclipseLink (entidades Entradas e Saidas)
iText 5.3.3 (geração do ticket em PDF)
Estrutura do projeto
estacionamento_completo_/
├── Criação_do_banco de dados.docx   # script SQL do banco
└── estacionamento_completo_/        # projeto NetBeans
    ├── src/
    │   ├── main/Main.java           # ponto de entrada (abre o splash)
    │   ├── Controller/conexão_BD.java   # carrega o driver do MySQL
    │   ├── Model/
    │   │   ├── Funcoes_DAO.java     # login, cadastro, saída, caixa e ticket PDF
    │   │   └── Splash_DAO.java      # barra de carregamento
    │   ├── View/                    # telas: Splash, Login, Tela inicial, Entradas, Saidas
    │   ├── Image/                   # ícones e logo
    │   └── META-INF/persistence.xml # configuração JPA
    ├── lib/                         # bibliotecas (MySQL, EclipseLink, iText)
    └── nbproject/                   # configuração do NetBeans
Banco de dados

O banco se chama estacionamento e precisa ser criado antes de abrir o programa, executando o script abaixo (o mesmo do arquivo Criação_do_banco de dados.docx) no MySQL.

sql
create database estacionamento;
use estacionamento;

create table veiculos(
  placa varchar(8) not null,
  marca varchar(30) not null,
  modelo varchar(30) not null,
  horario_entrada varchar(10) not null,
  horario_saida varchar(10) not null,
  data varchar(10) not null,
  status varchar(50) not null,
  primary key(placa)
);

create table entradas(
  codigo INTEGER AUTO_INCREMENT NOT NULL,
  cliente varchar(8) not null,
  descricao varchar(40) not null,
  data varchar(10) not null,
  forma_pagamento varchar(30) not null,
  valor float not null,
  primary key(codigo),
  FOREIGN KEY (cliente) REFERENCES veiculos(placa)
);

create table saidas(
  codigo INTEGER AUTO_INCREMENT NOT NULL,
  descricao varchar(150) not null,
  forma_pagamento varchar(100),
  valor DECIMAL(10, 2) NOT NULL,
  data varchar(10) not null,
  horario varchar(5) not null,
  primary key(codigo)
);

create table parados(
  placa varchar(8) not null,
  marca varchar(30) not null,
  modelo varchar(30) not null
);

CREATE TABLE saldo (
  saldo FLOAT NOT NULL DEFAULT 0.0,
  saindo FLOAT NOT NULL DEFAULT 0.0,
  entrando FLOAT NOT NULL DEFAULT 0.0
);

INSERT INTO saldo (saldo, saindo, entrando) VALUES (0.0, 0.0, 0.0);
Tabela	Função
veiculos	Histórico de todos os veículos, com horários e status (estacionado ou Fora do estacionamento)
parados	Veículos que estão no estacionamento agora
entradas	Pagamentos recebidos na saída dos veículos
saidas	Despesas lançadas no caixa
saldo	Saldo atual e totais que entraram e saíram
Como executar
Pré-requisitos
JDK 8 ou superior
MySQL em execução (o projeto usa a porta 3307, usuário root e senha vazia)
NetBeans (recomendado, pois é um projeto NetBeans)
Passo a passo
Clone o repositório:
bash
git clone https://github.com/matheussilvadealmeidasilva-cpu/Estacionamento.git
Execute o script SQL acima no MySQL para criar o banco e as tabelas.
Abra a pasta estacionamento_completo_ (a interna) como projeto no NetBeans.
Se o seu MySQL usar outra porta, usuário ou senha, altere url, username e password em src/Model/Funcoes_DAO.java e os dados de conexão em src/META-INF/persistence.xml.
Ajuste a pasta onde o ticket em PDF é salvo (veja Observações).
Execute o projeto (classe principal main.Main).
Acesso ao sistema
Perfil	Usuário	Senha	Acesso
Administrador	admin	admin	Todas as abas, incluindo Caixa
Funcionário	funcionario	func	Cadastrar, Saída e Registro
Observações
O ticket em PDF é salvo em C:\Users\Alunos\Documents\Estacionamento\. Crie essa pasta ou altere o caminho na função gerartickt() de Funcoes_DAO.java.
A logo do ticket é lida de um caminho fixo do computador de desenvolvimento. Se o arquivo não existir, o ticket é gerado sem a logo.
O valor da hora (R$ 15,00) está fixo no código.
Os usuários e senhas estão escritos diretamente no código, por isso o projeto serve para estudo.
Melhorias futuras
Valor da hora configurável pela tela
Usuários e senhas guardados no banco, com senha criptografada
Cálculo de permanência com data completa (hoje só considera horas e minutos)
Relatórios do caixa por período
Autor

Matheus Silva de Almeida – github.com/matheussilvadealmeidasilva-cpu

Projeto desenvolvido para fins de estudo.
