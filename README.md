# gestor-de-tarefas

# Utilizando Java com Spring

Entrada - processamento - saída  
Controlador - Classe/Entidade - Repositório  
Controller - Model - Repository

## Ambiente de desenvolvimento  

- Java instalado (JDK)
- Visual Studio Code
- Extensões Java e Sprig
- Banco de dados (MySQL) e ferramenta de administração (Workbench/DBEWaver ou mesmo PHPMyAdmin)
- Postman ou Imnsonia

## Dependência utilizadas em projetos com criação de API e banco de dados (MySQL)

- Dev Tools
- Spring Web
- Lombok
- MySQL JDBC
- Spring Data JPA

### Configuração do projeto (Java + Spring)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>3.1.2</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>br.com.aulajava</groupId>
	<artifactId>revisao-api</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>aula-api</name>
	<description>Demo project for Spring Boot</description>
	<properties>
		<java.version>17</java.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<scope>runtime</scope>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>com.mysql</groupId>
			<artifactId>mysql-connector-j</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<configuration>
					<excludes>
						<exclude>
							<groupId>org.projectlombok</groupId>
							<artifactId>lombok</artifactId>
						</exclude>
					</excludes>
				</configuration>
			</plugin>
		</plugins>
	</build>

</project>
```

### Exemplo de Model  
(Contato.java)
```java
package br.com.aulajava.revisaoapi.model;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import lombok.Data;

@Entity
@Data
public class Contato {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    @Column(nullable = false)
    private String nome;
    @Column(nullable = false)
    private String email;
    private String telefone;
    
}
```
(Produto.java)
```java
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import lombok.Data;

@Entity
@Data
public class Produto {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(length = 50, nullable = false)
    private String nome;

    @Column(length = 250)
    private String descricao;

    @Column(length = 3)
    private String unidade;
    
    private Long quantidade;
    private Double preco;

}
```

### Exemplo Repositório
(ContatoRepository.java)
```java
package br.com.aulajava.revisaoapi.repository;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import br.com.aulajava.revisaoapi.model.Contato;

@Repository
public interface ContatoRepository extends JpaRepository<Contato, Long> {
    
}
```

### Exemplo de Controller
(ContatoController.java)
```java
package br.com.aulajava.revisaoapi.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import br.com.aulajava.maisumaapi.model.Contato;
import br.com.aulajava.maisumaapi.repository.ContatoRepository;

@RestController
@RequestMapping("/contatos")
@CrossOrigin
public class ContatoController {
    @Autowired
    private ContatoRepository repositorio;

    @GetMapping
    public List<Contato> listar(){
        return repositorio.findAll();
    }

    @PostMapping
    public Contato adicionar(@RequestBody Contato contato){
        return repositorio.save(contato);
    }
    
    @PutMapping
    public Contato alterar(@RequestBody Contato contato){
        if(contato.getId() != null && contato.getId() > 0) {
            Contato contatoExistente = repositorio.findById(contato.getId()).orElse(null);
            if (contatoExistente != null) {
                contatoExistente.setNome(contato.getNome());
                contatoExistente.setEmail(contato.getEmail());
                contatoExistente.setTelefone(contato.getTelefone());

                return repositorio.save(contatoExistente);
            }
        }
        return null;
}

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> excluirContato(@PathVariable Long id) {
        Contato contatoExistente = repositorio.findById(id).orElse(null);
        if (contatoExistente != null) {
            repositorio.delete(contatoExistente);
            return ResponseEntity.noContent().build();
        } else {
            return ResponseEntity.notFound().build();
        }
    }
}
```

(ProdutoController.java)
```java
package br.com.aulajava.aulaapi.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import br.com.aulajava.revisaoapi.model.Produto;
import br.com.aulajava.revisaoapi.repository.ProdutoRepository;

@RestController
@RequestMapping("/produtos")
public class ProdutoController {

    @Autowired
    private ProdutoRepository produtoRepository;

