
# Exemple de mappage de relation One-To-Many avec Spring Boot, Spring Data JPA et HSQL
---
## 1.	Objectif
Créer un exemple de mappage d'une relation one-to-many  avec Spring Boot, Spring Data JPA et HSQL.

### Outils :

•	JDK 1.8 or later

•	Maven 3 ou plus

## 2.	Structure du projet
 
 ![3](https://user-images.githubusercontent.com/28655112/41151859-e79f15f0-6b09-11e8-8b4b-f6d62dbcf673.PNG)
 
## 3.	Configuration de projet
### 3.1.	Dépendances de projet
```bash
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>org.hsqldb</groupId>
        <artifactId>hsqldb</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```
### 3.2.	Propriétés de l'application
 application.properties :
 ```bash
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true
```
## 4.	Relation One-To-One

Les tables book et book_category ont une relation one-to-many via book.book_category_id et book_category.id.
 
 ![2](https://user-images.githubusercontent.com/28655112/41151938-27045886-6b0a-11e8-8cc6-c53719575255.png)

### Définir les entités JPA

L'entité JPA est définie avec l’annotation @Entity, représente une table dans la base de données. Elle signifie que la classe doit être gérée par la couche de persistance.

Dans la classe Book, on met le code suivant :
```bash
@ManyToOne
    @JoinColumn(name = "book_category_id")
    public BookCategory getBookCategory() {
        return bookCategory;
    }
```
 Dans la classe BookCategory, on met le code suivant :
   ```bash
   @OneToMany(mappedBy = "bookCategory", cascade = CascadeType.ALL)
    public Set<Book> getBooks() {
        return books;
    }
```
 ### Description des annotations :
 
•	@Table désigne la table utilisée en base pour représenter cet objet. Dans notre cas, cette annotation est facultative car par défaut la table prend le nom de la classe.

•	@Id indique que le champ est la clé primaire de la table.

•	@Column crée le lien entre la représentation du champ en base et sa déclaration dans le code. Plusieurs arguments, tous optionnels, sont paramétrables à travers cette annotation comme: name, length, nullable, unique…

•	@GeneratedValue avec son argument « strategy » apporte des informations sur la manière de gérer la clé primaire. Ici, elle est gérée de manière automatique par le SGBDR. L’attribut strategy peut prendre plusieurs valeurs :

  - Strategy = GenerationType.AUTO : La génération de la clé primaire est laissée à l’implémentation.  

  - Strategy = GenerationType.IDENTITY : La génération de la clé primaire se fera à partir d’une Identité propre au SGBD. Il utilise un type de colonne spéciale à la base de données. Exemple pour MySQL, il s’agit d’un AUTO_INCREMENT.

  - Strategy = GenerationType.TABLE : La génération de la clé primaire se fera en utilisant une table dédiée hibernate_sequence qui stocke les noms et les valeurs des séquences.

  - Strategy = GenerationType.SEQUENCE : La génération de la clé primaire se fera par une séquence définie dans le SGBD, auquel on ajoutera l’attribut generator. Cette stratégie doit être utilisée avec une autre annotation qui est @SequenceGenerator.

•	@OneToMany et ManyToOne : définissent une relation one-to-many et many-to-one entre les deux entités.

Dans l’exemple, l’annotation « @ManyToOne » est utilisée pour modéliser la relation entre la table « book » et la table « bookCategory » (une catégorie peut avoir plusieurs livres « 1-N »). 
C’est la relation principale. « @JoinColum » indique le nom de la colonne qui contiendra la contrainte d’intégrité vers la table « BookCategory». 

Dans la classe « BookCategory», l’annotation « @OneToMany » représente la relation inverse (« mappedBy »). Elle est facultative contrairement à la relation principale. 

Dans notre exemple, on  a utilisé dans l’annotation «@OneToMany»  l’attribut «Cascade». La valeur « CascadeType.ALL » indique à l’ORM que toutes les opérations (PERSIST, MERGE, REMOVE, REFRESH) effectuées sur l’objet Book sont implicitement effectuées sur les entités qui lui sont associées.. Ainsi, lors de la suppression d’un book, l’ORM supprimera en cascade toutes les commandes rattachées à ce dernier.

## 5.	Spring Data JPA Repository

Spring Data JPA contient certains intégré Repository mis en œuvre certaines fonctions communes à travailler avec la base de données: findOne, findAll, save…
```bash
public interface BookCategoryRepository extends JpaRepository<Book, Integer>{
}
```
## 6.	Exécuter l'application

 ![1](https://user-images.githubusercontent.com/28655112/41152102-9695b42e-6b0a-11e8-9817-7d4ec774c389.PNG)

   
