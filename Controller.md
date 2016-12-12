## Controller


O **Controller/base** do heart é a camada que fará a intermediação entre as views e as models, e por tanto, se comunica com as 4 principais operações de banco trabalhadas também na `Model/base`, a saber: **criação (*create*)**, **leitura (*read*)**, **atualização (*update*)** e **exclusão (*delete*)**.


Antes de explicar como cada uma das operações é trabalhada na nossa **Controller/base**, vamos falar sobre as estruturas que guiam o comportamento dessa classe: as propriedades `actions`, `offlineActions`, `fillneeded`, `relationals`, `model`, `location`, a função `action` e o construtor da Controller/base.


### BASICS
#### Actions
As ações permitidas em uma `controller` são definidas por dois `arrays` de `string`. O `array` `actions` e o `array` `offlineActions`. Vamos aos detalhes:

##### actions
É o array que define as ações privadas do sistema para aquele controlador. Em outras palavras, são as ações que precisam de autenticação para serem executadas.

##### offlineActions
É o array que define as ações públicas do sistema para aquele controlador. Em outras palabras, são as açoes que não precisam de autenticação para serem executadas.

#### Fillneeded
Esse é o array que trata os campos obrigatórios da tabela referente àquele controlador. Bom, tudo o que você vai colocar com `required` no `HTML` e/ou como `NOT NULL` no `MySQL`, deveria estar presente no array `fillneeded`, que vai funcionar como uma camada a mais de segurança.
O `fillneeded` trabalha em conjunto com a função `is_valid()`.

#### Relationals
Esse é o array que determina quais tabelas estão relacionadas com a tabela referente àquela  `controller`.

#### Model
Essa propriedade trabalha em conjunto com a função `get_model()`, que retornará o nome da model com base no nome de uma dada controller.

#### Location
Determina a página para a qual um usuário deve ser redirecionado após serem executadas ações de `Create, Update e Delete`.

#### Action
A função `action()` é quem lida com as chamadas da nossa controller. Ela interpreta quais são as ações permitidas e as executa quando solicitado, descartando as chamadas para ações inválidas, ou seja, que não estão em `actions` ou em `offlineActions`.

#### O construtor
Nosso é o responsável por atribuir valor à propriedade `model` e por executar a função `action`. Por tanto, o caminho mais curto para executar uma ação é fazendo uma nova instância da referida controller.

### CRUD
Bom, finalmente, vamos falar sobre o tratamento das 4 operações básicas..

#### Create e Update
São aplicados através da função `store()`. A função `store` irá identificar se deve fazer um `Model->insert()` ou um `Model->update()`, baseando-se na presença do campo `id` no `$_REQUEST`.
Por padrão, após a execução, a `store()` redireciona o usuário para a `location` definida na respectiva controller.

#### Read
Temos duas funções para executar esta ação que trabalham de formas diferentes. São elas:

##### loadAll($order = null, $where = null)
Executa a função Model::all($order, $where) e retorna seu valor de retorno;

##### one($id = null)
A função `one` da nossa controller identifica, caso não receba o `$id` por parâmetro, que o `$id` solicitado está no `$_GET`.
Executa a função Model::one($id) e retorna seu valor de retorno, caso haja um `$id` válido. Do contrário, retorna `null`.

#### Delete
A função `delete()` espera que na `$_REQUEST` exista um campo de `name` `delete` e cujo valor seja o `id` da linha que se deseja deletar.
Ela executa a `Model->remove()` e, por padrão, redireciona o usuário para a `location` definida na respectiva controller.
