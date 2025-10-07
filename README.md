# Ferrari-Motors
Ferrari Motors Brasil

A Ferrari Motors Brasil é uma concessionária oficial da marca, que realiza:

Venda de carros novos e seminovos,

Serviços de manutenção e personalização,

Gestão de estoque e peças originais,

Atendimento a clientes PF e PJ (colecionadores e empresas).

O banco de dados precisa registrar clientes, carros, funcionários, vendas, revisões, peças, fornecedores e pagamentos.

🧩 Modelo Lógico — Esquema Relacional

Abaixo estão as principais tabelas e relacionamentos (chaves primárias e estrangeiras):

1️⃣ Tabela: CLIENTE
Campo	Tipo	Descrição
idCliente (PK)	INT AUTO_INCREMENT	Identificador único
tipo_cliente	ENUM('PF','PJ')	Pessoa Física ou Jurídica
nome	VARCHAR(100)	Nome completo ou razão social
cpf_cnpj	VARCHAR(20)	Documento único
email	VARCHAR(100)	E-mail do cliente
telefone	VARCHAR(20)	Contato principal
endereco	VARCHAR(255)	Endereço completo
data_cadastro	DATE	Data de cadastro no sistema

🔸 Restrições:

cpf_cnpj único

Um cliente pode ter vários pedidos/vendas

2️⃣ Tabela: FUNCIONARIO
Campo	Tipo	Descrição
idFuncionario (PK)	INT AUTO_INCREMENT	Identificador único
nome	VARCHAR(100)	Nome completo
cargo	VARCHAR(50)	Função (vendedor, mecânico, gerente, etc.)
salario	DECIMAL(10,2)	Remuneração mensal
data_admissao	DATE	Data de entrada na empresa
email	VARCHAR(100)	E-mail corporativo

🔸 Um funcionário pode realizar vendas ou serviços de manutenção.

3️⃣ Tabela: CARRO
Campo	Tipo	Descrição
idCarro (PK)	INT AUTO_INCREMENT	Identificador único
modelo	VARCHAR(100)	Ex: Ferrari F8 Tributo
ano	INT	Ano de fabricação
cor	VARCHAR(50)	Cor do carro
preco	DECIMAL(12,2)	Valor do veículo
status	ENUM('Disponível','Vendido','Reservado','Em Manutenção')	Situação atual
chassi	VARCHAR(30)	Número de chassi
tipo	ENUM('Novo','Seminovo')	Tipo de carro
idFornecedor (FK)	INT	Fornecedor de origem (fábrica ou parceiro)
4️⃣ Tabela: FORNECEDOR
Campo	Tipo	Descrição
idFornecedor (PK)	INT AUTO_INCREMENT	Identificador
nome	VARCHAR(100)	Nome da fábrica/parceiro
cnpj	VARCHAR(20)	CNPJ único
telefone	VARCHAR(20)	Contato
endereco	VARCHAR(255)	Localização

🔸 Relacionamento:
Um fornecedor fornece vários carros e peças.

5️⃣ Tabela: VENDA
Campo	Tipo	Descrição
idVenda (PK)	INT AUTO_INCREMENT	Identificador
idCliente (FK)	INT	Cliente que comprou
idFuncionario (FK)	INT	Vendedor responsável
idCarro (FK)	INT	Carro vendido
data_venda	DATE	Data da venda
valor_total	DECIMAL(12,2)	Valor final
forma_pagamento	ENUM('Cartão','Transferência','Financiamento','PIX')	Método de pagamento
6️⃣ Tabela: PAGAMENTO
Campo	Tipo	Descrição
idPagamento (PK)	INT AUTO_INCREMENT	Identificador
idVenda (FK)	INT	Venda associada
tipo_pagamento	VARCHAR(50)	Cartão, financiamento, etc.
valor	DECIMAL(12,2)	Valor pago
data_pagamento	DATE	Data de quitação

🔸 Uma venda pode ter vários pagamentos (ex: entrada + parcelas).

