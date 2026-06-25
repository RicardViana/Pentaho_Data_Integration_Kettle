
# CSV file input

![alt text](image.png)

Com certeza. O step **CSV file input** é um dos mais utilizados no Pentaho e possui configurações cruciais para garantir que os dados sejam lidos corretamente e com boa performance.

Abaixo, detalho o que cada campo e opção dessa tela faz, divididos por categorias para facilitar o entendimento:

### Configurações Básicas de Origem

* **Step name:** O nome do step dentro da sua Transformation. É uma boa prática dar nomes descritivos (ex: `CSV file input - Clientes`).
* **Filename:** O caminho completo onde o arquivo CSV está salvo. O botão "Navegar..." ajuda a localizar o arquivo no seu computador ou servidor.
* **Delimiter:** O caractere que separa as colunas no seu arquivo. O padrão é a vírgula (`,`), mas no Brasil é muito comum usarmos o ponto e vírgula (`;`) ou até mesmo o *Tab* (usando o botão "Insert TAB").
* **Enclosure:** O caractere usado para "empacotar" um texto que possa conter o delimitador dentro dele. Geralmente são aspas duplas (`"`). *Exemplo: Se o delimitador é vírgula e um campo de endereço é `"Rua ABC, 123"`, o enclosure garante que a vírgula dentro das aspas não quebre a coluna.*

### Performance e Otimização

* **NIO buffer size:** O tamanho da memória (em bytes) alocada para ler o arquivo em blocos. O valor padrão de `50000` costuma atender bem, mas pode ser aumentado para arquivos gigantescos visando ganhar velocidade de leitura.
* **Lazy conversion?:** Uma das opções mais importantes para performance. Se marcada, o Pentaho não converte os dados do CSV (que nascem como texto) para seus tipos finais (Integer, Date, etc.) imediatamente na leitura. Ele os mantém em um formato binário bruto e só faz a conversão mais para frente no fluxo, caso algum step exija (como um cálculo ou filtro). Se o dado for apenas passar reto do CSV para um banco de dados, deixar isso marcado economiza muito processamento.

### Estrutura e Comportamento da Leitura

* **Header row present?:** Indica se a primeira linha do seu arquivo é o cabeçalho com o nome das colunas. Se desmarcado, o PDI vai tratar a primeira linha já como dado.
* **Add filename to result:** Se marcado, cria uma nova coluna no fluxo de dados contendo o caminho e o nome do arquivo de origem. Muito útil quando você está lendo múltiplos CSVs de uma pasta e precisa rastrear de qual arquivo cada linha veio.
* **The row number field name (optional):** Se você preencher um nome aqui (ex: `numero_linha`), o PDI criará uma coluna no seu fluxo enumerando cada linha lida (1, 2, 3...).
* **Running in parallel?:** Usado quando você tem um arquivo CSV massivo. Se marcado, permite que múltiplas instâncias (cópias) desse mesmo step leiam partes diferentes do arquivo ao mesmo tempo. *Nota: Geralmente exige configuração adicional no step para definir o número de cópias.*
* **New line possible in fields?:** Marque isso **apenas** se você tiver colunas de texto longo (como campos de observação ou comentários) que contenham "Enters" (quebras de linha) dentro do próprio texto. Se não marcar isso e houver quebras, o Pentaho achará que é uma nova linha de registro e vai quebrar os dados. *Atenção: Marcar isso deixa a leitura do arquivo consideravelmente mais lenta.*
* **Format:** Define o padrão de quebra de linha do sistema operacional onde o arquivo foi gerado (`DOS`, `UNIX` ou `mixed`). Na dúvida, `mixed` lida bem com a maioria.
* **File encoding:** A codificação de caracteres do arquivo. Extremamente importante para evitar problemas com acentuação (ç, á, ã). Os mais comuns são `UTF-8` ou `ISO-8859-1` / `Windows-1252`.

### Grade Inferior (Mapeamento das Colunas)

Esta é a tabela onde o PDI "tipa" os dados que estão entrando.

* **Name:** O nome da coluna.
* **Type:** O tipo de dado no Pentaho (String, Integer, Number, Date, Boolean, etc.).
* **Format:** A máscara do dado. Essencial para Datas (ex: `yyyy-MM-dd`) ou Números (ex: `#.#,00`).
* **Length / Precision:** Tamanho do campo e casas decimais.
* **Currency / Decimal / Group:** Símbolos usados para formatação financeira, separador decimal e agrupador de milhares (importante configurar se o arquivo usa `,` ou `.` para decimais).
* **Trim type:** Permite limpar espaços em branco indesejados automaticamente (à esquerda, à direita ou ambos).

