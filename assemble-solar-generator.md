# Monte Seu Gerador 2.0

Detalhamento das alterações necessárias para implantação da nova versão do menu "MONTE SEU GERADOR" da Loja Online.

> Todas as alterações aqui descritas podem se aplicar tanto ao frontend quanto ao backend.
> 
> Atentar, principalmente, para replicar **validações** em ambos os ambientes.

> Alterações no banco de dados, realizadas através de migrations, devem ser refletidas nas entidades, DTOs e outras estruturas similares no projeto.
>
> Atentar, principalmente, para consultas onde o campo está muitas vezes presente como uma string ou parte de uma string.

## Módulo Gestão

### Loja Online - Geral

* Reordenar e renomear as abas de acordo com a seguinte ordem:
  * Estruturas
    * Referente à _Tipos de Telhado_
  * Acessórios
  * Módulos
  * Marcas de Inversores
  * Inversores
* Alinhar `padding` das tabelas com demais elementos da página, conforme apontado na imagem abaixo:
  ![image](https://github.com/nexenketly/task-docs/assets/109694742/c3abcdd2-1993-4fa0-b8ae-2b3bc8babfe9)
* Realizar as seguintes alterações, conforme ilustrado na imagem abaixo:
  * Remover título da página, que está duplicado em relação à barra de navegação
  * Alinhar botão de inclusão e barra de pesquisa
    * Caso tenha filtros, apresentar abaixo da barra de pesquisa
    ![image](https://github.com/nexenketly/task-docs/assets/109694742/133bf5d4-105b-4a9a-bc04-a52daa153c3b)
* Padronizar o tamanho dos botões de adição
* Padronizar texto do botão de adição para **Adicionar**
* Ordenar apresentação da coluna **Filiais** por ordem alfabética
* Nos modais de cadastro/edição, quando aplicável:
  * Mover botões para o rodapé do modal
  * Padronizar tamanho dos botões **Salvar** e **Cancelar**
* Atualizar listagens ao realizar adição/edição/deleção
* Realizar carregamento dinâmico de todos os campos de `select` que apresentem todos os produtos do ERP (sem filtro)
  * Buscar dados somente quando o usuário digitar pelo menos três caracteres
  * Apresentar loading (Carregando...) enquanto os resultados estiverem sendo carregados
  * Possivelmente, criar _hook_ para listagem de produtos do ERP, como essa listagem será reutilizada em vários locais, somente com paramentrizações diferentes
* Ajustar a transição de salvamento de todos os botões **Salvar** para **`Salvando... <Spinner />`**, com espaço adequado entre o texto e o elemento
* Avaliar a possibilidade de substituir todos os modais por novas páginas, para manter o padrão com a edição e cadastro de estruturas
  * Se isso se concretizar, as alterações relacionadas exclusivamente a modais podem ser desconsideradas 

### Módulos

#### Listagem

* Remover coluna **ID**
* Colocar coluna **Código ERP** na primeira posição
* Renomear coluna **Nome** para **Descrição**

#### Cadastro / Edição

* Tornar todos os campos do formulário obrigatórios
* Substituir os campos de texto **Nome** e **Código ERP** por um campo `select` que lista os produtos **não obsoletos** do ERP que iniciam com o código `1001`
  * Armazenar as informações de descrição e código ERP de acordo com o retorno do ERP
* Adicionar campo `select` múltiplo para inclusão das estruturas com as quais aquele módulo pode ser instalado
  * Para armazenamento da informação, criar a tabela `module_structures` com os campos `module_id` e `structure_id`
* 🚩 Renomear colunas da tabela `modules`:
  * `name` ➡️ `erp_description`
  * `original_id` ➡️ `erp_code`
  * `base_power` ➡️ `power`

### Tipos de Telhado

#### Listagem

* Remover coluna **ID**
* Renomear coluna **Nome** para **Descrição**
* Renomear coluna **Ativo** para **Ativa**
* Remover coluna **Cód. ERP Cabo Negativo**
* Remover coluna **Cód. ERP Cabo Positivo**
* Remover coluna **Qtd. Cabos**
* Remover coluna **Módulos**
* Adicionar coluna **Tipo**

#### Cadastro / Edição

* Substituir modal por nova página, em razão do aumento de informações associadas às estruturas
  * Para cada estrutura, deverão ser criadas duas abas na página de adição/edição: **Configurações** e **Produtos**
  * A estrutura da nova página está ilustrada na imagem abaixo, podendo ser alterada para melhoria da usabilidade e apresentação do conteúdo ao usuário:
    ![image](https://github.com/nexenketly/task-docs/assets/109694742/6b7c3bcb-7bdc-4d4a-a20c-86582a6e2c6d)
    ![image](https://github.com/nexenketly/task-docs/assets/109694742/11eba02e-e2a5-47bd-b923-184d087275a1)
    * Todos os campos de formulário relacionados a ambas as abas devem ser obrigatórios
      * O sistema não deve permitir que uma estrutura seja salva sem ao menos um produto cadastrado
      * O campo **Número mínimo de módulos por pedido** deve ser preenchido inicialmente com zero
    * O campo `select` da aba de produtos deve listar os produtos **não obsoletos** do ERP que iniciam com o código `1020`
    * O campo de fórmula da aba de produtos deve ser implementado de maneira similar ao campo de fórmula utilizado hoje para os acessórios
      * Entretanto, variáveis e caracteres permitidos ainda serão definidos e, provavelmente, serão diferentes
    * O label do campo de fórmula deve apresentar um ícone de informação que exibe um tooltip (ou estrutura similar) explicando ao usuário o formato aceito pelo campo
      * Possivelmente, durante o desenvolvimento, essa abordagem pode ser substituída por outra forma de apresentação da informação, a depender do tamanho do texto necessário
    * Alterações em ambas as abas somente serão salvas quando o usuário clicar em "Salvar"
    * Deve ser exibida mensagem de confirmação ao usuário caso ele tente deixar a página, para evitar perda de informações
      * Para diminuir a complexidade, essa mensagem pode ser exibida sempre, porém com texto genérico "..informações não salvas serão perdidas.."
    * Precisamos deixar claro ao usuário que informações preenchidas na primeira linha da listagem de produtos não serão salvas a menos que ele clique no botão "+"
    * Outros detalhes de implementação do formulário deverão ser discutidos durante o desenvolvimento
* Para armazenamento das informações do formulário, criar a nova tabela `structures`
  * Essa tabela deve possuir as seguintes colunas:
    * `id`
    * `description`
    * `type`: Define se a estrutura é do tipo solo ou telhado, deve aceitar somente os valores `ROOF` ou `GROUND`
    * `min_module_qty`
    * `active`
    * `created_at`
    * `updated_at`
    * `deleted_at`
  * Também será necessário criar a tabela `structures_branches` para associação entre estruturas e filiais
  * 🚩 Também será necessário criar a tabela `structure_products` para associação entre estruturas e filiais, contendo as colunas:
    * `id`
    * `structure_id`
    * `erp_code`
    * `qty_formula`
    * `created_at`
    * `updated_at`
    * `deleted_at`
* 🚩 Remover tabelas `roof_types`, `roof_types_branches`, `module_roof_types`

#### Deleção

* Implementar deleção lógica

### Kits

* Remover aba pois não será mais necessária
  * 🚩 Remover tabela `kits`

### Marca Inversores

#### Listagem

* Remover coluna **ID**
* Renomear coluna **Ativo** para **Ativa**

#### Cadastro / Edição

* Tornar todos os campos do formulário obrigatórios
* Corrigir associação de filiais que não está funcionando
* 🚩 Renomear tabela `inverters_brands_branches`, que associa marcas e filiais, para `inverter_brands_branches`

#### Deleção

* Implementar deleção lógica
  * Adicionar coluna `deleted_at` na tabela `inverter_brands`

### Inversores

#### Listagem

* Remover coluna **ID**
* Renomear colunas:
  * **Código ERP inversor** para **Cód. ERP Inversor**
  * **Nome inversor** para **Descrição**
  * **Marca inversor** para **Marca**
  * **Potência inversor** para **Potência**
  * **Código ERP conector** para **Cód. ERP Conector**
  * **Quantidade de conectores** para **Qtd Conector**
* Remover coluna **Código Stick Wi-Fi**
* Adicionar coluna **Qtd Cabo/Conector**

#### Cadastro / Edição

* Tornar todos os campos do formulário obrigatórios
* Renomear os labels dos campos de formulário de acordo com as mesmas renomeações realizadas na tabela de listagem
* Substituir os campos de texto **Código ERP do inversor** e **Nome do inversor** por um campo `select` que lista os produtos **não obsoletos** do ERP que iniciam com o código `1010`
  * Armazenar as informações de descrição e código ERP de acordo com o retorno do ERP
* Substituir o campo de texto **Código ERP conector** por um campo `select` que lista os produtos **não obsoletos** do ERP que iniciam com o código `1030`
  * Armazenar a informação de código ERP de acordo com o retorno do ERP
* Remover o campo de formulário **Código Stick Wi-Fi (Não obrigatório)**
  * Esse campo passará a ser um acessório
* Criar campos para inclusão dos códigos e da quantidade de cabo exigida para cada conector em todos os tipos de estrutura:
  * **Cabo Positivo (padrão)**, **Cabo Negativo (padrão)** e **Qtd Cabo (padrão)** - _SEM ESTRUTURA_
  * **Cabo Positivo (telhado)**, **Cabo Negativo (telhado)** e **Qtd Cabo (telhado)**
  * **Cabo Positivo (solo)**, **Cabo Negativo (solo)** e **Qtd Cabo (solo)**
  * Os campos `select` dos cabos devem listar os produtos **não obsoletos** do ERP que iniciam com o código `1050`
  * Criar a tabela `inverter_cables` para armazenamento dessas informações com as seguintes colunas:
    * `id`
    * `inverter_id`
    * `structure_type`: Deve ser armazenado pelo sistema de acordo com o campo preenchido, sendo
      * **padrão** = `NULL`
      * **telhado** = `ROOF`
      * **solo** = `GROUND`
    * `positive_erp_code`
    * `negative_erp_code`
    * `quantity`
    * `updated_at`
* 🚩 Renomear colunas da tabela `inverters`:
  * `inverter_erp_code` ➡️ `erp_code`
  * `inverter_name` ➡️ `description`
  * `inverter_brand_id` ➡️ `brand_id`
  * `inverter_power` ➡️ `power`
  * `connectors_quantity` ➡️ `connector_quantity`
* 🚩 Remover coluna `stick_wifi_code` da tabela `inverters`

#### Deleção

* Implementar deleção lógica
  * Adicionar coluna `deleted_at` na tabela `inverters`

### Acessórios

* Nenhuma alteração específica identificada

## Módulo Portal

Considerando as alterações no módulo gestão, serão necessárias alterações na construção do menu "Monte Seu Gerador" da Loja Online. Além disso, também serão necessárias alterações na forma de cálculo de um gerador considerando a nova maneira de cadastro das estruturas.

### Monte Seu Gerador (MSG)

Em relação à estrutura atual do menu MSG, vamos realizar algumas alterações de layout para melhoria da experiência do usuário, conforme ilustrado abaixo:

![image](https://github.com/nexenketly/task-docs/assets/109694742/a681d98d-6061-4b5d-9512-4a1d9db71aad)

* Alterar o título do passo/etapa para "Configurações"
* Alterar o título do texto apresentado no cabeçalho do componente para "Configurações do Gerador"
  * O texto do cabeçalho também será alterado para melhoria da comunicação com o usuário, porém ainda está em construção
* Apresentar formulário de inserção dos parâmetros em duas colunas, sendo a primeira para informar o inversor e a segunda para informar módulo e potência
* Ajustar os rótulos dos campos de formulário
* Substituir a estilização atual dos campos de seleção, mantendo o padrão do componente `Select` do React
  * Padrão atual:
    
    ![image](https://github.com/nexenketly/task-docs/assets/109694742/5529a0dc-6d57-4c93-ad2e-4dd5f66a6ed3)
  * Padrão React:
    
    ![image](https://github.com/nexenketly/task-docs/assets/109694742/8fb4c4f0-1e64-491a-a4bb-4aabed30b0e2)
    
Além disso, também será adicionada opção para seleção da configuração de estrutura para o gerador, incluindo:

* Sem estrutura
* Estrutura em solo
* Estrutura em telhado

O comportamento de cada opção será detalhado nas seções abaixo.

> O botão de "Buscar" do menu MSG deve passar a estar sempre habilitado e, quando clicado, deve validar os campos de formulário da página para garantir o seu preenchimento.
>
> A princípio todos os campos são obrigatórios.
>
> Lógicas específicas de cada configuração de estrutura devem ser validadas e possíveis erros devem estar explícitos para o usuário.

#### Sem estrutura

Quando o usuário selecionar a opção "Sem estrutura", será exibida uma mensagem informando a ausência de estruturas de fixação no gerador montado:

![image](https://github.com/nexenketly/task-docs/assets/109694742/dc16b745-e922-41a2-8137-849c816de230)
_(Em construção)_

##### Lógica do cálculo

Para cálculo dos produtos do gerador para esta opção, deve ser realizada a seguinte lógica:

1. Determinar a potência real do gerador:
  ```math
  potencia\_do\_gerador = \left \lceil \frac{potencia\_informada}{potencia\_do\_modulo} \right \rceil * potencia\_do\_modulo
  ```
  * A potência do módulo está armazenada na tabela `modules` e deve ser encontrada com base no módulo selecionado pelo usuário
2. Determinar a quantidade de módulos:
  ```math
  quantidade\_de\_modulos = \frac{potencia\_do\_gerador}{potencia\_do\_modulo}
  ```
3. Determinar a quantidade de inversores:
  ```math
  overload\_maximo = 1.35
  ```
  ```math
  quantidade\_minima\_de\_inversores = \left \lfloor \frac{potencia\_do\_gerador}{potencia\_do\_inversor} \right \rfloor
  ```
  ```math
  potencia\_calculada = potencia\_do\_inversor * quantidade\_minima\_de\_inversores
  ```
  ```math
  quantidade\_de\_inversores = \left\{\begin{matrix}
  quantidade\_minima\_de\_inversores, & \frac{potencia\_do\_gerador}{potencia\_calculada} \leq overload\_maximo \\
  quantidade\_minima\_de\_inversores + 1, & \mbox{caso contr\'{a}rio}
  \end{matrix}\right.
  ```
  * A potência do inversor está armazenada na tabela `inverters` e deve ser encontrada com base no inversor selecionado pelo usuário
4. Determinar a quantidade de conectores:
  ```math
  quantidade\_de\_conectores = quantidade\_de\_inversores * quantidade\_de\_conectores\_do\_inversor
  ```
  * A quantidade de conectores do inversor está armazenada na tabela `inverters` e deve ser encontrada com base no inversor selecionado pelo usuário
5. Determinar a quantidade de cabo:
  ```math
  quantidade\_de\_cabo = quantidade\_de\_conectores * quantidade\_de\_cabo\_do\_inversor
  ```
  * A quantidade de cabo do inversor estará armazenada na tabela `inverter_cables` e deve ser encontrada com base no inversor selecionado pelo usuário e na opção de estrutura
6. Adicionar a uma váriavel de armazenamento os seguintes produtos:

| Produto | Quantidade |
|---------|------------|
| Módulo selecionado pelo integrador | _quantidade_de_modulos_ |
| Inversor selecionado pelo integrador | _quantidade_de_inversores_ |
| Conector associado ao inversor selecionado pelo integrador | _quantidade_de_conectores_ |
| Cabo positivo associado ao inversor selecionado pelo integrador (tabela `inverter_cables`) | _quantidade_de_cabo_ |
| Cabo negativo associado ao inversor selecionado pelo integrador (tabela `inverter_cables`) | _quantidade_de_cabo_ |

7. Determinar os acessórios do gerador e sua quantidade, de acordo com a tabela `accessories`
   * Os produtos acessórios e sua quantidade devem ser determinados de acordo com a lógica já utilizada hoje
8. Adicionar produtos acessórios à váriavel de armazenamento
9. A partir da variável de armazenamento, juntar possíveis produtos duplicados, somando sua quantidade
10. Buscar pelos códigos dos produtos no ERP para validação e cálculo da disponibilidade
    * Caso algum item não seja retornado, um erro deve ser exibido ao usuário, da mesma forma que já ocorre hoje
11. Excluir o carrinho ativo do usuário/integrador e criar um novo carrinho
12. Adicionar ao carrinho, na tabela `invoice_items`, todos os produtos da variável de armazenamento, determinando sua disponibilidade
    * O detalhamento dos dados a serem inseridos será determinado durante o desenvolvimento, após análise do código 

#### Estrutura em solo

Quando o usuário selecionar a opção "Estrutura em solo", será exibida uma mensagem informando a impossibilidade do dimensionamento personalizado dos módulos no gerador montado:

![image](https://github.com/nexenketly/task-docs/assets/109694742/c717fd14-ae24-43a9-a3af-526184ba75f4)
_(Em construção)_

Além da mensagem, será exibido um menu de seleção que lista as estruturas cadastradas no módulo gestão, cujo tipo é `GROUND`, permitindo ao usuário selecionar para qual tipo de estrutura em solo, dentre as padronizações existentes, ele deseja cálcular o seu gerador.

Durante a inclusão dos parâmetros, o sistema deve cálcular também no front a quantidade de módulos, de acordo com o passo 2 da lógica apresentada acima, e validar se essa quantidade é igual ou superior ao mínimo de módulos exigido por pedido, que estará armazenado dentro da tabela `structures` na coluna `min_module_qty`. Caso essa quantidade seja inferior, deverá ser apresentada uma mensagem ao usuário informando:

> A quantidade de módulos do gerador é inferior ao mínimo exigido para a estrutura selecionada. A quantidade mínima é de XX módulos e com a potência informada serão inclusos apenas XX módulos. Para prosseguir com a montagem do kit, você precisa informar uma potência mínima de XX kW.

O posicionamento dessa mensagem será definido durante o desenvolvimento.

##### Lógica do cálculo

Para cálculo dos produtos do gerador para esta opção, devem ser seguidos os mesmos doze passos utilizados na opção "Sem estrutura", adicionando um passo extra entre os passos 6 e 7:

* **Passo Extra:** Determinar os produtos da estrutura e sua quantidade para o gerador, de acordo com a tabela `structures`
   * Como as estruturas em solo não permitem personalização, a fórmula para cálculo das quantidades estará ajustada para se dar em relação ao total de módulos
   * Assim, todos os produtos da tabela 🚩 `structure_products`, associados à estrutura em solo selecionada pelo usuário, deverão ser inclusos na variável de armazenamento, e sua quantidade deve ser calculada com base na fórmula cadastrada pelo usuário
     * A interpretação e aplicação da fórmula para obtenção da quantidade deve se dar de maneira similar à lógica utilizada para os acessórios
       * Possíveis melhorias identificadas durante o desenvolvimento deverão ser propagadas para ambas as lógicas (ex.: conseguimos não utilizar `eval`?)

#### Estrutura em telhado

##### Lógica do cálculo

### Cálculo de Estruturas

Com a implementação da nova versão do menu MSG, sugere-se a remoção do menu "Editar estrutura de fixação", que existe hoje dentro da Loja Online e da edição de um orçamento, pelos seguintes motivos:

* Essa opção existe hoje dentro do carrinho em razão da ausência da possibilidade de personalização da distribuição dos módulos nas configurações iniciais do MSG
  * Com a nova versão do menu MSG, a personalização da distribuição dos módulos estará disponível, dispensando a existência de uma opção de configuração adicional
    * Para recálculo do gerador, será necessário apenas que o usuário retorne ao passo inicial
  * Para melhoria da experiência do usuário, as parametrizações inseridas serão salvas no Armazenamento Local (lado cliente), persistindo mesmo em caso de atualização da página
    * As parametrizações armazenadas serão removidas somente quando o carrinho for transformado em um orçamento ou pedido
    * As parametrizações armazenadas serão atualizadas sempre que o usuário alterar algum dos parâmetros do menu MSG
* Quanto a opção que existe hoje dentro da edição de um orçamento, é possível argumentar que:
  * Para recálculo somente dos produtos que compõe a estrutura a serem adicionados ao pedido, é preciso saber:
    * Qual é o inversor selecionado (para inclusão dos cabos, por exemplo)
    * Qual é o módulo e qual é a potência (para cálculo da quantidade de módulos permitida)
  * Isso gera alguns problemas:
    * Essas informações não são armazenadas hoje e, mesmo que fossem, poderiam estar desatualizadas em relação à configuração atual do pedido, que pode ser alterado manualmente
      * Além disso, também poderiam gerar inconsistências, como apresentar um módulo que não está mais ativo, apresentar uma combinação de módulo e estrutura que não é mais permitida, entre outros
    * Caso seja realizada tentativa de obtenção dessas informações a partir dos itens do pedido, podem ser encontradas inconsistências, como a existência de dois ou mais tipos de módulos ou inversores (novamente a partir de alteração manual)
    * Caso solicitemos a inclusão dessas informações novamente pelo usuário, visando substituição completa dos itens do pedido (para evitar inconsistências), caracterizamos a mesma estrutura do menu MSG
      * Nesse caso, o usuário teria a possibilidade de gerar um novo orçamento para o cliente, através do menu MSG da loja online
      * Entretanto, caso seja realmente necessária a permanência do cálculo, essa seria a opção mais indicada, adicionando um novo passo na edição do orçamento, de maneira a incluir a mesma estrutura utilizada no novo menu MSG
        * Nesse cenário, a parametrização não seria salva em Armazenamento Local, devido as especificidades da tela e do menu de edição do orçamento
        * Além disso, alterações somente seriam salvas após o usuário clicar no botão "Salvar"
        * Por fim, todas as edições manuais seriam perdidas (como já ocorre hoje)
       
## Recomendações Gerais

* Utilizar [React Hook Form](https://react-hook-form.com/) para construção dos formulários
* Utilizar [`useMutation`](https://tanstack.com/query/latest/docs/framework/react/reference/useMutation) para envio dos dados ao backend
* Utilizar [`useInfiniteQuery`](https://tanstack.com/query/latest/docs/framework/react/reference/useInfiniteQuery) para listagem dos dados do backend
* Utilizar tipagem de maneira adequada em todos os pontos do processo
* Aplicar otimizações de estilo necessárias para melhoria da usabilidade
* Corrigir outros problemas identificados durante o desenvolvimento
* Ajustar mensagens de sucesso e mensagens de erro
* Bloquear preenchimento de campos e cliques em botões enquanto:
  * Ocorrer um carregamento de dados
  * Ocorrer um salvamento de dados