    // Endpoint para listar todos os produtos
    @GetMapping
    public ResponseEntity<List<Produto>> listarProdutos() {
        List<Produto> produtos = produtoRepository.findAll();
        return ResponseEntity.ok(produtos);
    }

    // Endpoint para buscar um produto pelo ID
    @GetMapping("/{id}")
    public ResponseEntity<Produto> buscarProdutoPorId(@PathVariable Long id) {
        Produto produto = produtoRepository.findById(id).orElse(null);
        if (produto != null) {
            return ResponseEntity.ok(produto);
        } else {
            return ResponseEntity.notFound().build();
        }
    }

    // Endpoint para adicionar um novo produto
    @PostMapping
    public ResponseEntity<Produto> adicionarProduto(@RequestBody Produto produto) {
        Produto novoProduto = produtoRepository.save(produto);
        return ResponseEntity.status(HttpStatus.CREATED).body(novoProduto);
    }

    // Endpoint para atualizar um produto existente
    @PutMapping("/{id}")
    public ResponseEntity<Produto> atualizarProduto(@PathVariable Long id, @RequestBody Produto produto) {
        Produto produtoExistente = produtoRepository.findById(id).orElse(null);
        if (produtoExistente != null) {
            produtoExistente.setNome(produto.getNome());
            produtoExistente.setDescricao(produto.getDescricao());
            produtoExistente.setUnidade(produto.getUnidade());
            produtoExistente.setQuantidade(produto.getQuantidade());
            produtoExistente.setPreco(produto.getPreco());

            produtoRepository.save(produtoExistente);
            return ResponseEntity.ok(produtoExistente);
        } else {
            return ResponseEntity.notFound().build();
        }
    }

    // Endpoint para excluir um produto pelo ID
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> excluirProduto(@PathVariable Long id) {
        Produto produtoExistente = produtoRepository.findById(id).orElse(null);
        if (produtoExistente != null) {
            produtoRepository.delete(produtoExistente);
            return ResponseEntity.noContent().build();
        } else {
            return ResponseEntity.notFound().build();
        }
    }
}
```

### Exemplo de configuração das propriedades para acesso ao banco MySQL

```
spring.datasource.url=jdbc:mysql://localhost:3306/revisao-api
spring.datasource.username=root
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=update
```

## Testando as APIs

Para testar podemos utilizar ferramentas como Postman ou Imsonia  

```json
	{
		"nome": "Godofredo",
		"email": "godofredo@gmail.com",
		"telefone": "988997766"
	}
```

## Frontend

No frontend é possível acessar os endpoints utilizando HTML, CSS e JavaScript  

### Exemplo de página HTML
(index.html)
```html
<!DOCTYPE html>
<html>
<head>
    <title>Exemplo de Acesso a API</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <h1>Lista de Contatos</h1>
    <ul id="contatos-list"></ul>

    <h2>Adicionar Contato</h2>
    <form id="adicionar-contato-form">
        <label for="nome">Nome:</label>
        <input type="text" id="nome" placeholder="Nome" required>
        <label for="email">Email:</label>
        <input type="email" id="email" placeholder="Email" required>
        <label for="telefone">Telefone:</label>
        <input type="text" id="telefone" placeholder="Telefone">
        <button type="submit">Adicionar</button>
    </form>

    <script src="scripts.js"></script>
</body>
</html>
```

### Exemplo de CSS
(styles.css)
```css
body {
    font-family: Arial, sans-serif;
}

h1, h2 {
    text-align: center;
}

#contatos-list {
    list-style: none;
    padding: 0;
}

li {
    margin-bottom: 10px;
}

#adicionar-contato-form {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-top: 20px;
}

#adicionar-contato-form label,
#adicionar-contato-form input,
#adicionar-contato-form button {
    margin-bottom: 5px;
}

#adicionar-contato-form button {
    padding: 5px 10px;
}
```
### Exemplo de JavaScript
(scripts.js)
```javascript
// Selecionando elementos do DOM usando seus IDs
const contatosList = document.getElementById('contatos-list');
const adicionarContatoForm = document.getElementById('adicionar-contato-form');
const nomeInput = document.getElementById('nome');
const emailInput = document.getElementById('email');
const telefoneInput = document.getElementById('telefone');