7️⃣ Tabela: PECA
Campo	Tipo	Descrição
idPeca (PK)	INT AUTO_INCREMENT	Identificador
nome_peca	VARCHAR(100)	Nome da peça
codigo_fabrica	VARCHAR(50)	Código original Ferrari
preco_unitario	DECIMAL(10,2)	Valor de cada peça
idFornecedor (FK)	INT	Fornecedor da peça
estoque	INT	Quantidade disponível
8️⃣ Tabela: MANUTENCAO
Campo	Tipo	Descrição
idManutencao (PK)	INT AUTO_INCREMENT	Identificador
idCarro (FK)	INT	Carro em manutenção
idFuncionario (FK)	INT	Mecânico responsável
data_inicio	DATE	Início do serviço
data_fim	DATE	Término do serviço
descricao	TEXT	Descrição dos trabalhos
valor_servico	DECIMAL(10,2)	Custo total

🔸 Um carro pode ter várias manutenções ao longo do tempo.

9️⃣ Tabela: ITEM_MANUTENCAO
Campo	Tipo	Descrição
idItem (PK)	INT AUTO_INCREMENT	Identificador
idManutencao (FK)	INT	Referência da manutenção
idPeca (FK)	INT	Peça usada
quantidade	INT	Quantidade usada
valor_unitario	DECIMAL(10,2)	Valor de cada peça

🔸 Permite registrar quais peças foram usadas em cada manutenção.

🔗 Relacionamentos (Resumo)

Cliente (1) → (N) Venda

Funcionario (1) → (N) Venda

Venda (1) → (N) Pagamento

Fornecedor (1) → (N) Carro

Fornecedor (1) → (N) Peca

Carro (1) → (N) Manutencao

Manutencao (1) → (N) Item_Manutencao

Peca (1) → (N) Item_Manutencao

📘 Diagrama ER Simplificado (Texto)
CLIENTE (1,N)───< VENDA >───(1,1) FUNCIONARIO
      │                       │
      │                       ▼
      ▼                      PAGAMENTO
     CARRO ───< MANUTENCAO >── FUNCIONARIO
      │          │
      ▼          ▼
   FORNECEDOR   ITEM_MANUTENCAO >── PECA
-- ==========================================================
-- 🏎️ FERRARI MOTORS BRASIL — Esquema Lógico de Banco de Dados
-- ==========================================================
-- Autor: Seu Nome
-- Data: 2025-10-06
-- Banco: MySQL 8+
-- Descrição: Estrutura relacional completa para loja de carros Ferrari
-- ==========================================================

DROP DATABASE IF EXISTS ferrari_motors;
CREATE DATABASE ferrari_motors;
USE ferrari_motors;

-- ==========================================================
-- 1️⃣ TABELA: CLIENTE
-- ==========================================================
CREATE TABLE cliente (
    idCliente INT AUTO_INCREMENT PRIMARY KEY,
    tipo_cliente ENUM('PF','PJ') NOT NULL,
    nome VARCHAR(100) NOT NULL,
    cpf_cnpj VARCHAR(20) UNIQUE NOT NULL,
    email VARCHAR(100),
    telefone VARCHAR(20),
    endereco VARCHAR(255),
    data_cadastro DATE DEFAULT (CURRENT_DATE)
);

-- ==========================================================
-- 2️⃣ TABELA: FUNCIONARIO
-- ==========================================================
CREATE TABLE funcionario (
    idFuncionario INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    cargo VARCHAR(50) NOT NULL,
    salario DECIMAL(10,2),
    data_admissao DATE,
    email VARCHAR(100)
);

-- ==========================================================
-- 3️⃣ TABELA: FORNECEDOR
-- ==========================================================
CREATE TABLE fornecedor (
    idFornecedor INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    cnpj VARCHAR(20) UNIQUE NOT NULL,
    telefone VARCHAR(20),
    endereco VARCHAR(255)
);

