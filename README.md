
### Parte 1 - Transações de Consultas e Modificações de Dados

1. **Definição da Tarefa:** Executar statements de consulta e modificação de dados no banco de dados via transações.
2. **Implementação:** Aqui, você vai criar transações que envolvem operações de `SELECT`, `INSERT`, `UPDATE` e `DELETE` dentro de uma transação. Vou fornecer um exemplo básico para te ajudar a começar.

```sql
BEGIN;

-- Exemplo de consulta (SELECT)
SELECT * FROM produtos WHERE estoque > 0;

-- Exemplo de inserção (INSERT)
INSERT INTO produtos (nome, preco, estoque) VALUES ('Produto Teste', 10.00, 50);

-- Exemplo de atualização (UPDATE)
UPDATE produtos SET estoque = estoque - 1 WHERE id = 1;

-- Exemplo de exclusão (DELETE)
DELETE FROM produtos WHERE id = 2;

COMMIT;
```

### Parte 2 - Transação com Procedure

1. **Definição da Tarefa:** Criar uma transação dentro de uma procedure, incluindo verificação de erro para um `ROLLBACK` total ou parcial usando `SAVEPOINT`.
2. **Implementação:** Vou criar um exemplo de procedure que verifica uma condição e, se houver um erro, utiliza o `ROLLBACK` com `SAVEPOINT`.

```sql
DELIMITER //

CREATE PROCEDURE ProcessarTransacao()
BEGIN
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    BEGIN
        ROLLBACK;
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Erro ao processar a transação.';
    END;

    START TRANSACTION;

    -- Criação de um savepoint
    SAVEPOINT antes_do_update;

    -- Exemplo de atualização que pode falhar
    UPDATE produtos SET estoque = estoque - 10 WHERE id = 1;

    -- Verificação de condição para rollback parcial
    IF (SELECT estoque FROM produtos WHERE id = 1) < 0 THEN
        ROLLBACK TO antes_do_update;
    END IF;

    -- Confirma a transação se tudo estiver certo
    COMMIT;
END//

DELIMITER ;
```

### Parte 3 - Backup e Recovery

1. **Definição da Tarefa:** Executar o backup do banco de dados e-commerce e restaurá-lo usando `mysqldump`.
2. **Implementação:** Vou descrever como fazer o backup e recovery com `mysqldump`.

#### Backup com mysqldump

Para fazer o backup do banco de dados `e_commerce`, execute o comando abaixo no terminal:

```bash
mysqldump -u seu_usuario -p e_commerce > e_commerce_backup.sql
```

Esse comando irá criar um arquivo `e_commerce_backup.sql` contendo o backup do banco de dados `e_commerce`.

#### Recovery com mysqldump

Para restaurar o banco de dados a partir do backup criado, use o comando:

```bash
mysql -u seu_usuario -p e_commerce < e_commerce_backup.sql
```

Isso restaurará os dados do arquivo de backup para o banco de dados `e_commerce`.

### Recursos Adicionais

1. **Procedures e Eventos:** No backup, certifique-se de incluir procedures, eventos e outros recursos do banco de dados. Você pode adicionar a flag `--routines` ao comando `mysqldump` para isso.

```bash
mysqldump -u seu_usuario -p --routines --events e_commerce > e_commerce_full_backup.sql
```
