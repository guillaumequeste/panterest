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
14) Charger bootstrap (sans webpack encore)
15) Création header (et footer)
16) Installer Webpack Encore
17) Upload files
18) Effet zoom
19) Création de la table 'users'
20) Création d'utilisateurs via le terminal
21) Relation table 'users' et table 'pins'
22) Nouvelle création d'utilisateurs et de pins via le terminal
    (+ PRATIQUE de créer directement dans phpmyadmin, vérifier à bien mettre default->current_timestamp pour 'created_at' et 'updated_at' et remplir 'user-id', pour l'instant on laisse le role vide (null))
23) Création d'un getter pourr récupérer le prénom et le nom de l'utilisateur
24) Boutons visibles dans la barre de navigation
25) Authentification
26) Message flash de connexion
27) Bloquer l'accès à la page login lorsque l'on est connecté
28) Se déconnecter
29) Rester connecté même après la fermeture du navigateur


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

  Autre :
    - symfony console make:entity Pin
        imageName
        string
        255
        yes
        enter
        symfony console make:migration
        dans le nouveau fichier de migration :
            public function getDescription() : string
            {
                return 'Add image_name field to pins table';
            }
        symfony console doctrine:migrations:migrate
        yes


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


16) - composer req Encore
    - yarn
    Dans le dossier 'assets/css', on crée un fichier 'style.css'
    Dans le fichier 'assets/js/app.js', on importe également le fichier 'style.css' : import '../css/style.css';
    - yarn dev ou - yarn watch   (dev: quitte à la fin, watch: compile en temps réel)
    -> il crée un fichier 'public/build/app.css' qui prend en compte 'app.css' et 'style.css'
    (dans le fichier 'base.html.twig' : <link rel="stylesheet" href="{{ asset('build/app.css') }}">)
    Pour utiliser SASS, dans le fichier 'webpack.config.js', activer .enableSassLoader() (enlever les 2 //)
    - yarn dev  -> error, on nous dit d 'installer yarn add sass...
    Renommer les fichier 'app.scss' et 'style.scss' et dans le fichier 'assets/js/app.js', renommer en scss
    - yarn dev   (nouveu fichier css créé)
    Pour installer bootstrap :
    - yarn add bootstrap --dev
    et importer dans le fichier 'app.scss' ou 'style.scss' : @import "~bootstrap/scss/bootstrap";
    - yarn add jquery popper.js --dev
    et importer jquery et bootstrap dans le fichier 'assets/js/app.js' :
        import $ from 'jquery';
        import 'bootstrap';


17) on utilise un bundle :
    - composer require vich/uploader-bundle
      y
    Dans le fichier 'config/packages/vich_uploader.yaml' :
        vich_uploader:
        db_driver: orm

        mappings:
            pin_image:
                uri_prefix: /uploads/pins
                upload_destination: '%kernel.project_dir%/public/uploads/pins'
                namer: Vich\UploaderBundle\Naming\UniqidNamer
    Dans le fichier 'Pin.php', on ajoute :
        use Symfony\Component\HttpFoundation\File\File;
        use Vich\UploaderBundle\Mapping\Annotation as Vich;

        * @Vich\Uploadable

        /**
        * NOTE: This is not a mapped field of entity metadata, just a simple property.
        * 
        * @Vich\UploadableField(mapping="pin_image", fileNameProperty="imageName")
        * @Assert\Image(maxSize="8M")
        * 
        * @var File|null
        */
        private $imageFile;

        /**
        * If manually uploading a file (i.e. not using Symfony Form) ensure an instance
        * of 'UploadedFile' is injected into this setter to trigger the update. If this
        * bundle's configuration parameter 'inject_on_load' is set to 'true' this setter
        * must be able to accept an instance of 'File' as the bundle will inject one here
        * during Doctrine hydration.
        *
        * @param File|\Symfony\Component\HttpFoundation\File\UploadedFile|null $imageFile
        */
        public function setImageFile(?File $imageFile = null): void
        {
            $this->imageFile = $imageFile;

            if (null !== $imageFile) {
                // It is required that at least one field changes if you are using doctrine
                // otherwise the event listeners won't be called and the file is lost
                $this->updatedAt = new \DateTimeImmutable();
            }
        }

        public function getImageFile(): ?File
        {
            return $this->imageFile;
        }

        Installer le bundle liip_imagine :
        - composer require liip/imagine-bundle
            y
        
        Dans le fichier 'config/packages/liip_imagine.yaml' :
            # See dos how to configure the bundle: https://symfony.com/doc/current/bundles/LiipImagineBundle/basic-usage.html
            liip_imagine:
                # valid drivers options include "gd" or "gmagick" or "imagick"
                driver: "gd"

                filter_sets:
                    squared_thumbnail_medium:
                        filters:
                            thumbnail:
                                size: [300, 300]
                                mode: outbound
                                allow_upscale: true
                
                    squared_thumbnail_small:
                        filters:
                            thumbnail:
                                size: [200, 200]
                                mode: outbound
                                allow_upscale: true

        Dans le fichier 'Form/PinType.php', ajouter :
            use Vich\UploaderBundle\Form\Type\VichImageType;

            ->add('imageFile', VichImageType::class, [
                'label' => 'Image (JPG, JPEG or PNG file)',
                'required' => false,
                'allow_delete' => true,
                'download_uri' => false,
                'imagine_pattern' => 'squared_thumbnail_small'
            ])
        
        Dans le fichier '.gitignore', ajouter :
            ###> custom end vars ###
            /public/uploads/
            ###< custom end vars ###

        Dans le fichier 'assets/js/app.js', ajouter (pour avoir le nom du fichier dans le champ input):
            $('.custom-file-input').on('change', function(e) {
                var inputFile = e.currentTarget;
                $(inputFile).parent().find('.custom-file-label').html(inputFile.files[0].name);
            })
        Dans le fichier 'templates/pins/index.html.twig', ajouter :
            <img src="/uploads/pins/{{ pin.imageName }}"/>