**Dica de Ouro:** Dificilmente você precisa preencher essa grade inferior na mão. Basta preencher o caminho do arquivo, o delimitador, e clicar no botão **Obtem campos** (Get Fields) lá embaixo. O PDI vai ler uma amostra do arquivo e tentar adivinhar o nome, o tipo e o formato de cada coluna automaticamente. Depois, você clica em **Preview** para garantir que a leitura ficou perfeita!

## Text File

![alt text](image-2.png)

Excelente adição para a sua apostila! O step **Text file input** (na sua imagem traduzido como *Leitura de arquivo plano*) é o grande "coringa" do Pentaho.

Embora o *CSV file input* (que vimos primeiro) seja mais rápido e otimizado especificamente para CSVs simples, este step de Arquivo Plano é muito mais flexível. Ele permite ler arquivos delimitados (separados por vírgula, ponto e vírgula, tabulação) e também arquivos de **tamanho fixo** (fixed-width, onde cada coluna tem uma quantidade exata de caracteres, muito comum em arquivos de sistemas bancários antigos ou Mainframes).

A aba **File** deste step funciona de forma quase idêntica à do Excel. Vamos ao detalhamento:

---

### Aba "File" (Seleção de Arquivos de Texto)

#### 1. Configuração Básica

* **Nome do Step:** O nome da etapa no seu fluxo (no seu caso, `regiao`).

#### 2. Seleção de Arquivos (File or directory)

* **File or directory:** O caminho onde o seu arquivo `.txt`, `.csv` ou `.dat` está. Assim como no Excel, **é obrigatório clicar no botão "Add"** para que o arquivo vá para a lista de baixo.
* **Regular Expression (Wildcard):** Expressão regular para ler múltiplos arquivos de uma pasta. *Exemplo: Se você recebe um arquivo por dia e quer ler todos de uma vez, pode apontar para a pasta e colocar `.*\.txt` aqui.*
* **Exclude Regular Expression:** Expressão regular para ignorar arquivos específicos (ex: ignorar um arquivo chamado `leia-me.txt` que esteja no meio dos dados).

#### 3. Tabela de Arquivos Selecionados (Selected files)

No seu print, já vemos o arquivo `C:\Material_PDI\Inputs\Arquivos_texto\regiao.txt` adicionado.

* **Required (N/Y):** Se estiver como `Y` (Sim) e o arquivo não existir na pasta, o Pentaho gera um erro fatal e para. Se `N` (Não), ele ignora e segue o fluxo vazio.
* **Include subfolders (N/Y):** Se buscará arquivos em subpastas.

#### 4. Leitura Dinâmica (Accept filenames from previous steps)

Igual ao Excel, serve para automação avançada, recebendo o nome do arquivo de um passo anterior.

* **Accept filenames from previous step:** Ativa a leitura dinâmica.
* **Pass through fields from previous step:** *[Diferencial deste step]* Se ativado, além de ler o arquivo de texto, o Pentaho vai "carregar" junto as colunas que vieram do step anterior e juntar com os dados lidos do texto.
* **Step / Field:** Define de onde vem a informação do caminho do arquivo.

#### 5. Botões de Visualização Inferiores (Diferenciais)

Estes botões são excelentes aliados para investigar o arquivo antes mesmo de configurar o resto:

* **Show filename(s)...:** Mostra uma lista de todos os arquivos que o Pentaho conseguiu encontrar com base nas suas configurações da grade.
* **Show file content:** Abre uma janelinha mostrando o texto bruto do arquivo selecionado. Ótimo para você descobrir qual é o delimitador que está sendo usado ou se o arquivo tem algum lixo no cabeçalho.
* **Show content from first data line:** Mostra o arquivo ignorando o cabeçalho (útil para ver direto os dados).

---

### Resumo das Outras Abas (Para a sua Apostila)

Como este step é o mais versátil para arquivos de texto, as outras abas são fundamentais:

* **Aba `Content` (Conteúdo):** É aqui que a mágica da formatação acontece. Você vai definir se o tipo de arquivo é **CSV** (delimitado) ou **Fixed** (tamanho fixo). Também é aqui que você define o delimitador (se for o caso), a codificação (*Encoding* como UTF-8) e se tem cabeçalho.
* **Aba `Error Handling` (Tratamento de Erros):** Permite configurar o que fazer se o Pentaho encontrar uma linha corrompida (ex: uma letra onde deveria ter um número). Em vez de quebrar tudo, você pode mandar os erros para um arquivo `.txt` de log separado.
* **Aba `Filters` (Filtros):** Permite pular linhas inteiras do arquivo que contenham uma palavra específica. Útil para limpar arquivos que vêm com "rodapés" inúteis gerados por sistemas legados.
* **Aba `Fields` (Campos):** A grade onde ficam os nomes das colunas e os tipos de dados (String, Date, Number). Assim como no CSV, você usará o botão "Obter Campos" para o Pentaho preencher isso automaticamente para você.

## Microsoft Excel

![alt text](image-1.png)

Como o Excel é um arquivo mais complexo (pode ter várias planilhas/abas internas e formatos diferentes), esse step é dividido em várias abas (`Files`, `Sheets`, `Content`, etc.).

A imagem que você mandou mostra a aba principal: **Files**. Vamos destrinchar o que cada campo faz nela, e depois te dou um resumo rápido das outras abas para sua apostila ficar completa!

---

### Aba "Files" (Seleção e Configuração dos Arquivos)

Esta aba é focada exclusivamente em dizer ao PDI **quais** arquivos Excel ele deve ler e **como** ele deve abrir (qual motor usar).

#### 1. Configurações Básicas e Motor (Engine)

* **Nome do Step:** Assim como no CSV, é o nome descritivo do passo no seu fluxo (ex: `Excel Input - Vendas Mensais`).
* **Spread sheet type (engine):** **[MUITO IMPORTANTE]** O Excel mudou muito ao longo dos anos. Aqui você diz ao PDI qual "motor" (biblioteca Java) usar para ler o arquivo:
* *Excel 97-2003 XLS (JXL):* Use apenas para arquivos antigos (`.xls`).
* *Excel 2007 XLSX (Apache POI):* Use para arquivos modernos (`.xlsx`). Ele carrega o arquivo na memória RAM, então é rápido, mas pode dar erro de falta de memória (*Out of Memory*) se o Excel for gigantesco.
* *Excel 2007 XLSX (Apache POI Streaming):* Use para arquivos `.xlsx` **muito grandes**. Ele lê o arquivo em blocos, economizando muita memória RAM, mas pode ser um pouquinho mais lento e tem algumas limitações com formatação complexa.

* **Password:** Se a sua planilha Excel for protegida por senha, é aqui que você a insere para que o PDI consiga abri-la.

#### 2. Seleção de Arquivos (File or directory)

Diferente do CSV, o step do Excel permite ler dezenas de arquivos de uma vez só de uma pasta.

* **File or directory:** Você usa o botão "Navegar..." para achar o arquivo ou a pasta. **Atenção:** Apenas preencher o caminho aqui não faz o PDI ler o arquivo. Você **precisa clicar no botão "Add"** para que o arquivo desça para a tabela "Selected files" (veja abaixo).
* **Regular Expression (Wildcard):** Se você selecionou uma *pasta* em vez de um arquivo, você pode usar uma Expressão Regular para ler vários arquivos de uma vez. *Exemplo: `.*\.xlsx` vai ler todos os arquivos XLSX dentro daquela pasta.*
* **Exclude Regular Expression:** O oposto do item acima. Útil para ignorar arquivos específicos numa pasta (ex: ignorar arquivos que comecem com "bkp_").

#### 3. Tabela de Arquivos Selecionados (Selected files)

É aqui que a "mágica" acontece. **O PDI só vai ler o que estiver listado nesta grade.**

* **File/Directory:** O caminho que foi adicionado.
* **Wildcard / Exclude wildcard:** As regras de inclusão/exclusão (se você adicionou uma pasta).
* **Required:** Se estiver marcado como `Y` (Yes) e o PDI não encontrar o arquivo na hora de rodar, o pipeline dá erro e para. Se estiver `N` (No), ele apenas ignora e continua o fluxo.
* **Include subfolders:** Se você adicionou uma pasta, marca se o PDI deve vasculhar dentro das subpastas atrás de mais arquivos Excel.

#### 4. Leitura Dinâmica (Accept filenames from previous steps)

Esta é uma funcionalidade **avançada e essencial** para pipelines profissionais.