-- ==========================================================
-- 4️⃣ TABELA: CARRO
-- ==========================================================
CREATE TABLE carro (
    idCarro INT AUTO_INCREMENT PRIMARY KEY,
    modelo VARCHAR(100) NOT NULL,
    ano INT,
    cor VARCHAR(50),
    preco DECIMAL(12,2) NOT NULL,
    status ENUM('Disponível','Vendido','Reservado','Em Manutenção') DEFAULT 'Disponível',
    chassi VARCHAR(30) UNIQUE NOT NULL,
    tipo ENUM('Novo','Seminovo') DEFAULT 'Novo',
    idFornecedor INT,
    FOREIGN KEY (idFornecedor) REFERENCES fornecedor(idFornecedor)
        ON UPDATE CASCADE ON DELETE SET NULL
);

-- ==========================================================
-- 5️⃣ TABELA: VENDA
-- ==========================================================
CREATE TABLE venda (
    idVenda INT AUTO_INCREMENT PRIMARY KEY,
    idCliente INT NOT NULL,
    idFuncionario INT NOT NULL,
    idCarro INT NOT NULL,
    data_venda DATE DEFAULT (CURRENT_DATE),
    valor_total DECIMAL(12,2) NOT NULL,
    forma_pagamento ENUM('Cartão','Transferência','Financiamento','PIX') NOT NULL,
    FOREIGN KEY (idCliente) REFERENCES cliente(idCliente)
        ON UPDATE CASCADE ON DELETE CASCADE,
    FOREIGN KEY (idFuncionario) REFERENCES funcionario(idFuncionario)
        ON UPDATE CASCADE ON DELETE SET NULL,
    FOREIGN KEY (idCarro) REFERENCES carro(idCarro)
        ON UPDATE CASCADE ON DELETE SET NULL
);

-- ==========================================================
-- 6️⃣ TABELA: PAGAMENTO
-- ==========================================================
CREATE TABLE pagamento (
    idPagamento INT AUTO_INCREMENT PRIMARY KEY,
    idVenda INT NOT NULL,
    tipo_pagamento VARCHAR(50),
    valor DECIMAL(12,2),
    data_pagamento DATE,
    FOREIGN KEY (idVenda) REFERENCES venda(idVenda)
        ON UPDATE CASCADE ON DELETE CASCADE
);

-- ==========================================================
-- 7️⃣ TABELA: PECA
-- ==========================================================
CREATE TABLE peca (
    idPeca INT AUTO_INCREMENT PRIMARY KEY,
    nome_peca VARCHAR(100) NOT NULL,
    codigo_fabrica VARCHAR(50) UNIQUE NOT NULL,
    preco_unitario DECIMAL(10,2) NOT NULL,
    idFornecedor INT,
    estoque INT DEFAULT 0,
    FOREIGN KEY (idFornecedor) REFERENCES fornecedor(idFornecedor)
        ON UPDATE CASCADE ON DELETE SET NULL
);

-- ==========================================================
-- 8️⃣ TABELA: MANUTENCAO
-- ==========================================================
CREATE TABLE manutencao (
    idManutencao INT AUTO_INCREMENT PRIMARY KEY,
    idCarro INT NOT NULL,
    idFuncionario INT NOT NULL,
    data_inicio DATE DEFAULT (CURRENT_DATE),
    data_fim DATE,
    descricao TEXT,
    valor_servico DECIMAL(10,2),
    FOREIGN KEY (idCarro) REFERENCES carro(idCarro)
        ON UPDATE CASCADE ON DELETE CASCADE,
    FOREIGN KEY (idFuncionario) REFERENCES funcionario(idFuncionario)
        ON UPDATE CASCADE ON DELETE SET NULL
);

-- ==========================================================
-- 9️⃣ TABELA: ITEM_MANUTENCAO
-- ==========================================================
CREATE TABLE item_manutencao (
    idItem INT AUTO_INCREMENT PRIMARY KEY,
    idManutencao INT NOT NULL,
    idPeca INT NOT NULL,
    quantidade INT DEFAULT 1,
    valor_unitario DECIMAL(10,2),
    FOREIGN KEY (idManutencao) REFERENCES manutencao(idManutencao)
        ON UPDATE CASCADE ON DELETE CASCADE,
    FOREIGN KEY (idPeca) REFERENCES peca(idPeca)
        ON UPDATE CASCADE ON DELETE SET NULL
);

