# Sobre o Refinamento

A entidade <em>**Cliente**</em> foi removida sendo criada a entidade <em>**Pessoa**</em>.

As entidades <em>**Pessoa_Fisica**</em> / <em>**Pessoa_Juridica**</em> atendem exclusivamente a delimitação do requerimento do desafio.

As entidades <em>**Pagamento**</em> / <em>**Cartao_Credito**</em> / <em>**Boleto**</em> atendem exclusivamente a delimitação do requerimento do desafio.

A entidade <em>**Entrega**</em> foi criada para contemplar a generalização de entrega.

<br />

**Observação:**

O desafio abordou a possibilidade de se receber N pagamentos, todavia não especificou quais seriam esses muito menos demais requisitos necessários para uma modelagem mais completa, todavia criei a tabela Boleto e Cartao_Credito saindo da abstração generalista desse dois e contemplando a possibilidade de cadastrar assim N cartões para a mesma pessoa, indaguei no fórum acerca dos demais meios e se deveriam ter entidades específicas, porém após 48 horas não houve resposta substancial, por fim, em grupo de WhatsApp o consenso foi que por se tratar de bootcamp introdutório não teria tal necessidade e a abordagem de abstração generalista conforme as aulas e mais simplista possível contemplaria o desafio.

Nesse ponto do bootcamp ainda não foi explanado sobre quais tipo de dados utilizar seguindo as melhores práticas, estando focado na explanação de entidade e atributos...

Todavia abaixo deixo um pouco de como realizei a análise de cada escolha, excluindo-se as PKs e FKs que possuem notas na imagem.

<br />

## Curiosidades sobre os campos

<br />

### Tabela Pessoa

<br />

###### Nome | VARCHAR(100)

A escolha de 100 caracteres pode parecer exagero e não otimizada, de fato se formos levar em consideração que o maior nome registrado no Brasil em 2018 foi:

Charlingtonglaevionbeecheknavare dos Anjos Mendonça (52 caracteres)

Fonte:
Conheça o homem com o nome mais longo do Brasil
https://www.youtube.com/watch?v=yzv-2zb779M

Ainda temos quase que o dobro de espaço para contemplar no caso de quebrarem esse recorde.
Outro ponto a destacar é que o desafio não informava se deveria ser tratado nomes estrangeiros e qual limitação para esses casos.

<br />

###### Tipo | TINYINT

Usaria lógica similar a:

16 = Pessoa Física
32 = Pessoa Jurídica

O desafio não contemplava, mas poderia usar também o código:

8 = Pessoa sem CPF

Tratando assim estrangeiros e adolescentes por exemplo, todavia, limitando-se ao desafio a modelagem por seguinte ficou restrita de 1:1, 1 pessoa pode ser Pessoa Física ou Pessoa Jurídica apenas.

<br />

### Tabela Pessoa_Fisica

<br />

###### <a id="CPF">CPF | BIGINT(11)</a> {#CPF}

O causador de muita desavença entre squad de desenvolvimento e DBAs :smile:

Vou defender minha preferência com apontamentos empíricos acumulados ao longo dos anos

Comparar string gera overhead extra, usar números reduz esse overhead.

Para começar a ver esse ganho experimente realizar consultas em tabelas com no mínimo 1 milhão de registros, Oracle é um dos bancos com maior ganho utilizando essa abordagem.

Para o "problema de comparar os zeros a esquerda" do CPF basta apenas usar ZEROFILL no campo uma vez que no bootcamp está sendo utilizado o MySQL.

Seria possível sim utilizar <em>**unsigned INT**</em> para economia de memoria ao limitar o escopo para 9 números do CPF não armazenando dessa forma os digito verificador (2 últimos números), todavia estaria confiando demais na camada de aplicação e no futuro se precisasse verificar a integridade dos dados nesse campo não teria certeza (sem digito verificador) se a camada de aplicação de fato enviou todos os dígitos válidos.

<br />

###### RG | VARCHAR(15)

Com a adição de letras ao código e em breve extinção do mesmo, varchar atende a demanda sem precisar aumentar a complexidade de algo que deixará de ser válido.

<br />

###### ORGAO_EMISSOR_RG | VARCHAR(15)

