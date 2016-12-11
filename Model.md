##MODEL


O **Model/base** do heart é a camada mais próxima do banco de dados, e se encarrega das 4 principais operações de banco, a saber: **criação (*create*)**, **leitura (*read*)**, **atualização (*update*)** e **exclusão (*delete*)**. 


Antes de explicar como cada uma das operações é trabalhada na nossa **Model/base**, vamos falar de 3 estruturas que guiam o comportamento dessa classe: a propriedade `fillable`, o `construtor` dinâmico e a função `entity`.


###BASICS
####Fillable
Em tradução literal, **'Fillable'** quer dizer **'Preenchível'**, e é exatamente isso que estamos tratando nessa propriedade. O nosso `fillable` é um `array` de `strings` que deve armazenar o nome dos campos (colunas) que queremos utilizar em nossas solicitações de inserção e atualização de linhas nas tabelas do `BD`. Qualquer tentativa de inserir algo em uma coluna que não esteja registrada no `fillable` será falha. Isso implica que nos seus `forms`, os `inputs` e afins devem possuir os mesmos `names` registrados no `fillable` da respectiva model.

####O construtor
Nosso construtor dinâmico é o responsável por fazer o `fillable` funcionar. Ele vai tratar a sua `$_REQUEST` e eliminar tudo o que não faz parte do `fillable`, bem como irá criptografar o conteúdo do campo `password`, caso você envie um na sua requisição.

####Entity($isView = true)
O nome da função já é bastante sugestivo. A `entity` retorna o nome da entidade em questão, na formatação exigida pelo `heart` como padrão para nomes de tabelas, que nada mais é do que o nome da respectiva model, em `lowercase`, seguido de s.


Essa função é permitida apenas para o contexto das `Models`, e pode receber um argumento que indica se uma dada função pretende fazer sua consulta em uma `view` ou em uma `table` `mysql`.


As `views` devem seguir o mesmo padrão das tabelas, seguido de `_vw`. O `heart`, por padrão, utiliza `views` em sua configuração.


Então, sendo assim, se o nome da sua `model` é User, o nome da sua tabela referente a essa `model` deve ser users, e o nome da `view` referente a essa tabela deve ser users_vw, porque é assim que o `heart` vai interpretar. 


- **Model**: *User*
- **Table**: *users*
- **View**: *users_vw*
- **Controller**: *User_controller*


###CRUD
Bom, finalmente, vamos falar sobre o tratamento das 4 operações básicas. Mas antes, fique atento a isso:
- `table` é o retorno da função `entity(false)`;
- `view`é o retorno da função `entity()`;
- `columns` e `values` são as strings do nosso array `fillable` devidamente formatadas para esta `query`. Cada função vai executar um algoritmo capaz de formatar o conteúdo do `$fillable` de forma a atender às exigências de sintaxe.
- `$this->id` é o `id` que deve estar como um campo da sua `$_REQUEST`.

####Create
É aplicado através da função `insert()` que, basicamente, vai executar a query 
```SQL
INSERT INTO `table` (`columns`) VALUES (`values`)
```
A `insert` retorna o valor da coluna `id` da linha inserida.

####Read
Temos duas funções para executar esta ação que trabalham de formas diferentes. São elas:

#####all($order = 'id ASC', $where = null)
Esta função vai trazer o resultado da query
```SQL
SELECT * FROM `view` WHERE $where ORDER BY $order.
```
Por padrão, a função não faz nenhuma restrição com a cláusula WHERE e traz os resultados ordenados pela coluna id, de forma ascendente.
O retorno desta função é um array de objetos órfãos, onde cada objeto é uma linha da respectiva view.

#####one($id)
Esta função é o resultado da consulta
```SQL
SELECT * FROM `view` WHERE id = $id LIMIT = 1
```
O retorno desta função é um objeto órfão.

####Update
É aplicado através da função `update()` que apenas executa a query
```SQL
UPDATE `table` SET (`colums` = `values`) WHERE id = $this->id
```
O retorno dessa função é booleano.


####Delete
É aplicado através da função `remove($id)` que executa a query
```SQL
DELETE FROM `table` WHERE id = $this->id
```
O retorno desta função é booleano.
