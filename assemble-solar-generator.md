# Monte Seu Gerador 2.0

Detalhamento das alterações necessárias para implantação da nova versão do menu "MONTE SEU GERADOR" da Loja Online.

> Todas as alterações aqui descritas podem se aplicar tanto ao frontend quanto ao backend.
> 
> Atentar, principalmente, para replicar **validações** em ambos os ambientes.

## Módulo Gestão

### Loja Online - Geral

* Reordenar e renomear as abas de acordo com a seguinte ordem:
  * Estruturas
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

### Módulos

#### Listagem

* Remover coluna **ID**
* Colocar coluna **Código ERP** na primeira posição
* Renomear coluna **Nome** para **Descrição**

#### Cadastro / Edição

* Tornar todos os campos do formulário obrigatórios
* Substituir os campos de texto **Nome** e **Código ERP** por um campo `select` que lista os produtos **não obsoletos** do ERP que iniciam com o código `1001`
  * Armazenar as informações de descrição e código ERP de acordo com o retorno do ERP
* 🚩 Renomear colunas do banco:
  * `name` ➡️ `erp_description`
  * `original_id` ➡️ `erp_code`

### Tipos de Telhado

### Kits

* Remover aba pois não será mais necessária

### Marca Inversores

#### Listagem

* Remover coluna **ID**
* Renomear colunas:
  * **Código ERP inversor** para **Cód. ERP Inversor**
  * **Nome inversor** para **Descrição**
  * **Marca inversor** para **Marca**
  * **Potência inversor** para **Potência**
  * **Código ERP conector** para **Cód. ERP Conector**
  * **Quantidade de conectores** para **Qtd Conectores**
* Remover coluna **Código Stick Wi-Fi**

#### Cadastro / Edição

* Tornar todos os campos do formulário obrigatórios
* Corrigir associação de filiais que não está funcionando

#### Deleção

* Implementar deleção lógica
  * Adicionar coluna `deleted_at` na tabela `inverter_brands`

### Inversores

#### Listagem

* Remover coluna **ID**
* Renomear coluna **Ativo** para **Ativa**

#### Cadastro / Edição

* Tornar todos os campos do formulário obrigatórios
* Substituir os campos de texto **Código ERP do inversor** e **Nome do inversor** por um campo `select` que lista os produtos **não obsoletos** do ERP que iniciam com o código `1010`
  * Armazenar as informações de descrição e código ERP de acordo com o retorno do ERP

#### Deleção

* Implementar deleção lógica
  * Adicionar coluna `deleted_at` na tabela `inverters`

### Acessórios

## Módulo Portal

### Informações iniciais

### Detalhamento de estruturas
