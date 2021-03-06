# Symfony 3.4 New

Attention : ce cours installe une version de Symfony 3.4 qui a une structure de dossier identique avec celle de Symfony 4.x.

Dans Symfony 4.x, certains dossiers changent de nom et d'emplacement :

- `/app/config` devient `/config`
- `/app/Ressources/views` devient `/templates`
- `/src/AppBundle` devient `/src`
- `/web` devient `/public`

## Install

Attention : l'installation nécessite la nouvelle version (`4.x`) de l'installeur Symfony. Voir [symfony-installer.md](symfony-installer.md).

Installer Symfony 3.4 :

    symfony new --full --version=3.4 my_project

Se rendre dans le dossier du projet :

    cd my_project

Installer le bundle `symfony/web-server-bundle` :

    composer require server --dev

Lancer le serveur web de développement :

    php bin/console server:run

Note : pour stopper le serveur, appuyer sur `CTRL + C`.

Pour tester l'installation, ouvrir l'url suivante dans un navigateur :

    http://localhost:8000/

## Configuration de l'accès à la base de données

Créez un fichier `.env.local` à la racine du projet.

Ouvrez le fichier `.env` à la racine du projet et copiez le bloc :

    APP_ENV=dev
    APP_SECRET=24209e2e73cff25fd43b74b21cf4e173

et colle-le dans le fichier `.env.local`.

Copiez aussi le bloc dans le fichier `.env` :

    DATABASE_URL=mysql://db_user:db_password@127.0.0.1:3306/db_name

et colle-le dans le fichier `.env.local`.

Ensuite, modifiez la ligne `DATABASE_URL` du fichier `.env.local` pour que Symfony puisse accéder à votre BDD.

Vous devez modifier les éléments `db_user`, `db_password`, `127.0.0.1`, `3306` et `db_name` avec les informations qui correspondent à votre configuration.

### Exemples

Voici la ligne originale :

    DATABASE_URL=mysql://db_user:db_password@127.0.0.1:3306/db_name

#### Wamp

Voici la ligne pour le user `root`, sans mot de passe, une connection sur le port `3306` (mysql) sur le même serveur, la BDD `my_project` :

    DATABASE_URL=mysql://root:@127.0.0.1:3306/my_project

#### MAMP

Voici la ligne pour le user `root`, le mot de passe `root`, une connection sur le port `8889` (mysql) sur le même serveur, la BDD `my_project` :

    DATABASE_URL=mysql://root:root@127.0.0.1:8889/my_project

#### Autre

Voici la ligne pour le user `root`, le mot de passe `123`, une connection sur le port `3306` (mysql) sur le même serveur, la BDD `my_project` :

    DATABASE_URL=mysql://root:123@127.0.0.1:3306/my_project

## Le `APP_SECRET` de `.env`

Si vous voulez regénérer le `APP_SECRET` de votre fichier `.env` (ou de votre `.env.local`), vous pouvez utiliser le script suivant qui génèrera une chaîne de 32 caractères en hexadécimal tirée au hasard :

    <?php
    $bytes = random_bytes(16);
    echo bin2hex($bytes).PHP_EOL;

