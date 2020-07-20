1) Création d'un projet (full)
2) Lancement du projet en local
3) Création du controller PinsController
4) Création d'une entité Pin
5) Création de la base de données 'panterest_dev'
6) Création de pins via le terminal
7) Création d'une fonction Twig
8) Utilisation du fuseau horaire voulu
9) Rajout de 2 champs dans une entité
10) Ordonner les pins sur la page d'accueil
11) Création d'un formulaire
12) Création d'un message flash
13) Validation serveur
14) Charger bootstrap
15) Création header (et footer)


1) - symfony new panterest --full


2) - symfony serve -d


3) - symfony console make:controller pins


4) - symfony console make:entity Pin
    title
    string
    255
    no
    description
    text
    no
    Enter
    (Ne pas oublier les champs createdAt et UpdatedAt pour ne pas avoir à les ajouter plus tard (voir plus bas))
    Dans le fichier 'src/Entity/Pin.php', ajouter l'annotation suivante : '@ORM\Table(name="pins")' pour nommer la table 'pins'


5) Ouvrir MAMP.
   Ouvrir phpmyadmin : 'localhost:8888/phpmyadmin'.
   'Comptes utilisateurs' -> 'Nouvel utilisateur' -> 'Ajouter un compte utilisateur'.
        -> Nom d'utilisateur : panterest_dev
        -> Nom d'hôte : (tout hôte) %
        -> Mot de passe : panterest_dev
        -> Saisir à nouveau : pinterest_clone_dev
   'Base de données pour ce compte d'utilisateur' -> cocher 'Créer une base portant son nom et donner à cet utilisateur tous les privilèges sur cette base'.
   'Privilèges globaux' -> 'Tout cocher'
   'Exécuter'
   Créer un fichier '.env.local' et y mettre :
        DATABASE_URL=mysql://...   (reprendre ce qu'il y a dans le fichier '.env')
   Ce fichier '.env.local' prend le dessus sur le fichier '.env' et il n'est pas commité sur github.
   - symfony console make:migration
   - symfony console doctrine:migrations:migrate
   La table 'pins' est créée dans la base de données 'panterest_dev'.


6) - composer req theofidry/psysh-bundle --dev
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


7) - symfony console make:twig-extension
    AppExtension (crée 'src/Twig/AppExtension.php')


8) Créer le fichier 'php.ini'
Fermer le serveur (symfony server:stop) et le relancer (symfony serve -d)


9) - symfony console make:entity
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
  Pour pouvoir mettre un titre vide, mettre le point d'interrogation :
  'Pin.php' : public function setTitle(?string $title): self


10) Dans le fichier 'PinsController.php', modifier : '$pins = $pinRepository->findBy([], ['createdAt' => 'DESC']);'


11) - symfony console make:form
    PinType
    Pin


12) Dans 'PinsController.php', ajouter '$this->addFlash('success', 'Pin successfully created !');'
et ajouter dans le fichier twig (ici on a mis cela dans un fichier '_flash_messages.html.twig' et on l'a inclus dans le fichier 'base.html.twig' (include...partials...)):
{% for type, messages in app.flashes %}
    {% for message in messages %}
        <div class="alert alert-{{ type }}" role="alert">
            {{ message }}
        </div>
    {% endfor %}
{% endfor %}
Ajouter également le style dans 'base.html.twig'


13) 'Pin.php' : use Symfony\Component\Validator\Constraints as Assert;
    Dans les champs voulus :
    * @Assert\NotBlank
    * @Assert\Length(min=3, minMessage="Le titre doit contenir au moins 3 caractères")


14) 'base.html.twig' : dans le block stylesheets, coller le lien <link> de bootstrap,
dans le block javascripts, coller les 3 scripts de bootstrap
Pour avoir un theme bootstrap pour les formulaires :
'config/packages/twig.yaml' : form_themes: ['bootstrap_4_layout.html.twig']


15) On crée un fichier '_nav.html.twig' dans le dossier 'templates/layouts/partials'
On inclut le fichier dans 'base.html.twig' avec '{{ include('layouts/partials/_nav.html.twig') }}'