* **Accept filenames from previous step:** Se você marcar essa caixinha, a grade "Selected files" é ignorada. Em vez disso, o step vai esperar que o caminho do arquivo venha de um step anterior através de uma "seta" (hop).
* **Step to read filenames from:** Qual step anterior está enviando os nomes dos arquivos (ex: um step "Get File Names" ou uma consulta num banco de dados).
* **Field in the input to use as filename:** Qual é o nome da coluna (campo) que está chegando e que contém o caminho completo do arquivo Excel.
* *Para que serve?* Imagine que você tem uma tabela no banco de dados com a lista de relatórios que precisam ser processados hoje. Você lê essa tabela e passa os caminhos dinamicamente para o Excel Input.

---

### Resumo das Outras Abas (Para sua Apostila)

Como o Excel tem peculiaridades, você vai precisar navegar pelas outras abas antes de clicar em OK:

* **Aba `Sheets` (Planilhas):** Onde você define quais "abas" do seu arquivo Excel (ex: Plan1, Plan2, ou "Resumo") serão lidas. Você precisa adicionar o nome das sheets que quer processar.
* **Aba `Content` (Conteúdo):** Onde você diz se o arquivo tem cabeçalho (*Header*), se quer ignorar linhas em branco (*Strict types*) ou limitar o número de linhas lidas (*Limit*).
* **Aba `Fields` (Campos):** Exatamente igual à grade inferior do CSV. Onde você clica em "Obter campos" (Get fields from header row) para o PDI mapear as colunas, dizer se é String, Number, Date, etc.

**Dica de Ouro para sua Apostila:** Se o PDI estiver fechando sozinho ou dando erro de Java ao ler um arquivo `.xlsx` grande, volte na aba "Files" e mude a Engine para **"Apache POI Streaming"**. Isso resolve 99% dos problemas de gargalo de memória com Excel!

## Dummy

![alt text](image-3.png)

O step **Dummy (do nothing)** é um dos mais curiosos do Pentaho porque, como o próprio nome e a descrição dizem, ele **não faz absolutamente nada** com os dados. Ele apenas recebe as linhas e as passa para o próximo step exatamente do jeito que chegaram.

Mas se ele não altera os dados, por que ele é tão utilizado por profissionais de dados?

Aqui estão os **4 principais motivos** para você usar o Dummy no seu dia a dia:

### 1. Ponto de Encontro (Organização de Fluxo)

Esta é a função número um do Dummy. Imagine que você está lendo dados de 3 arquivos Excel diferentes e quer aplicar a mesma transformação em todos eles (por exemplo, deixar todos os nomes em maiúsculo).
Em vez de criar três steps de transformação iguais, você liga a saída dos 3 arquivos Excel em um único step **Dummy**. Depois, liga o Dummy no step de transformação. Ele age como um funil ou rotatória, organizando o trânsito e deixando seu fluxo visualmente limpo.

### 2. O Caminho do "Lixo" (Sink / Fim da Linha)

Quando você usa steps de decisão, como o **Filter Rows** (Filtrar Linhas), você é obrigado a dizer para onde vão as linhas que passaram no filtro (Verdadeiro) e as linhas que foram barradas (Falso).
Se você só se importa com as linhas Verdadeiras e quer simplesmente descartar as Falsas, você aponta a seta do Falso para um step **Dummy**. Ele servirá como um "ralo" ou "lixeira" segura para onde esses dados vão e morrem, sem dar erro no fluxo.

### 3. Ponto Seguro para Testes (Preview)

Muitas vezes, enquanto estamos desenvolvendo, queremos ver como os dados estão ficando antes de enviá-los de fato para o Banco de Dados final (Insert/Update).
Para evitar gravar dados errados por acidente durante os testes, os desenvolvedores colocam um **Dummy** no final da linha provisoriamente. Assim, você pode clicar nele e usar o "Preview" tranquilamente, sabendo que os dados não estão indo para lugar nenhum ainda.

### 4. Esqueleto do Projeto (Placeholder)

Às vezes você sabe que o dado precisará passar por uma validação complexa, mas você ainda não sabe como fazer isso ou está esperando uma regra de negócio do seu chefe. Você pode colocar um **Dummy** lá com o nome "Validar CPF futuramente", apenas para desenhar o fluxo completo e deixar aquele "espaço reservado" para ser substituído pelo step correto depois.

---

**Resumo para a Apostila 📝:**
Pense no step **Dummy** como um "cone de trânsito" organizador. Ele não altera a carga, não muda a velocidade e não transforma nada, mas é essencial para organizar as rotas dos seus dados, unir caminhos e criar pontos seguros de parada!