Sinon vous pouvez utiliser un outil en ligne (qui génère 40 caractères et non 32) : [Symfony 2 Secret Generator - nux.net](http://nux.net/secret).

### Création de la base de données

#### Avec la console

Interrompre le serveur web avec `CTRL + C` si nécessaire.

Dans le terminal :

    php bin/console doctrine:database:create

Relancer le serveur web avec la commande `php bin/console server:run` si nécessaire.

#### Avec PhpMyAdmin

Avec PhpMyAdmin, créer une nouvelle base de données.

Les noms (BDD, tables, colonnes, etc) ne doivent comporter aucun espace ` `, ni tiret `-`, ni point `.`, ni accent.
Il faut donc se limiter aux caractères minuscules de l'alphabet, aux chiffres et au « underscore » (tiret du bas) `_`.

Pour le nom de la BDD, choisissez le même nom que votre projet, en remplaçant les caractères interdits par des caractères autorisés.

L'interclassement de la BDD doit être `utf8mb4_unicode_ci`.

## Langue

Pour configurer la langue, ouvrir le fichier `config/services.yaml`, et modifier le bloc :

    # Put parameters here that don't need to change on each machine where the app is deployed
    # https://symfony.com/doc/current/best_practices/configuration.html#application-related-configuration
    parameters:
        locale: 'en'

afin d'obtenir :

    # Put parameters here that don't need to change on each machine where the app is deployed
    # https://symfony.com/doc/current/best_practices/configuration.html#application-related-configuration
    parameters:
        locale: 'fr'

## Exemple de contrôleur minimaliste

Dans le dossier `src/Controller`, créer le fichier `MinimalController.php` :

    <?php
    // src/Controller/MinimalController.php

    namespace App\Controller;

    use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
    use Symfony\Component\HttpFoundation\Request;
    use Symfony\Component\Routing\Annotation\Route;
    use Symfony\Component\Routing\Annotation\Method;

    /**
     * @Route("/")
     */
    class MinimalController extends AbstractController
    {
        /**
         * @Route("/hello/{name}", name="minimal_hello_name")
         */
        public function helloName(Request $request, $name)
        {
            $greeting = "Hello {$name}!";

            return $this->render('minimal/hello-name.html.twig', [
                'greeting' => $greeting,
            ]);
        }
    }

Dans le dossier `templates`, créer un dossier `minimal` puis créer le fichier `hello-name.html.twig` dedans :

    {# templates/minimal/hello-name.html.twig #}
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <title>{{ greeting }}</title>
    </head>
    <body>
        <h1>{{ greeting }}</h1>
    </body>
    </html>

Pour tester de contôleur, vous devez lancer votre serveur web :

    php bin/console server:run

Puis ouvrir l'url associée [http://localhost:8000/hello/Toto](http://localhost:8000/hello/Toto).

## Accéder au service `database_connection`

Modifier tous les contrôleurs qui doivent utiliser le service `database_connection`, en ajoutant la ligne de code suivante :

    use Doctrine\DBAL\Connection;

    // ...

        private $conn;

        public function __construct(Connection $conn)
        {
            $this->conn = $conn;
        }

### Exemple avec notre contrôleur minimaliste

Dans le dossier `src/Controller`, modifier le fichier `MinimalController.php` :

    <?php
    // src/Controller/MinimalController.php

    namespace App\Controller;

    use Doctrine\DBAL\Connection;
    use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
    use Symfony\Component\HttpFoundation\Request;
    use Symfony\Component\Routing\Annotation\Route;
    use Symfony\Component\Routing\Annotation\Method;

    /**
     * @Route("/")
     */
    class MinimalController extends AbstractController
    {
        private $conn;

        public function __construct(Connection $conn)
        {
            $this->conn = $conn;
        }

        /**
         * @Route("/hello/{name}", name="minimal_hello_name")
         */
        public function helloName(Request $request, $name)
        {
            $greeting = "Hello {$name}!";

            return $this->render('minimal/hello-name.html.twig', [
                'greeting' => $greeting,
            ]);
        }

        /**
         * @Route("/items", name="minimal_items_index")
         */
        public function itemsIndex(Request $request, $name)
        {
            $title = 'Liste des items';

            $sql = 'SELECT * FROM item';
            $items = $this->conn->fetchAll($sql);

            return $this->render('minimal/items-index.html.twig', [
                'title' => $title,
                'items' => $items,
            ]);
        }
    }

Il faut aussi créer un template qui peut afficher les données de la requête SQL.
Dans le dossier `templates/minimal`, créer le fichier `items-index.html.twig` :

    {# templates/minimal/items-index.html.twig #}
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <title>{{ title }}</title>
    </head>
    <body>
        <h1>{{ title }}</h1>

        <ul>
        {% for item in items %}
            <li><a href="/item/{{item.id}}">{{ item.name }}</a></li>
        {% endfor %}
        </ul>
    </body>
    </html>

## Migrer des entités, des contrôleurs et des formulaires 2.8 vers 3.4

Si vous avez du code Symfony 2.8, ou du code Symfony 3.4 utilisant le namespace `AppBundle`, il est assez facile de le transformer en code compatible avec Symfony 3.4 sans bundle ou avec Symfony 4.x.

Attention : quand vous faites ce type de remplacement de chaîne de caractère, cocher la case « sensible à la casse ».

Dans les entités, remplacez les occurences de :

- `AppBundle` par `App`

Dans les contrôleurs, remplacez les occurences de :

- `AppBundle` par `App`
- `App:` par `App\Entity\`

Dans les formulaires, remplacez les occurences de :

- `AppBundle` par `App`
- `appbundle` par `app`

## Doc

### Install

- [Download Symfony Framework and Components](https://symfony.com/download)
- [Framework Configuration Reference (FrameworkBundle) (Symfony 3.4 Docs)](https://symfony.com/doc/3.4/reference/configuration/framework.html#secret)

### Entity

- [Databases and the Doctrine ORM (Symfony 3.4 Docs)](https://symfony.com/doc/3.4/doctrine.html#persisting-objects-to-the-database)
- [How to Work with Doctrine Associations / Relations (Symfony 3.4 Docs)](https://symfony.com/doc/3.4/doctrine/associations.html)
- [Association Mapping - Object Relational Mapper (ORM) - Doctrine](https://www.doctrine-project.org/projects/doctrine-orm/en/latest/reference/association-mapping.html)
- [Doctrine\Common\Collections\ArrayCollection | API](https://www.doctrine-project.org/api/collections/latest/Doctrine/Common/Collections/ArrayCollection.html)

### GET, POST, FILES, SESSION, COOKIES, SERVER

- [Symfony and HTTP Fundamentals (Symfony 3.4 Docs)](https://symfony.com/doc/3.4/introduction/http_fundamentals.html)
- [The HttpFoundation Component (Symfony 3.4 Docs)](https://symfony.com/doc/3.4/components/http_foundation.html)

### Form

- [Forms (Symfony 3.4 Docs)](https://symfony.com/doc/3.4/forms.html)
- [Form Types Reference (Symfony 3.4 Docs)](http://symfony.com/doc/3.4/reference/forms/types.html)
- [EntityType Field (Symfony 3.4 Docs)](http://symfony.com/doc/3.4/reference/forms/types/entity.html)
- [Validation Constraints Reference (Symfony 3.4 Docs)](http://symfony.com/doc/3.4/reference/constraints.html)

