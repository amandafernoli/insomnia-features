# Insomnia Features

## Parte 1
O Insomnia é uma plataforma colaborativa de desenvolvimento de API de código aberto que facilita a criação de APIs de alta qualidade. Ela pode ser usada para envio de requisições REST, SOAP, GraphQ e GRPC. Com essa ferramenta é possível realizar documentação e automação. Com a sua versão CLI tools é possível implementar até testes em pipeline.

Empresas como cisco, Facebook, Tesla, Netflix, entre outras, utilizam essa plataforma durante o desenvolvimento. 

### Criando API Fake para usar o Insomnia
  - Pra usar o Insomnia, precisamos de uma API. Nesse caso nós vamos usar o JSON Server
  - O [JSON Server](https://github.com/typicode/json-server) cria uma API fake baseada num arquivo `data.json`
    - Vamos criar users e products para dados iniciais no `data.json`

      ```json
      {
        "users": [
          {
            "id": "1",
            "name": "Amanda",
            "email": "amandafernoli@outlook.com"
          }
        ],
        "products": []
      }
      ```
    
    - Para iniciar o JSON Server, vamos usar o seguinte comando: 
      ```sh
      json-server data.json -p 3333 -d 500 -w
      ```
    - `-p` é porta, `-d` é o delay na resposta, `-w` é modo watch

### Criando algumas requisições iniciais
  - Vamos criar pastas de Users e Products no Insomnia
  - Fazer a listagem de usuários e produtos cadastrados.
  - Criar requisições `GET Users` e `GET Products` como List no Insomnia
  - Colocar a seguinte URL no Insomnia `http://localhost:3333/users` e fazer a listagem
  - Criar rota Create: `POST Products`
    ```json
    {
      "title": "Coca-cola",
      "price": 8.50
    }
    ```
  - O `id` é gerado automaticamente pelo JSON Server, então não precisa colocar nesse caso.

## Parte 2
### Criando variáveis no Insomnia
  - E se um dia a _base URL_ mudasse e eu precisasse alterar em todas as rotas no Insomnia? Ia ser inviável ter que alterar manualmente passando uma por uma
  - Para isso, é possível criar variáveis por ambiente ou globais 
  - Variáveis em Base Enviroments (globais) são variáveis que devem ser compartilhadas com toda a aplicação
  - Variáveis em Sub Enviroments são indicadas para serem exclusivas do ambiente. Como de produção, staging ou desenvolvimento
    - Vamos criar variáveis em sub enviroments de desenvolvimento e staging
      ```json
      "development": {
        "url": "http://localhost:3333",
      }
      "staging": {
        "url": "http://api-example.companyname.com.br"
      }
      ```
  - Só de digitar `url` deve aparecer a variável, mas outra forma de inserir é digitando: `{{url}}`
  - Variáveis de contexto são as variáveis que são definidas dentro do contexto de cada pasta. Também é bem útil pra quando você quer alterar a rota em todos os lugares da pasta, se necessário
    - Criar variáveis de contexto para os recursos das rotas. Como a rota `/products`. Colocar no contexto: `"resource": "products"`
  
###  Funções
  - Criar o recurso `POST Orders` como Create
  - Colocar as variáveis `url` e `resource` na rota
  - Também é muito útil colocar data, id, ou talvez um base64 como um dado do body
    - Colocar o body:
      ```json
      {
        "date": "Press Ctrl+Space",
        "uuid": "Press Ctrl+Space uuid versão 4"
      }
      ```
  - Suponhamos que queremos alterar um único campo de um produto. Nesse caso vamos usar o `PATCH`
    - Criar o recurso `PATCH Products` como Update
    - Colocar o `id` na rota. Atualizar um produto alterando um campo no body
    - Criar o recurso `DELETE Products` como Delete
    - Colocar o `id` na rota
  - Suponhamos que você queira fazer testes e garantir que a rota de delete sempre apague um produto que exista. De preferência um que acabou de ser criado. Vamos criar uma forma de fazer isso usando Chain Request

### Chain Requests
  - Nós vamos criar um produto toda vez pro delete apagar ele logo em seguida 
    - Colocar `Response -> Body Attribute` na rota de Delete
  - Vai ficar vermelho porque não a Chain Request não foi definida
    - Para definir, selecionar a Request que vai ser feita
    - Alterar o Filter para `$.id`. O símbolo de `$` significa objeto principal da resposta
  - O Trigger Behavior funciona da seguinte forma:
	  - Never - Nunca disparar a requisição
	  - No History - Dispara a requisição quando não há histórico de chamada dela
    - When Expired - Dispara a requisição quando o dado expirar
    - Always - Sempre disparar todas as vezes
  - Nós vamos selecionar a opção de Always para que ele crie um produto toda vez que a rota de Delete for chamada
  - Para rotas de autenticação, a funcionalidade de When Expired é muito útil. Como, por exemplo, para trazer o token JWT toda vez que ele expirar
  - Também é possível colocar a Response com um cookie no Header de outra requisição
  - Também é possível trazer dados do Header das requisições
  - Também é possível colocar prompt para o usuário digitar o valor. 
    - No body da requisição `PATCH Product` coloque `price: contrl+space` e selecione Prompt
    - Title: `price`
    - Label: `Preço`
    - Default Value: `0`
    - Armazenar o valor até fechar a aplicação para não ter que digitar de novo
    	- Storage Key: `price`
    - Desmarcar o Mask Text. É pra quando o valor for do tipo password e fique com estrelinhas: `********`
    - Deixar marcado o Default to Last Value para ficar com o valor padrão como o último que foi digitado
    - Quando clicar em Send, ele pede um valor para digitar

## Parte 3 
### Filtro na resposta
  - Para uma resposta muito grande, é possível filtrar apenas os registros relevantes
    - Vá para a rota `GET Products`
    - De todos os produtos, selecionar o preço de cada um escrevendo: `$[*].price`
  - Se uma resposta com apenas um item, poderia ser apenas `$.price`
  - Do lado tem o histórico de filtros e do lado tem uma pequena documentação pra fazer filtros

### Histórico de requests
  - No canto da tela à direita, podemos ver o histórico das requisições feitas com a requisição completa e a respectiva resposta

### Exportar requisições
  - Podemos exportar todas as requisições e passar pra pessoas da equipe 
  - Selecionar a opção de Insomnia v4 JSON

### Botão Insomnia
  - E ainda melhor que exportar, é possível criar um botão pra pessoa automaticamente importar dentro do Insomnia dela através dele
  - [Run-in Insomnia Button](https://docs.insomnia.rest/insomnia/run-in-insomnia-button)
  - Para fazer o botão:
    1. Gerar uma exportação do Insomnia e colocar o `export.json` no repositório no git
    2. Colocar a URL do export no gerador do botão nesse [link](https://insomnia.rest/create-run-button)
    3. Copie o Markdown gerado e você pode colocá-lo no README do seu repositório no Git Hub. Fica dessa forma:          
      [![Run in Insomnia}](https://insomnia.rest/images/run.svg)](https://insomnia.rest/run/?label=Teste%20Insomnia&uri=https%3A%2F%2Fgithub.com%2Finsodoc%2Finsomnia-documenter%2Fblob%2Fmaster%2Fpackage.json)
  - Com o plano pago do Insomnia, é possível ter as rotas atualizadas em tempo real e em todas as máquinas da equipe com tudo criptografado



  
