# Documentação do Projeto CRUD Spring Boot

## Visão Geral

Este projeto é uma API REST desenvolvida com Spring Boot que implementa operações CRUD (Create, Read, Update, Delete) para gerenciar produtos. A aplicação utiliza PostgreSQL como banco de dados e segue a arquitetura MVC (Model-View-Controller).

## Estrutura do Projeto

```
crud/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── punkrig/
│   │   │           └── crud/
│   │   │               ├── controllers/
│   │   │               │   └── ProductController.java
│   │   │               ├── dtos/
│   │   │               │   └── ProductDto.java
│   │   │               ├── model/
│   │   │               │   └── Product.java
│   │   │               ├── repositories/
│   │   │               │   └── ProductRepository.java
│   │   │               └── CrudApplication.java
│   │   └── resources/
│   │       ├── application.properties
│   │       └── db/
│   │           └── migration/
│   │               └── v1__product.sql
```

## Componentes da Aplicação

### 1. Ponto de Entrada da Aplicação (`CrudApplication.java`)

Este é o arquivo principal que inicia a aplicação Spring Boot.

```java
@SpringBootApplication
public class CrudApplication {
    public static void main(String[] args) {
        SpringApplication.run(CrudApplication.class, args);
    }
}
```

### 2. Modelo de Dados (`Product.java`)

A classe `Product` representa a entidade que será persistida no banco de dados.

```java
@Entity(name = "product")
@Table(name="product")
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;
    private String name;
    private Long price;
    
    // Construtores, getters e setters
}
```

### 3. Data Transfer Object (`ProductDto.java`)

O DTO é utilizado para transferir dados entre camadas da aplicação, especialmente na comunicação com o cliente.

```java
public record ProductDto(String name, Long price) {
}
```

### 4. Repositório (`ProductRepository.java`)

Interface que estende `JpaRepository` para fornecer operações de CRUD básicas para a entidade `Product`.

```java
public interface ProductRepository extends JpaRepository<Product,Integer> {
}
```

### 5. Controlador REST (`ProductController.java`)

Responsável por expor os endpoints da API REST e tratar as requisições HTTP.

```java
@RestController
@RequestMapping("/products")
public class ProductController {
    @Autowired
    ProductRepository repository;
    
    // Endpoints para operações CRUD
}
```

### 6. Configuração do Banco de Dados (`application.properties`)

Arquivo de configuração para conexão com o banco de dados PostgreSQL.

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/product-api
spring.datasource.username=postgres
spring.datasource.password=root
spring.datasource.driver-class-name=org.postgresql.Driver
spring.jpa.hibernate.ddl-auto=update
```

### 7. Migração do Banco de Dados (`v1__product.sql`)

Script SQL para criação da tabela de produtos no banco de dados.

```sql
CREATE TABLE product(
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    price BIGINT NOT NULL
);
```

## Endpoints da API

### 1. Listar todos os produtos
- **URL**: `/products`
- **Método**: GET
- **Resposta de Sucesso**: `200 OK` com a lista de produtos

### 2. Obter produto por ID
- **URL**: `/products/{id}`
- **Método**: GET
- **Resposta de Sucesso**: `302 FOUND` com os detalhes do produto
- **Resposta de Erro**: `404 NOT FOUND` se o produto não existir

### 3. Cadastrar novo produto
- **URL**: `/products`
- **Método**: POST
- **Corpo da Requisição**: Objeto JSON com `name` e `price`
- **Resposta de Sucesso**: `201 CREATED` com o produto criado

### 4. Atualizar produto
- **URL**: `/products/{id}`
- **Método**: PUT
- **Corpo da Requisição**: Objeto JSON com os campos a serem atualizados
- **Resposta de Sucesso**: `200 OK` com o produto atualizado
- **Resposta de Erro**: `404 NOT FOUND` se o produto não existir

### 5. Excluir produto
- **URL**: `/products/{id}`
- **Método**: DELETE
- **Resposta de Sucesso**: `200 OK` com mensagem de confirmação
- **Resposta de Erro**: `404 NOT FOUND` se o produto não existir