// Função para carregar a lista de contatos do servidor via API
function carregarContatos() {
    fetch('http://localhost:8080/contatos') // Faz uma requisição GET para a API de contatos
        .then(response => response.json()) // Extrai os dados JSON da resposta da API
        .then(contatos => {
            contatosList.innerHTML = ''; // Limpa a lista de contatos atual

            // Itera sobre a lista de contatos recebida da API e adiciona cada contato à lista do DOM
            contatos.forEach(contato => {
                const li = document.createElement('li'); // Cria um elemento <li> para cada contato
                li.innerText = `${contato.nome} - ${contato.email} - ${contato.telefone || ''}`; // Define o texto do <li> com os dados do contato
                contatosList.appendChild(li); // Adiciona o <li> à lista de contatos no DOM
            });
        })
        .catch(error => console.error('Erro ao carregar contatos:', error)); // Tratamento de erro caso ocorra algum problema na requisição
}

// Evento para adicionar um novo contato quando o formulário for submetido
adicionarContatoForm.addEventListener('submit', function(event) {
    event.preventDefault(); // Previne o comportamento padrão de enviar o formulário

    // Cria um objeto JavaScript com os dados do novo contato a ser adicionado
    const novoContato = {
        nome: nomeInput.value,
        email: emailInput.value,
        telefone: telefoneInput.value
    };

    fetch('http://localhost:8080/contatos', {
        method: 'POST', // Faz uma requisição POST para a API de contatos para adicionar o novo contato
        headers: {
            'Content-Type': 'application/json' // Define o cabeçalho da requisição com o tipo de conteúdo JSON
        },
        body: JSON.stringify(novoContato) // Converte o objeto JavaScript em uma string JSON antes de enviar a requisição
    })
    .then(response => response.json()) // Extrai os dados JSON da resposta da API
    .then(contatoAdicionado => {
        console.log('Contato adicionado:', contatoAdicionado); // Exibe no console o contato que foi adicionado com sucesso
        carregarContatos(); // Atualiza a lista de contatos no DOM para exibir o novo contato adicionado
        nomeInput.value = ''; // Limpa o campo de nome após a adição do contato
        emailInput.value = ''; // Limpa o campo de email após a adição do contato
        telefoneInput.value = ''; // Limpa o campo de telefone após a adição do contato
    })
    .catch(error => console.error('Erro ao adicionar contato:', error)); // Tratamento de erro caso ocorra algum problema na requisição
});

