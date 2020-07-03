- symfony new panterest --full

- symfony serve -d

- symfony console make:controller pins

- symfony console make:entity Pin
    title
    string
    255
    no
    description
    text
    no
    Enter

Créer la base de données 'panterest_dev'

Créer le fichier '.env.local'

Dans 'Pin.php', ajouter : '@ORM\Table(name="pins")'

- symfony console make:migration

Dans le fichier 'Version...' de la migration, modifier : "return 'Create pins table'";

- symfony console doctrine:migrations:migrate
    y
    (symfony console doctrine:migrations:status ou symfony console doctrine:migrations:status --show-versions)

- composer req theofidry/psysh-bundle --dev (Pour créer des pins via le terminal)
- symfony console psysh
- use App\Entity\Pin;
- $em = $container->get('doctrine')->getManager();
- $p1 = new Pin;
- $p1->setTitle('Pin 1')
- $p1->setDescription('Description 1')
- dump($p1)
- $em->persist($p1)
- $em->flush()
- exit()

- symfony console make:twig-extension
    AppExtension (crée 'src/Twig/AppExtension.php')