# PortSwigger — Descoberta de recurso administrativo e acesso indevido

## 1. Objetivo

Resolver o laboratório identificando um recurso escondido na aplicação Web e utilizando a informação encontrada para obter acesso à funcionalidade administrativa, realizando a exclusão do usuário solicitado.

## 2. Abordagem inicial

Inicialmente, considerei utilizar a ferramenta Hydra para realizar tentativas de brute force com o objetivo de descobrir as credenciais administrativas.

Entretanto, durante a análise, percebi que essa abordagem não seria necessária e que poderia gerar um número elevado de tentativas sem atacar a causa real do problema.

A descrição do laboratório indicava a existência de um arquivo escondido, o que direcionou minha investigação para a descoberta de recursos da aplicação.

## 3. Metodologia utilizada

### 3.1. Interceptação com Burp Suite

Utilizei o Burp Suite para observar e analisar as comunicações entre o navegador e a aplicação Web.

Com o Proxy habilitado, iniciei a navegação pelo laboratório e utilizei a funcionalidade Target para visualizar os recursos identificados durante a interação com a aplicação.

### 3.2. Descoberta do recurso

Durante a análise dos recursos apresentados na estrutura do Target, identifiquei o host ou caminho necessário para realizar uma requisição GET e acessar o recurso procurado.

Essa descoberta permitiu avançar na investigação sem precisar realizar tentativas de autenticação por brute force.

### 3.3. Acesso à funcionalidade administrativa

Após encontrar o recurso necessário, consegui acessar a funcionalidade administrativa disponibilizada pela aplicação.

Com o acesso obtido, realizei a exclusão do usuário solicitado pelo laboratório e concluí o desafio.

## 4. Ferramentas utilizadas

| Ferramenta | Finalidade |
|---|---|
| Burp Suite | Interceptação e análise das requisições HTTP |
| Proxy | Observação do tráfego entre navegador e aplicação |
| Target | Visualização e organização dos recursos identificados |
| Navegador | Interação com a aplicação Web |

Hydra foi considerada inicialmente, mas não foi utilizada na resolução final.

## 5. Vulnerabilidade e observações

O laboratório demonstrou como a exposição de um recurso e a ausência de controles adequados de acesso podem permitir que funcionalidades administrativas sejam alcançadas indevidamente.

A descoberta do recurso escondido foi essencial para a resolução.

A classificação técnica específica da vulnerabilidade deverá ser confirmada com a identificação da rota, das requisições e dos controles de autorização envolvidos.

## 6. Aprendizados

- Entendimento do funcionamento do Proxy no Burp Suite.
- Utilização da aba Target para identificar recursos da aplicação.
- Compreensão do método HTTP GET.
- Importância da descoberta de recursos durante uma avaliação Web.
- Diferença entre tentar descobrir credenciais e identificar falhas de controle de acesso.
- Importância de revisar a abordagem quando a hipótese inicial não corresponde às evidências.

## 7. Dificuldades encontradas

Durante a execução, tive dificuldades de desempenho na máquina virtual Kali Linux.

A navegação pelo Firefox estava lenta, com demora no carregamento das páginas, prejudicando a velocidade da investigação e a realização de testes.

Apesar dessa limitação, consegui concluir o laboratório de forma independente, utilizando os conhecimentos adquiridos durante meus estudos no TryHackMe e na prática com o Burp Suite.

## 8. Conclusão

O laboratório foi concluído sem a utilização de ferramentas de IA durante a resolução.

O principal aprendizado foi perceber que uma abordagem baseada em brute force não era necessária e que a investigação dos recursos da aplicação poderia revelar um caminho mais direto para compreender e explorar a falha.

A atividade contribuiu para desenvolver minha capacidade de análise, descoberta de recursos e utilização prática do Burp Suite em testes de segurança Web.

## 9. Evidências

![alt text](<Screenshot 2026-10-31 191643.png>)
![alt text](<Screenshot 2026-10-31 192428.png>)