// Carrega a lista de contatos ao carregar a página
carregarContatos();
```

### Exemplo de frontend de Produto
(index.html)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Minha Loja Online</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <h1>Minha Loja Online</h1>
    <div id="produtos-list" class="produtos-list">
        <!-- Aqui os produtos serão preenchidos dinamicamente pelo JavaScript -->
    </div>

    <script src="script.js"></script>
</body>
</html>
```
styles.css
```css
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
}

h1 {
    text-align: center;
}

.produtos-list {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
}

.produto-card {
    border: 1px solid #ccc;
    border-radius: 8px;
    padding: 10px;
    margin: 10px;
    width: 250px;
}

.produto-card img {
    max-width: 100%;
    border-radius: 8px;
}

.produto-card h3 {
    margin: 5px 0;
}

.produto-card p {
    margin: 0;
}

.produto-card button {
    display: block;
    margin-top: 10px;
}
```
script.js
```javascript
// Obter a referência ao elemento HTML que conterá a lista de produtos
const produtosList = document.getElementById('produtos-list');

// Função para carregar a lista de produtos a partir da API
function carregarProdutos() {
    // Faz uma requisição para a API no endpoint 'http://localhost:8080/produtos'
    fetch('http://localhost:8080/produtos')
        .then(response => response.json()) // Transforma a resposta em formato JSON
        .then(produtos => {
            // Limpa a lista de produtos antes de carregar os novos dados
            produtosList.innerHTML = '';

            // Itera sobre cada produto retornado pela API
            produtos.forEach(produto => {
                // Cria um card para o produto usando a função criarProdutoCard
                const produtoCard = criarProdutoCard(produto);

                // Adiciona o card do produto à lista de produtos na página
                produtosList.appendChild(produtoCard);
            });
        })
        .catch(error => console.error('Erro ao carregar produtos:', error));
}

// Função para criar um card para um produto
function criarProdutoCard(produto) {
    // Cria um elemento div que representa o card do produto
    const produtoCard = document.createElement('div');
    produtoCard.className = 'produto-card'; // Adiciona a classe 'produto-card' ao card

    // Cria uma imagem para o produto e define a URL da imagem e o texto alternativo
    const imagem = document.createElement('img');
    imagem.src = produto.imagemUrl; // Adicione a URL da imagem do produto se houver
    imagem.alt = produto.nome;

    // Cria um elemento h3 para exibir o nome do produto
    const nome = document.createElement('h3');
    nome.innerText = produto.nome;

    // Cria um elemento p para exibir a descrição do produto
    const descricao = document.createElement('p');
    descricao.innerText = produto.descricao;

    // Cria um elemento p para exibir o preço do produto com formatação em reais (R$)
    const preco = document.createElement('p');
    preco.innerText = `Preço: R$ ${produto.preco.toFixed(2)}`;

    // Cria um botão para permitir a ação de compra do produto
    const botaoComprar = document.createElement('button');
    botaoComprar.innerText = 'Comprar';
    botaoComprar.addEventListener('click', () => {
        // Exibe um alerta com uma mensagem de confirmação de compra
        alert(`Você comprou o produto: ${produto.nome}`);
    });

    // Adiciona todos os elementos criados ao card do produto
    produtoCard.appendChild(imagem);
    produtoCard.appendChild(nome);
    produtoCard.appendChild(descricao);
    produtoCard.appendChild(preco);
    produtoCard.appendChild(botaoComprar);

    // Retorna o card do produto criado
    return produtoCard;
}

// Carregar a lista de produtos ao carregar a página
carregarProdutos();
```

### Vamos pensar em uma Single Page Application (SPA)

Podemos começar fazendo uma página simples que lista os dados dos produtos e ir incrementando recursos a ela.

(index.html)
```html
<!DOCTYPE html>
<html>
<head>
    <title>Exemplo de Acesso a API</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <h1>Lista de Produtos</h1>
    <ul id="produtos-list"></ul>

    <script src="scripts.js"></script>
</body>
</html>
```

(scripts.js)
```javascript
// Selecionando elementos do DOM usando seus IDs
const produtosList = document.getElementById('produtos-list');

// Função para carregar a lista de produtos do servidor via API
function carregarProdutos() {
    fetch('http://localhost:8080/produtos') // Faz uma requisição GET para a API de produtos
        .then(response => response.json()) // Extrai os dados JSON da resposta da API
        .then(produtos => {
            produtosList.innerHTML = ''; // Limpa a lista de produtos atual

            // Itera sobre a lista de produtoss recebida da API e adiciona cada produto à lista do DOM
            produtos.forEach(produto => {
                const li = document.createElement('li'); // Cria um elemento <li> para cada produtos
                li.innerHTML = `<strong>${produto.nome}</strong> - ${produto.descricao} - ${produto.unidade} - ${produto.quantidade} - ${produto.preco || ''}
                <a href="#" class="alterar" data-id="${produto.id}">Alterar</a>
                <a href="#" class="excluir" data-id="${produto.id}">Excluir</a>`;
                produtosList.appendChild(li); // Adiciona o <li> à lista de produtos no DOM
            });
        })
        .catch(error => console.error('Erro ao carregar produtos:', error)); // Tratamento de erro caso ocorra algum problema na requisição
}

// Carrega a lista de produtos ao carregar a página
carregarProdutos();
```
