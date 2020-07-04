- symfony new panterest --full


- symfony serve -d


- symfony console make:controller pins


(Créer une entité)
- symfony console make:entity Pin
    title
    string
    255
    no
    description
    text
    no
    Enter
    (Ne pas oublier les champs createdAt et UpdatedAt pour ne pas avoir à les ajouter plus tard (voir plus bas))


Créer la base de données 'panterest_dev'


Créer le fichier '.env.local'


Dans 'Pin.php', ajouter : '@ORM\Table(name="pins")'


(Faire la migration vers la base de données)
- symfony console make:migration
  (Dans le fichier 'Version...' de la migration, modifier : "return 'Create pins table'";)
- symfony console doctrine:migrations:migrate
    y
    (symfony console doctrine:migrations:status ou symfony console doctrine:migrations:status --show-versions)


(Pour créer des pins via le terminal)
- composer req theofidry/psysh-bundle --dev
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


(Créer une fonction Twig)
- symfony console make:twig-extension
    AppExtension (crée 'src/Twig/AppExtension.php')


(Utiliser le fuseau horaire voulu)
Créer le fichier 'php.ini'
Fermer le serveur (symfony server:stop) et le relancer (symfony serve -d)


(Pour rajouter 2 champs :)
- symfony console make:entity
    Pin
    createdAt
    datetime
    no
    updatedAt
    datetime
    no
    Enter
  Dans le fichier 'Pin.php', ajouter : '* @ORM\HasLifecycleCallbacks', la fonction updateTimeStamps() et 'options={"default": "CURRENT_TIMESTAMP"}' pour createdAt et updatedAt
  Supprimer les fichiers de migration dans le dossier 'Migration'
  symfony console make:migration
  symfony console doctrine:migrations:migrate


(Ordonner les pins sur la page d'accueil)
Dans le fichier 'PinsController.php', modifier : '$pins = $pinRepository->findBy([], ['createdAt' => 'DESC']);'


(Créer un formulaire)
- symfony console make:form
    PinType
    Pin