# TelecomX_alura
# Análise de Churn para Telecom X

Este projeto tem como objetivo analisar dados de clientes da Telecom X para identificar os principais fatores que levam ao churn (cancelamento de serviços). A partir dessa análise, serão gerados insights valiosos para auxiliar a equipe de Data Science no desenvolvimento de modelos preditivos e estratégias eficazes para a redução da evasão de clientes.

## Fonte de Dados

Os dados utilizados nesta análise são fornecidos em formato JSON e foram obtidos de um repositório público no GitHub. O dataset contém informações detalhadas sobre os clientes da Telecom X, incluindo:

*   **customerID:** Identificador único do cliente.
*   **Churn:** Indica se o cliente cancelou o serviço (Yes) ou não (No).
*   **customer:** Dados demográficos do cliente (gênero, status de idoso, parceiro, dependentes, tempo de permanência - tenure).
*   **phone:** Informações sobre o serviço telefônico (serviço telefônico, múltiplas linhas).
*   **internet:** Detalhes sobre o serviço de internet (tipo de serviço, segurança online, backup online, proteção de dispositivo, suporte técnico, streaming de TV, streaming de filmes).
*   **account:** Informações da conta do cliente (tipo de contrato, cobrança sem papel, método de pagamento, encargos mensais e totais).

## Processo ETL

A etapa de **Extração** dos dados foi realizada lendo diretamente o arquivo JSON hospedado em um repositório do GitHub, utilizando a biblioteca `requests` para obter o conteúdo e, em seguida, carregando-o em um DataFrame pandas.

A **Transformação** dos dados envolveu várias etapas para garantir a qualidade e a usabilidade para análise:
- A estrutura JSON aninhada foi normalizada usando `pd.json_normalize()` para criar um DataFrame plano.
- Valores vazios ('') foram identificados nas colunas 'Churn' e 'account.Charges.Total'. As linhas com valores vazios na coluna 'Churn' foram removidas do DataFrame.
- A coluna 'account.Charges.Total', que foi inicialmente lida como tipo 'object', foi convertida para o tipo numérico (float) utilizando `pd.to_numeric`, com `errors='coerce'` para tratar quaisquer valores que não pudessem ser convertidos como NaN (embora a análise posterior tenha mostrado que não havia NaNs após a remoção das linhas vazias na coluna 'Churn').
- Uma nova feature, 'Total.Day', foi criada calculando o encargo médio diário a partir do 'account.Charges.Total' e do 'customer.tenure'. Esta nova coluna foi inserida no DataFrame.

Com os dados devidamente extraídos e transformados, a etapa de **Carga** implicitamente ocorreu ao armazenar os dados tratados no DataFrame pandas `df_plano`, tornando-os prontos para a Análise Exploratória de Dados (EDA).

## Análise Exploratória de Dados (EDA)

A Análise Exploratória de Dados (EDA) foi realizada para entender as características dos clientes e identificar padrões que podem estar relacionados ao churn. Foram criadas diversas visualizações para explorar a relação entre as variáveis do dataset e a evasão de clientes.

As visualizações criadas incluem:
- **Histogramas:** Utilizados para visualizar a distribuição de variáveis categóricas e numéricas em relação ao Churn.
- **Scatter Plot:** Utilizado para explorar a relação entre duas variáveis numéricas ('customer.tenure' e 'account.Charges.Monthly') colorindo os pontos por Churn.
- **Line Plot:** Criado para visualizar a taxa de churn ao longo do tempo de permanência ('customer.tenure').
- **Box Plot:** Utilizado para comparar a distribuição dos encargos mensais ('account.Charges.Monthly') para clientes que cancelaram e para aqueles que não cancelaram.

**Insights das Visualizações:**