-- ==========================================================
-- 🔸 INSERINDO DADOS DE EXEMPLO
-- ==========================================================

INSERT INTO fornecedor (nome, cnpj, telefone, endereco)
VALUES ('Ferrari Itália S.p.A', '12.345.678/0001-00', '+55 11 99999-9999', 'Maranello, Itália'),
       ('Ferrari Parts Brasil', '98.765.432/0001-99', '+55 21 98888-8888', 'São Paulo - SP');

INSERT INTO funcionario (nome, cargo, salario, data_admissao, email)
VALUES ('Carlos Mendes', 'Vendedor', 8500.00, '2020-05-10', 'carlos@ferrari.com'),
       ('João Lima', 'Mecânico', 7200.00, '2019-08-22', 'joao@ferrari.com');

INSERT INTO cliente (tipo_cliente, nome, cpf_cnpj, email, telefone, endereco)
VALUES ('PF', 'Roberto Silva', '123.456.789-00', 'roberto@gmail.com', '(11) 99999-9999', 'Rua Itália, SP'),
       ('PJ', 'Luxury Imports LTDA', '45.987.654/0001-22', 'contato@luxury.com', '(21) 98888-7777', 'Av. Europa, RJ');

INSERT INTO carro (modelo, ano, cor, preco, status, chassi, tipo, idFornecedor)
VALUES ('Ferrari F8 Tributo', 2023, 'Vermelho', 3500000.00, 'Disponível', 'F8TRB12345BR2023', 'Novo', 1),
       ('Ferrari Roma', 2022, 'Preto', 2900000.00, 'Disponível', 'FRM2022BR001', 'Novo', 1);

INSERT INTO peca (nome_peca, codigo_fabrica, preco_unitario, idFornecedor, estoque)
VALUES ('Filtro de óleo F8', 'FOF8-2023', 950.00, 2, 30),
       ('Pastilha de freio Roma', 'PFR-2022', 1800.00, 2, 15);

INSERT INTO venda (idCliente, idFuncionario, idCarro, data_venda, valor_total, forma_pagamento)
VALUES (1, 1, 1, '2025-09-30', 3500000.00, 'Transferência');

INSERT INTO pagamento (idVenda, tipo_pagamento, valor, data_pagamento)
VALUES (1, 'Transferência', 3500000.00, '2025-09-30');

INSERT INTO manutencao (idCarro, idFuncionario, data_inicio, descricao, valor_servico)
VALUES (2, 2, '2025-10-01', 'Troca de pastilhas de freio', 2500.00);

INSERT INTO item_manutencao (idManutencao, idPeca, quantidade, valor_unitario)
VALUES (1, 2, 1, 1800.00);


-- ==========================================================
-- 🔍 CONSULTAS DE TESTE
-- ==========================================================

-- 1. Listar todas as vendas com nome do cliente e carro vendido
SELECT v.idVenda, c.nome AS cliente, ca.modelo AS carro, v.valor_total
FROM venda v
JOIN cliente c ON c.idCliente = v.idCliente
JOIN carro ca ON ca.idCarro = v.idCarro;

-- 2. Ver peças com estoque abaixo de 20 unidades
SELECT nome_peca, estoque FROM peca WHERE estoque < 20;

-- 3. Carros atualmente em manutenção
SELECT ca.modelo, ca.chassi, m.descricao, m.data_inicio
FROM manutencao m
JOIN carro ca ON ca.idCarro = m.idCarro
WHERE ca.status = 'Em Manutenção' OR m.data_fim IS NULL;

-- 4. Total de vendas por cliente
SELECT c.nome, COUNT(v.idVenda) AS qtd_vendas, SUM(v.valor_total) AS total_gasto
FROM cliente c
JOIN venda v ON v.idCliente = c.idCliente
GROUP BY c.nome;

-- 5. Peças utilizadas em manutenções
SELECT m.idManutencao, p.nome_peca, i.quantidade, i.valor_unitario
FROM item_manutencao i
JOIN peca p ON p.idPeca = i.idPeca
JOIN manutencao m ON m.idManutencao = i.idManutencao;