18) div       -> class="mw-100 overflow-hidden"
        img   -> transition: transform 0.3s ease-out;
        img:hover ->transform: scale(1.075);


19) - symfony console make:user
    user
    yes
    email
    yes
    enter

    symfony console make:entity user
    firstName
    string
    255
    no
    lastName
    string
    255
    no
    enter

    Réorganiser le fichier 'src/Entity/User.php' comme l'on veut (ordre que l'on souhaite)
    Dans le fichier 'src/Entity/User.php', ajouter :
        * @ORM\Table(name="users")
        * @ORM\HasLifecycleCallbacks
        la fonction updateTimeStamps() et 'options={"default": "CURRENT_TIMESTAMP"}' pour createdAt et updatedAt
    
    symfony console make:entity user
    createdAt
    datetime
    no
    updatedAt
    datetime
    no
    enter

    symfony console make:migration

    Dans le fichier de migration, ajouter :
        return 'Create users table';

    symfony console doctrine:migrations:migrate
    yes

    Dans phpmyadmin, dans la structure de la table 'users',
    pour les champs created_at et updated_at, cliquer sur 'Change' à droite
    et choisir 'Default' -> 'CURRENT_TIMESTAMP'


20) - symfony console psysh
    - use App\Entity\User;
    - $user1 = new User;
    - dump($user1)
    - $user1->setFirstName('John')
    - dump($user1)
    - $user1->setLastName('Doe');
    - $user1->setEmail('johndoe@example.com')
    - dump($user1)

     Ouvrir un nouvel onglet dans le terminal :
     - symfony console security:encode-password
     - taper le mot de passe et enter
     - copier le mot de passe encodé

    - $user1->setPassword('$2y$13$4Xm66DzJitnCOVToQf9I0Og32dZpPI4zzMDe75t1UZssV.x9fB8ZK')
    - dump($user1)
    - $em = $container->get('doctrine')->getManager();
    - $em->persist($user1);
    - $em->flush()
    (Ici ne marche pas car le champ created_at ne doit pas être null,
    le faire via phpmyadmin, on est obligé de mettre eun rôle [1])


21) On veut lier la table 'users' et la table 'pins'.
    Chaque utilisateur peut créer plusieurs pins et chaque pin est lié à un seul utilisateur.
    - symfony console make:entity Pin  (on aurait aussi pu partir de l'entité User)
    - user
    - relation
    - User
    - ManyToOne
    - no
    - yes
    - pins
    - yes
    - enter
    Vider la base de données pour ne pas avoir d'erreur lors de la migration
    (car on a dit que chaque pin doit être lié à un utilisateur, chose que l'on n' a pas pu faire jusqu'à présent)
    - symfony console make:migration
    - symfony console doctrine:migrations:migrate


22) - symfony console psysh
    - use App\Entity\{User, Pin};
    - $u1 = new User;
    - $u1->setFirstName('John')
    - $u1->setLastName('Doe')
    Nouvel onglet :
    - symfony console security:encode-password
    - secret
    - $u1->setPassword('$2y$13$eGVU36lxtppqt2EMhnhX2uYNb4EcD.5vxhq/JSqU7MpQ26T.4tbny');
    - dump($u1)
    - $u1->setEmail('johndoe@example.com')
    - $em = $container->get('doctrine')->getManager();
    - $em->persist($u1)
    - $em->flush()
    etc...


23) Dans le fichier 'src/Entity/User.php', ajouter :
        public function getFullName(): ?string
        {
            return $this->getFirstName() . ' ' . $this->getLastName();
        }
    Dans le template voulu, mettre :
        {{ pin.user.fullName }}


24) Dans le fichier 'src/templates/layouts/partials/_nav.html.twig', mettre :
    {% if app.user %}
    ...
    {% else %}
    ...
    {% endif %}


25) - symfony console make:auth
    - 1   (Login form authenticator)
    - LoginFormAuthenticator
    - SecurityController
    - yes
    Les fichiers suivants sont créés :
        - src/Security/LoginFormAuthenticator.php
        - templates/security/login.html.twig   (à modifier si besoin)


26) Fonctionne avec le 12)
    Dans le fichier 'LoginFormAuthenticator.php', ajouter :
    $request->getSession()->getFlashBag()->add('success', 'Logged in successfully!');


27) Dans le fichier 'src/Controller/SecurieyController.php', décommenter :
    if ($this->getUser()) {
        $this->addFlash('error', 'Already loggedin!');
        return $this->redirectToRoute('app_home');
    }


28) Dans le fichier 'config/packages/security.yaml' :
    logout:
        path: app_logout
        target: app_home


29) Dans le fichier 'config/packages/security.yaml', ajouter :
    remember_me:
        secret: '%kernel.secret%'
    Dans le fichier 'templates/security/login.html.twig', décommenter :
    <div class="checkbox mb-3">
        <label>
            <input type="checkbox" name="_remember_me"> Remember me
        </label>
    </div>