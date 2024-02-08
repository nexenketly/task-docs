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
* Utilizar [React Hook Form](https://react-hook-form.com/) para construção dos formulários
* Utilizar [`useMutation`](https://tanstack.com/query/latest/docs/framework/react/reference/useMutation) para envio dos dados ao backend
* Utilizar [`useInfiniteQuery`](https://tanstack.com/query/latest/docs/framework/react/reference/useInfiniteQuery) para listagem dos dados do backend
* Utilizar tipagem de maneira adequada em todos os pontos do processo
* Aplicar otimizações de estilo necessárias para melhoria da usabilidade
* Renderizar componentes somente quando variável de apresentação estiver com valor lógico VERDADEIRO
* Corrigir outros problemas identificados durante o desenvolvimento
* Ajustar mensagens de sucesso e mensagens de erro
* Bloquear preenchimento de campos e cliques em botões enquanto:
  * Ocorrer um carregamento de dados
  * Ocorrer um salvamento de dados
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
* Renomear coluna **Cabo 1** para **Cód. ERP Cabo Negativo**
* Renomear coluna **Cabo 2** para **Cód. ERP Cabo Positivo**
* Remover coluna **Qtd. Cabos**
* Remover coluna **Módulos**

#### Cadastro / Edição

* Renomear os labels dos campos de formulário de acordo com as mesmas renomeações realizadas na tabela de listagem
* Remover o campo de formulário **ID Original**
* Remover o campo de formulário **Quantidade de cabos**
* Remover o campo de formulário **Módulos**
* Substituir os campos de texto **Cabo 1 - Código Erp** e **Cabo 2 - Código Erp** por campos `select` que listam os produtos **não obsoletos** do ERP que iniciam com o código `1020`
  * Armazenar as informações de código ERP de acordo com o retorno do ERP
* Para armazenamento das informações do formulário, criar a nova tabela `structures`
  * Essa tabela deve possuir as seguintes colunas:
    * `id`
    * `description`
    * `type`: Define se a estrutura é do tipo solo ou telhado, deve aceitar somente os valores `ROOF` ou `GROUND`
    * `negative_cable_erp_code`
    * `positive_cable_erp_code`
    * `active`
    * `created_at`
    * `updated_at`
    * `deleted_at`
  * Também será necessário criar a tabela `structures_branches` para associação entre estruturas e filiais 
* 🚩 Remover tabelas `roof_types`, `roof_types_branches`, `module_roof_types`
* Substituir modal por nova página, em razão do aumento de informações associadas às estruturas
  * Para cada estrutura, deverão ser criadas duas abas na página de adição/edição: **Configurações** e **Produtos**
  * Todos os campos de formulário relacionados a ambas as abas devem ser obrigatórios
    * O sistema não deve permitir que uma estrutura seja salva sem ao menos um produto cadastrado
  * A estrutura da nova página está ilustrada na imagem abaixo, podendo ser alterada para melhoria da usabilidade e apresentação do conteúdo ao usuário:
    ![image](https://github.com/nexenketly/task-docs/assets/109694742/dba9cd6d-381a-4601-95b4-082913e725e6)
    ![image](https://github.com/nexenketly/task-docs/assets/109694742/11eba02e-e2a5-47bd-b923-184d087275a1)
    * Detalhes de implementação do formulário deverão ser discutidos durante o desenvolvimento
    * O campo `select` da aba de produtos deve listar os produtos **não obsoletos** do ERP que iniciam com o código `1050`

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
* Criar o campo **Qtd Cabo/Conector** para inclusão da quantidade de cabo exigida para cada conector
  * Adicionar a coluna `connector_cable_quantity` à tabela `inverters`
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

### Informações iniciais

### Detalhamento de estruturas