Idem explanação campo [RG](#rg--varchar15).

<br />

###### Data_Emissao_RG | DATE

Data Type Padrão

<br />

###### Data_Nascimento | DATE

Data Type Padrão

<br />

###### Sexo | Boolean

A interface do MySQL Workbench está com bug, toda vez que é selecionado Boolean ela muda para TINYINT.
Assim, o Data Type visando performance seria Boolean, cabendo a escolha se Verdadeiro para Homem ou Mulher e falso para outra opção.

<br />

### Tabela Pessoa_Juridica

<br />

###### CNPJ | BIGINT(14)

Idem explanação campo [CPF](#cpf--bigint11-cpf) da tabela Pessoa_Fisica.

<br />

###### Nome_Fantasia | VARCHAR(100)

Nome que poderá ser apresentado no Frontend.

<br />

###### Razao_Social | VARCHAR(115)

Limitação máxima padrão de campo.

Peguei de códigos de scripts pontuais que fiz para outros clientes, scripts que acessam os sistemas online sem erro, infelizmente não achei a referência original que usei como base.

<br />

###### Inscricao_Estadual | BIGINT(14)

Idem explanação campo [CPF](#cpf--bigint11-cpf) da tabela Pessoa_Fisica.

<br />

###### Inscricao_Municipal | BIGINT(15)

Idem explanação campo [CPF](#cpf--bigint11-cpf) da tabela Pessoa_Fisica.

<br />

### Tabela Pagamento

<br />

###### Tipo_Pagamento | TINYINT

ID único a ser utilizado para identificar a modalidade que foi escolhida.

Poderia ser ID para referenciar entidades específicas para um sistema mais performático, exemplo:

- Boleto
- C/C
- Cartão de Débito via TEF
- Cartão de Crédito via TEF
- Cartão de Crédito
- Cartão de Débito
- Carnê
- Cheque
- Dinheiro
- Vale Funcionário
- Faturamento
- Fidelidade
- Pix
- Vale Compra
- Vale Presente

Todavia, conforme explanado a priori a conclusão foi de que seria desnecessário visto ser curso introdutório.

<br />

###### Data_Pagamento | DATE

Para filtros referentes à data do pagamento, se foi ou não realizado e quando.

<br />

###### Valor | DECIMAL

Valor referente ao pagamento efetuado.

<br />

###### Data_Pagamento | DATE

Para filtros referentes à data do pagamento, se foi ou não realizado e quando.

<br />

### Tabela Boleto

<br />

###### Beneficiario | VARCHAR(115)

Referenciado como base no maior tamanho de Razão Social.

<br />

###### Agencia_Codigo | INT

Contempla os 5 digítos de agência.

<br />

###### Agencia_Digito | CHAR

Trata de Agências com letra no fim.

<br />

###### Numero_Documento | VARCHAR(50)

Bug no MySQL Workbench não permitia escolher Numeric.

<br />

###### CPF_CNPJ | BIGINT(14)

Tamanho visando o dado máximo que seria CNPJ.

<br />

###### Codigo_Boleto | VARCHAR(48)

Bug no MySQL Workbench não permitia escolher Numeric.

<br />

###### Codigo_Banco | SMALLINT

Contempla o range utilizado para os Bancos no Brasil.

<br />

###### Data_Vencimento | DATE

Data Padrão

<br />

###### Data_Documento | DATE

Data Padrão

<br />

###### Numero_Documento | BIGINT(14)

Contempla grandeza de ID variável próprio das instituições.

<br />

###### Nosso_Documento | BIGINT(11)

Contempla grandeza de ID variável próprio das instituições.


<br />

### Tabela Cartao_Credito

<br />

###### Numero_Cartao | BIGINT(19)

O tamanho varia de instituição para instituição, assim foi utilizado um tamanho que comportasse o maior número na data de geração desse modelo.

<br />

###### Nome_Titular | VARCHAR(45)

Tamanho padrão que é geralmente utilizado pelas operadoras.

<br />

###### Validade_Mes | TINYINT

Contempla os 12 meses.

<br />

###### Validade_ano | SMALLINT

Contempla os anos.


<br />

### Tabela Entrega

<br />

###### Tipo_Envio | TINYINT

Opções para histórico da modalidade de envio escolhida. 

Exemplo:

- 1 - Correios
- 2 - Transportadora A
- 3 - Trabsportadora B
- ...
- 15 - Avião
- ...

Dado opção de seguir pela abordagem simplista e generalista limitada somente ao enunciado e falta de maiores detalhes para algo pontual, as entidades para cada caso ou um modelo de super entidade contemplando esses não foi criado.

<br />

###### Codigo_Rastreio | VARCHAR(100)

Dado opção por via generalista, o código de rasterio ficou embutido nessa tabela.

<br />

###### Data_Envio | DATETIME

Data type padrão.

<br />

###### Data_Entrega | DATETIME

Data type padrão.

