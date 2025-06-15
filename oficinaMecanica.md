## Esquema conceitual para uma oficina, e esquema lógico, scripts SQL para criação das tabelas e seus relacionamentos, persistência de dados (inserções) e diversas queries SQL complexas, conforme as especificações solicitadas.


![424177954-d113c96b-bb05-4230-bfaf-d104279fed1c](https://github.com/user-attachments/assets/67ec4c05-e7b3-4793-a1e3-e81e663419b3)



## Análise do Esquema Conceitual e Geração do Esquema Lógico

**O esquema conceitual apresenta as seguintes entidades e relacionamentos:
Entidades:**

**CLIENTE:** Representa os clientes da oficina.

**Atributos:** ID_Cliente (PK), Nome, CPF, Telefone, Endereco.

**VEICULO:** Representa os veículos que são atendidos na oficina.

**Atributos:** ID_Veiculo (PK), Placa, Modelo, Marca, Ano, ID_Cliente (FK).

**ORDEM_SERVICO:** Representa as ordens de serviço geradas.
    
  **Atributos:** ID_OS (PK), Data_Emissao, Horario_Recebimento, Valor_Total, Status, Data_Conclusao, ID_Veiculo (FK), Autorizado.

**EQUIPE:** Representa as equipes de mecânicos.

**Atributos:** ID_Equipe (PK).

**MECANICO:** Representa os mecânicos individuais.

**Atributos:** ID_Mecanico (PK), Nome, Endereco, Telefone, Email, Especialidade.

**SERVICO:** Representa os tipos de serviços oferecidos pela oficina.

**Atributos:** ID_Servico (PK), Descricao, Valor_Mao_De_Obra.

**PECA:** Representa as peças utilizadas nos serviços.

**Atributos:** ID_Peca (PK), Nome, Valor_Unitario.
Relacionamentos:

**CLIENTE possui VEICULO:** Um cliente pode possuir vários veículos. Cada veículo pertence a um cliente. (1:N)

**VEICULO gera ORDEM_SERVICO:** Um veículo pode gerar várias ordens de serviço. Cada ordem de serviço é gerada por um veículo. (1:N)

**ORDEM_SERVICO atribuida EQUIPE:** Uma ordem de serviço é atribuída a uma ou mais equipes. Uma equipe pode estar atribuída a várias ordens de serviço. (N:M)

**EQUIPE composta MECANICO:** Uma equipe é composta por um ou mais mecânicos. Um mecânico pertence a uma equipe. (1:N)

**ORDEM_SERVICO utiliza SERVICO:** Uma ordem de serviço pode utilizar um ou mais serviços. Um serviço pode ser utilizado em várias ordens de serviço. (N:M)

**ORDEM_SERVICO utiliza PECA:** Uma ordem de serviço pode utilizar uma ou mais peças. Uma peça pode ser utilizada em várias ordens de serviço. (N:M)


## Esquema Lógico Relacional

## Com base no esquema conceitual, definimos as tabelas e seus atributos, incluindo chaves primárias (PK) e chaves estrangeiras (FK), e as tabelas de associação para relacionamentos N:M.
Tabelas:

**CLIENTE**
   * ID_Cliente (INT, PK, AUTO_INCREMENT)
   * Nome (VARCHAR(100))
   * CPF (VARCHAR(11), UNIQUE)
   * Telefone (VARCHAR(20))
   * Endereco (VARCHAR(255))

     
  **VEICULO**
   * ID_Veiculo (INT, PK, AUTO_INCREMENT)
   * Placa (VARCHAR(10), UNIQUE)
   * Modelo (VARCHAR(50))
   * Marca (VARCHAR(50))
   * Ano (INT)
   * ID_Cliente (INT, FK - Referencia CLIENTE)
 *
   **ORDEM_SERVICO**
   * ID_OS (INT, PK, AUTO_INCREMENT)
   * Data_Emissao (DATE)
   * Horario_Recebimento (TIME)
   * Valor_Total (DECIMAL(10, 2))
   * Status (VARCHAR(50))
   * Data_Conclusao (DATE)
   * ID_Veiculo (INT, FK - Referencia VEICULO)
   * Autorizado (BOOLEAN)
 *
   **EQUIPE**
   * ID_Equipe (INT, PK, AUTO_INCREMENT)
 *
   **MECANICO**
   * ID_Mecanico (INT, PK, AUTO_INCREMENT)
   * Nome (VARCHAR(100))
   * Endereco (VARCHAR(255))
   * Telefone (VARCHAR(20))
   * Email (VARCHAR(100))
   * Especialidade (VARCHAR(100))
   * ID_Equipe (INT, FK - Referencia EQUIPE)
 *
  **SERVICO**
   * ID_Servico (INT, PK, AUTO_INCREMENT)
   * Descricao (VARCHAR(255))
   * Valor_Mao_De_Obra (DECIMAL(10, 2))
 *
 *
   **PECA**
   * ID_Peca (INT, PK, AUTO_INCREMENT)
   * Nome (VARCHAR(100))
   * Valor_Unitario (DECIMAL(10, 2))

**Tabelas de Associação para Relacionamentos N:M:**
  **OS_EQUIPE (Associação entre ORDEM_SERVICO e EQUIPE)**
   * ID_OS (INT, PK, FK - Referencia ORDEM_SERVICO)
   * ID_Equipe (INT, PK, FK - Referencia EQUIPE)
 *
   **OS_SERVICO (Associação entre ORDEM_SERVICO e SERVICO)**
   * ID_OS (INT, PK, FK - Referencia ORDEM_SERVICO)
   * ID_Servico (INT, PK, FK - Referencia SERVICO)
   * Quantidade (INT) -- Adicionado para registrar a quantidade de um serviço aplicado (ex: horas de serviço)
 *
   **OS_PECA (Associação entre ORDEM_SERVICO e PECA)**
   * ID_OS (INT, PK, FK - Referencia ORDEM_SERVICO)
   * ID_Peca (INT, PK, FK - Referencia PECA)
   * Quantidade (INT)


## Scripts SQL

## 1. Criação do Esquema do Banco de Dados (DDL)
 **Criação do banco de dados**
   
CREATE DATABASE IF NOT EXISTS oficina;
USE oficina;

**Tabela CLIENTE**
CREATE TABLE IF NOT EXISTS CLIENTE (
    ID_Cliente INT PRIMARY KEY AUTO_INCREMENT,
    Nome VARCHAR(100) NOT NULL,
    CPF VARCHAR(11) UNIQUE NOT NULL,
    Telefone VARCHAR(20),
    Endereco VARCHAR(255)
);

**Tabela VEICULO**
CREATE TABLE IF NOT EXISTS VEICULO (
    ID_Veiculo INT PRIMARY KEY AUTO_INCREMENT,
    Placa VARCHAR(10) UNIQUE NOT NULL,
    Modelo VARCHAR(50),
    Marca VARCHAR(50),
    Ano INT,
    ID_Cliente INT NOT NULL,
    CONSTRAINT fk_cliente_veiculo FOREIGN KEY (ID_Cliente) REFERENCES CLIENTE(ID_Cliente)
);

**Tabela ORDEM_SERVICO**
CREATE TABLE IF NOT EXISTS ORDEM_SERVICO (
    ID_OS INT PRIMARY KEY AUTO_INCREMENT,
    Data_Emissao DATE NOT NULL,
    Horario_Recebimento TIME NOT NULL,
    Valor_Total DECIMAL(10, 2),
    Status VARCHAR(50) NOT NULL, -- Ex: "Pendente", "Em Andamento", "Concluido", "Cancelado"
    Data_Conclusao DATE,
    ID_Veiculo INT NOT NULL,
    Autorizado BOOLEAN,
    CONSTRAINT fk_veiculo_os FOREIGN KEY (ID_Veiculo) REFERENCES VEICULO(ID_Veiculo)
);

**Tabela EQUIPE**
CREATE TABLE IF NOT EXISTS EQUIPE (
    ID_Equipe INT PRIMARY KEY AUTO_INCREMENT
);

**Tabela MECANICO**
CREATE TABLE IF NOT EXISTS MECANICO (
    ID_Mecanico INT PRIMARY KEY AUTO_INCREMENT,
    Nome VARCHAR(100) NOT NULL,
    Endereco VARCHAR(255),
    Telefone VARCHAR(20),
    Email VARCHAR(100),
    Especialidade VARCHAR(100),
    ID_Equipe INT NOT NULL,
    CONSTRAINT fk_equipe_mecanico FOREIGN KEY (ID_Equipe) REFERENCES EQUIPE(ID_Equipe)
);

**Tabela SERVICO**
CREATE TABLE IF NOT EXISTS SERVICO (
    ID_Servico INT PRIMARY KEY AUTO_INCREMENT,
    Descricao VARCHAR(255) NOT NULL,
    Valor_Mao_De_Obra DECIMAL(10, 2) NOT NULL
);

**Tabela PECA**
CREATE TABLE IF NOT EXISTS PECA (
    ID_Peca INT PRIMARY KEY AUTO_INCREMENT,
    Nome VARCHAR(100) NOT NULL,
    Valor_Unitario DECIMAL(10, 2) NOT NULL
);

**Tabela de Associação OS_EQUIPE (Ordem de Serviço atribuída a Equipe)**
CREATE TABLE IF NOT EXISTS OS_EQUIPE (
    ID_OS INT NOT NULL,
    ID_Equipe INT NOT NULL,
    PRIMARY KEY (ID_OS, ID_Equipe),
    CONSTRAINT fk_os_osequipe FOREIGN KEY (ID_OS) REFERENCES ORDEM_SERVICO(ID_OS),
    CONSTRAINT fk_equipe_osequipe FOREIGN KEY (ID_Equipe) REFERENCES EQUIPE(ID_Equipe)
);

**Tabela de Associação OS_SERVICO (Ordem de Serviço utiliza Serviço)**
CREATE TABLE IF NOT EXISTS OS_SERVICO (
    ID_OS INT NOT NULL,
    ID_Servico INT NOT NULL,
    Quantidade INT NOT NULL DEFAULT 1, -- Quantidade de um determinado serviço (ex: horas, número de vezes)
    PRIMARY KEY (ID_OS, ID_Servico),
    CONSTRAINT fk_os_osservico FOREIGN KEY (ID_OS) REFERENCES ORDEM_SERVICO(ID_OS),
    CONSTRAINT fk_servico_osservico FOREIGN KEY (ID_Servico) REFERENCES SERVICO(ID_Servico)
);

**Tabela de Associação OS_PECA (Ordem de Serviço utiliza Peça)**
CREATE TABLE IF NOT EXISTS OS_PECA (
    ID_OS INT NOT NULL,
    ID_Peca INT NOT NULL,
    Quantidade INT NOT NULL DEFAULT 1, -- Quantidade de peças utilizadas
    PRIMARY KEY (ID_OS, ID_Peca),
    CONSTRAINT fk_os_ospeca FOREIGN KEY (ID_OS) REFERENCES ORDEM_SERVICO(ID_OS),
    CONSTRAINT fk_peca_ospeca FOREIGN KEY (ID_Peca) REFERENCES PECA(ID_Peca)
);



## 2. Persistência de Dados (DML - Inserções)
USE oficina;

**Inserção de dados na tabela CLIENTE**
INSERT INTO CLIENTE (Nome, CPF, Telefone, Endereco) VALUES
('João Silva', '11122233344', '987654321', 'Rua A, 123, Centro'),
('Maria Souza', '22233344455', '998877665', 'Av. B, 456, Bairro X'),
('Pedro Oliveira', '33344455566', '976543210', 'Travessa C, 789, Vila Y'),
('Ana Paula', '44455566677', '912345678', 'Alameda D, 101, Cidade Z'),
('Carlos Pereira', '55566677788', '934567890', 'Praça E, 202, Loteamento W');

**Inserção de dados na tabela VEICULO**
INSERT INTO VEICULO (Placa, Modelo, Marca, Ano, ID_Cliente) VALUES
('ABC1234', 'Onix', 'Chevrolet', 2020, 1),
('DEF5678', 'HB20', 'Hyundai', 2019, 2),
('GHI9012', 'Civic', 'Honda', 2022, 3),
('JKL3456', 'Corolla', 'Toyota', 2021, 1),
('MNO7890', 'T-Cross', 'Volkswagen', 2023, 4);

**Inserção de dados na tabela EQUIPE**
INSERT INTO EQUIPE () VALUES
(), -- Equipe 1
(), -- Equipe 2
(); -- Equipe 3

**Inserção de dados na tabela MECANICO**
INSERT INTO MECANICO (Nome, Endereco, Telefone, Email, Especialidade, ID_Equipe) VALUES
('Fernando Lima', 'Rua do Mecânico, 1', '987123456', 'fernando@oficina.com', 'Motor', 1),
('Mariana Costa', 'Av. das Peças, 2', '998234567', 'mariana@oficina.com', 'Freios', 1),
('Rafael Santos', 'Travessa do Óleo, 3', '976345678', 'rafael@oficina.com', 'Suspensão', 2),
('Juliana Alves', 'Alameda da Ferramenta, 4', '912456789', 'juliana@oficina.com', 'Elétrica', 2),
('Marcelo Rocha', 'Praça da Engrenagem, 5', '934567891', 'marcelo@oficina.com', 'Funilaria', 3);

**Inserção de dados na tabela SERVICO**
INSERT INTO SERVICO (Descricao, Valor_Mao_De_Obra) VALUES
('Troca de Óleo', 50.00),
('Alinhamento e Balanceamento', 80.00),
('Revisão Geral', 250.00),
('Troca de Pastilhas de Freio', 120.00),
('Inspeção de Suspensão', 70.00);

**Inserção de dados na tabela PECA**
INSERT INTO PECA (Nome, Valor_Unitario) VALUES
('Óleo do Motor 5W30', 45.00),
('Filtro de Óleo', 20.00),
('Pastilha de Freio Dianteira', 80.00),
('Vela de Ignição', 15.00),
('Amortecedor Dianteiro', 150.00);

**Inserção de dados na tabela ORDEM_SERVICO**
INSERT INTO ORDEM_SERVICO (Data_Emissao, Horario_Recebimento, Valor_Total, Status, Data_Conclusao, ID_Veiculo, Autorizado) VALUES
('2025-06-10', '09:00:00', 300.00, 'Concluido', '2025-06-11', 1, TRUE), -- OS para Onix (João)
('2025-06-10', '10:30:00', 150.00, 'Em Andamento', NULL, 2, TRUE), -- OS para HB20 (Maria)
('2025-06-11', '14:00:00', 400.00, 'Pendente', NULL, 3, TRUE), -- OS para Civic (Pedro)
('2025-06-12', '08:45:00', 90.00, 'Concluido', '2025-06-12', 4, FALSE), -- OS para Corolla (João), não autorizado
('2025-06-13', '11:15:00', 200.00, 'Em Andamento', NULL, 5, TRUE); -- OS para T-Cross (Ana)

**Inserção de dados na tabela OS_EQUIPE**
INSERT INTO OS_EQUIPE (ID_OS, ID_Equipe) VALUES
(1, 1),
(2, 2),
(3, 1),
(4, 3),
(5, 2);

**Inserção de dados na tabela OS_SERVICO**
INSERT INTO OS_SERVICO (ID_OS, ID_Servico, Quantidade) VALUES
(1, 1, 1), -- OS 1: Troca de Óleo
(1, 2, 1), -- OS 1: Alinhamento e Balanceamento
(2, 4, 1), -- OS 2: Troca de Pastilhas de Freio
(3, 3, 1), -- OS 3: Revisão Geral
(4, 1, 1), -- OS 4: Troca de Óleo
(5, 5, 1); -- OS 5: Inspeção de Suspensão

**Inserção de dados na tabela OS_PECA**
INSERT INTO OS_PECA (ID_OS, ID_Peca, Quantidade) VALUES
(1, 1, 4), -- OS 1: 4 litros de Óleo do Motor
(1, 2, 1), -- OS 1: 1 Filtro de Óleo
(2, 3, 1), -- OS 2: 1 Pastilha de Freio Dianteira
(3, 4, 4), -- OS 3: 4 Velas de Ignição
(4, 1, 4), -- OS 4: 4 litros de Óleo do Motor
(5, 5, 2); -- OS 5: 2 Amortecedores Dianteiros


## Queries SQL Complexas

**Aqui estão diversas queries SQL que abordam os requisitos solicitados:**

## 1. Recuperações Simples com SELECT Statement

**Pergunta: Liste todos os clientes cadastrados.**
SELECT * FROM CLIENTE;

**Pergunta: Quais são todos os serviços que a oficina oferece e seus respectivos valores de mão de obra?**
SELECT Descricao, Valor_Mao_De_Obra FROM SERVICO;

## 2. Filtros com WHERE Statement
   
**Pergunta: Quais veículos pertencem ao cliente "João Silva"?**
SELECT V.Marca, V.Modelo, V.Placa
FROM VEICULO V
JOIN CLIENTE C ON V.ID_Cliente = C.ID_Cliente
WHERE C.Nome = 'João Silva';

**Pergunta: Quais ordens de serviço estão com o status "Em Andamento"?**
SELECT ID_OS, Data_Emissao, Horario_Recebimento, Status
FROM ORDEM_SERVICO
WHERE Status = 'Em Andamento';

## 3. Crie expressões para gerar atributos derivados

**Pergunta: Mostre o nome do cliente, o modelo do veículo e uma coluna indicando se o veículo é novo (ano 2023 ou posterior) ou antigo.**
SELECT 
    C.Nome AS Nome_Cliente,
    V.Modelo AS Modelo_Veiculo,
    V.Ano,
    CASE
        WHEN V.Ano >= 2023 THEN 'Veículo Novo'
        ELSE 'Veículo Antigo'
    END AS Tipo_Veiculo
FROM CLIENTE C
JOIN VEICULO V ON C.ID_Cliente = V.ID_Cliente;

**Pergunta: Para cada ordem de serviço, calcule o custo total das peças utilizadas (Valor Unitário * Quantidade).**
SELECT 
    OS.ID_OS,
    OS.Data_Emissao,
    SUM(P.Valor_Unitario * OSP.Quantidade) AS Custo_Total_Pecas
FROM ORDEM_SERVICO OS
JOIN OS_PECA OSP ON OS.ID_OS = OSP.ID_OS
JOIN PECA P ON OSP.ID_Peca = P.ID_Peca
GROUP BY OS.ID_OS, OS.Data_Emissao;

## 4. Defina ordenações dos dados com ORDER BY

**Pergunta: Liste todos os mecânicos em ordem alfabética de nome.**
SELECT Nome, Especialidade, Telefone
FROM MECANICO
ORDER BY Nome ASC;

**Pergunta: Quais são as ordens de serviço, ordenadas da mais recente para a mais antiga?**
SELECT ID_OS, Data_Emissao, Status, Valor_Total
FROM ORDEM_SERVICO
ORDER BY Data_Emissao DESC, Horario_Recebimento DESC;

## 5. GROUP BY e HAVING Statement
   
**Pergunta: Quantos veículos cada cliente possui, mas apenas para clientes que possuem mais de um veículo?**
SELECT 
    C.Nome AS Nome_Cliente,
    COUNT(V.ID_Veiculo) AS Quantidade_Veiculos
FROM CLIENTE C
JOIN VEICULO V ON C.ID_Cliente = V.ID_Cliente
GROUP BY C.Nome
HAVING COUNT(V.ID_Veiculo) > 1;

**Pergunta: Qual o valor total de serviços e peças utilizados em cada ordem de serviço, mostrando apenas as ordens de serviço com um valor total de produtos/serviços superior a R$ 200,00?**
SELECT 
    OS.ID_OS,
    SUM(COALESCE(S.Valor_Mao_De_Obra * OSS.Quantidade, 0) + COALESCE(P.Valor_Unitario * OSP.Quantidade, 0)) AS Valor_Total_Calculado
FROM ORDEM_SERVICO OS
LEFT JOIN OS_SERVICO OSS ON OS.ID_OS = OSS.ID_OS
LEFT JOIN SERVICO S ON OSS.ID_Servico = S.ID_Servico
LEFT JOIN OS_PECA OSP ON OS.ID_OS = OSP.ID_OS
LEFT JOIN PECA P ON OSP.ID_Peca = P.ID_Peca
GROUP BY OS.ID_OS
HAVING Valor_Total_Calculado > 200.00
ORDER BY Valor_Total_Calculado DESC;


## 6. Sub-queries
   
**Pergunta: Liste os nomes dos clientes que possuem veículos da marca "Toyota".**
SELECT Nome
FROM CLIENTE
WHERE ID_Cliente IN (
    SELECT ID_Cliente
    FROM VEICULO
    WHERE Marca = 'Toyota'
);

**Pergunta: Encontre os mecânicos que estão em equipes atribuídas a ordens de serviço "Em Andamento".**
SELECT M.Nome, M.Especialidade
FROM MECANICO M
WHERE M.ID_Equipe IN (
    SELECT OSE.ID_Equipe
    FROM OS_EQUIPE OSE
    JOIN ORDEM_SERVICO OS ON OSE.ID_OS = OS.ID_OS
    WHERE OS.Status = 'Em Andamento'
);


## 7. Junções entre tabelas para fornecer uma perspectiva mais complexa dos dados
   
**Pergunta: Para cada ordem de serviço, mostre o ID da OS, o nome do cliente, a placa do veículo e o status da OS.**
SELECT 
    OS.ID_OS,
    C.Nome AS Nome_Cliente,
    V.Placa AS Placa_Veiculo,
    OS.Status
FROM ORDEM_SERVICO OS
JOIN VEICULO V ON OS.ID_Veiculo = V.ID_Veiculo
JOIN CLIENTE C ON V.ID_Cliente = C.ID_Cliente;


**Pergunta: Liste todas as ordens de serviço, os serviços utilizados em cada uma delas e as peças utilizadas, incluindo as quantidades e os valores unitários.**
SELECT
    OS.ID_OS,
    OS.Data_Emissao,
    V.Marca AS Marca_Veiculo,
    V.Modelo AS Modelo_Veiculo,
    OS.Status,
    S.Descricao AS Descricao_Servico,
    OSS.Quantidade AS Qtd_Servico,
    S.Valor_Mao_De_Obra AS Valor_Mao_De_Obra_Servico,
    P.Nome AS Nome_Peca,
    OSP.Quantidade AS Qtd_Peca,
    P.Valor_Unitario AS Valor_Unitario_Peca
FROM ORDEM_SERVICO OS
JOIN VEICULO V ON OS.ID_Veiculo = V.ID_Veiculo
LEFT JOIN OS_SERVICO OSS ON OS.ID_OS = OSS.ID_OS
LEFT JOIN SERVICO S ON OSS.ID_Servico = S.ID_Servico
LEFT JOIN OS_PECA OSP ON OS.ID_OS = OSP.ID_OS
LEFT JOIN PECA P ON OSP.ID_Peca = P.ID_Peca
ORDER BY OS.ID_OS, Descricao_Servico, Nome_Peca;


**Pergunta: Qual o nome dos mecânicos e as equipes às quais pertencem, juntamente com as ordens de serviço que suas equipes estão atribuídas, mostrando o status da OS?**
SELECT 
    M.Nome AS Nome_Mecanico,
    M.Especialidade,
    E.ID_Equipe AS ID_da_Equipe,
    OS.ID_OS AS ID_Ordem_Servico,
    OS.Status AS Status_OS,
    OS.Data_Emissao AS Data_Emissao_OS
FROM MECANICO M
JOIN EQUIPE E ON M.ID_Equipe = E.ID_Equipe
JOIN OS_EQUIPE OSE ON E.ID_Equipe = OSE.ID_Equipe
JOIN ORDEM_SERVICO OS ON OSE.ID_OS = OS.ID_OS
ORDER BY M.Nome, OS.Data_Emissao DESC;
 



