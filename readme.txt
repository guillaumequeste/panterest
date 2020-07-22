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