- **customer.gender vs Churn:** A análise do gênero mostrou que a distribuição de churn é relativamente similar entre clientes masculinos e femininos, indicando que o gênero não é um fator determinante para o churn.
- **customer.SeniorCitizen vs Churn:** Clientes idosos ('SeniorCitizen' = 1) apresentam uma taxa de churn visivelmente maior em comparação com clientes não idosos ('SeniorCitizen' = 0).
- **customer.tenure vs Churn:** O histograma e o gráfico de linha da taxa de churn por tenure revelam que clientes com menor tempo de permanência (especialmente nos primeiros meses) têm uma probabilidade muito maior de churn. A taxa de churn diminui significativamente à medida que o tempo de permanência aumenta.
- **customer.tenure vs account.Charges.Monthly vs Churn:** O scatter plot mostra que clientes com tenure baixo e encargos mensais altos parecem ter uma tendência maior ao churn. Clientes com tenure alto, independentemente dos encargos mensais, tendem a não cancelar.
- **account.Contract vs Churn:** Clientes com contrato mensal ('Month-to-month') apresentam uma taxa de churn substancialmente mais alta do que aqueles com contratos de um ou dois anos. Contratos de longo prazo estão associados a uma menor probabilidade de churn.
- **account.Charges.Monthly vs Churn:** O box plot confirma que clientes que cancelam ('Yes' no Churn) tendem a ter encargos mensais medianos e superiores mais altos em comparação com aqueles que não cancelam ('No' no Churn).
- **phone.PhoneService vs Churn:** A maioria dos clientes possui serviço telefônico. O churn parece ocorrer tanto para clientes com quanto sem serviço telefônico, com uma leve tendência maior para quem possui o serviço.
- **internet.InternetService vs Churn:** Clientes com serviço de internet "Fiber optic" apresentam uma taxa de churn significativamente maior do que aqueles com "DSL" ou sem serviço de internet.
- **internet.StreamingTV vs Churn:** Clientes que utilizam serviço de streaming de TV ("Yes") parecem ter uma taxa de churn um pouco maior em comparação com aqueles que não utilizam ("No"). Clientes sem serviço de internet ("No internet service") raramente cancelam.
- **account.PaymentMethod vs Churn:** O método de pagamento "Electronic check" está associado a uma taxa de churn consideravelmente mais alta em comparação com outros métodos como "Mailed check", "Bank transfer (automatic)" e "Credit card (automatic)".

Estes insights iniciais destacam a importância de fatores como tempo de permanência, tipo de contrato, serviço de internet (especialmente fibra óptica), encargos mensais e método de pagamento na decisão do cliente de cancelar os serviços.

## Principais Insights

A análise exploratória de dados revelou diversos fatores cruciais associados ao churn de clientes na Telecom X:

- **Tempo de Permanência (Tenure):** Clientes com menor tempo de permanência apresentaram uma taxa de churn significativamente maior. A probabilidade de cancelar diminui drasticamente à medida que o cliente permanece mais tempo com a empresa.
- **Tipo de Contrato:** Clientes com contratos de mês a mês ("Month-to-month") são muito mais propensos a cancelar do que aqueles com contratos de um ou dois anos. Contratos de longo prazo indicam maior fidelidade.
- **Serviço de Internet:** Clientes que utilizam o serviço de internet de fibra óptica ("Fiber optic") têm uma taxa de churn consideravelmente mais alta em comparação com aqueles que usam DSL ou não têm serviço de internet.
- **Encargos Mensais e Totais:** Clientes com encargos mensais mais altos, especialmente aqueles com menor tempo de permanência, tendem a cancelar. O valor total pago também está relacionado ao tempo de permanência e, consequentemente, ao churn.
- **Método de Pagamento:** O método de pagamento "Electronic check" está fortemente associado a uma maior taxa de churn.
- **Status de Idoso:** Clientes idosos ("SeniorCitizen" = 1) mostram uma propensão maior ao churn em comparação com clientes não idosos.
- **Serviços Adicionais:** Alguns serviços adicionais, como Streaming de TV e Filmes, quando combinados com outros fatores (como internet de fibra óptica), parecem ter alguma influência no churn, embora menos impactante que tenure ou tipo de contrato.

Estes insights sugerem que a Telecom X deve focar em estratégias de retenção para clientes novos, revisar a oferta de serviços de fibra óptica e métodos de pagamento, e considerar programas de fidelidade para clientes de longo prazo.

