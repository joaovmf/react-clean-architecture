# Clean-Archtecture

## Separando as camadas no login: 

### Camada de domínio (domain) - Onde ficam as regras de negócio. 

- Dentro dessa camada teremos o authentication, como uma interface. Será feito uma classe de autenticação que precisa de um email e uma senha para se autenticar e que isso vai te retornar um token de acesso por exemplo. 

### Camada de acesso de dados (data layer). 

- Nela você fará o remote authentication, ou seja, o acesso a alguma API remota. Nessa camada será tratada as repostas e erros da API. Essa autenticação remota será uma classe. 

- Dentro dela, terá uma interface http post client, para definir que alguma parte de seu código fará essa requisição http. 

### Camada de infra-estrutura (infra)

- Onde será feita implementação do http post client, que foi criado na interface da camada de acesso de dados. 

- Será criado um client que saiba fazer um post. Podemos utilizar o axios ou fetch para isso.

### Camada de apresentação de dados (presentation).

- Essa é a camada que pegará a resposta de dados e converterá para apresentação da UI no formato necessitado. 

- Aqui teremos o componente principal que é o login, que dependerá apenas da camada de domínio.  

- As principais funções do login serão renderizar a view e controlar os estados dos componentes. 

- O login, apesar de ser um componente, deve ser considerado uma página que você quebra em componentes pequenos, sendo eles reutilizáveis e sem estar acoplados a nenhum outra tela. 

- Se for necessáio criar uma instância para chamar outra página, deve-se fazer pelo link das rotas, indicando o path e reaproveitando componentes já prontos, como um header ou um footer.  

- A camada de login também grava os dados do cache. 

- Será criada uma interface no Login que vai depender de uma abstração, que será o validation. 

- A melhor forma para desacoplar camadas é criar uma interface própria camada e "dizer" que o meu login vi depender de uma abstração (validation) e defino a regra: eu preciso que o campo receba os parâmetros determinados e ai quem ta responsável por fazer a validação tem que se adaptar a esse protocolo definido no presentation. Então todos os campos do validation vão implementar esse protocolo. Para fazer isso você usa um design pattern que é o composite. O composite nada mais é que a composição de objetos similares. A ideia é que como eu preciso passar vários validadores para meu login, como password e senha, então o composite vai compor todos os pequenos validators e injetará no login. 

### Camada de validação (validation) 

- Vários componentes de validação para email e o que mais for necessário validar. 

### Camada “separada” - Main Layer. 

- Essa camada depende de tudo. Para fazer a tela funcionar, como a maioria dos componentes se tratam de interfaces abstratas, você vai precisar de uma camada que forneça os validadores, o remote auth. É ai que entra o main layer, tendo dentro dele alguns design patterns como factories. Aqui você terá classes que geram instancias de outras classes, ou seja, vai instanciar os componentes e interfaces.

- Esse design pattern acima é conhecido como composition route, que significa que o main layer será o ponto de entrada da aplicação e essa camada será acoplada com todas as outras para você conseguir manter as demais desacopladas. É necessário sacrificar uma camada para ter um sistema completamente limpo. 

- Dentro do main layer será criado o index que vai ser o ponto de entrada da aplicação. 

- Dentro do main estará toda injeção de dependência. 

### Resumão: 

1. Todos os componentes poderão ser aproveitados por outros projetos ou migrações de framework, devido estarem altamente desacoplados. 

2. Neste projeto, apenas os componentes do login estarão sendo reutilizados em outras páginas. 

3. Sempre há apenas uma dependência entre camadas e o domínio não depende de ninguém. 

4. A camada de infra depende apenas da camada de acesso de dados (axios). 

5. A camada de acesso de dados depende apenas da camada de domínio (acesso API).

6. A camada de login depende apenas da camada de dominio (composite).

7. A camada de validação vai depender apenas do login.

8. A camada de domínio não depende de ninguém. 
