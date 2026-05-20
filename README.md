# lab2-securit-
----Ce projet met en place un environnement Android contrôlé (émulateur Pixel 5 API 33, userdebug) pour étudier le root, le Verified Boot et l’impact des privilèges élevés sur une application de test.
Les objectifs sont : activer le root via ADB, vérifier l’intégrité du démarrage, observer le comportement de l’application sur un appareil rooté, collecter des preuves techniques, documenter les risques et nettoyer l’environnement.

Les résultats montrent que l’émulateur accepte adb root (passage en uid=0). Les propriétés ro.boot.verifiedbootstate, ro.boot.veritymode et ro.boot.vbmeta.device_state confirment le statut d’intégrité (verity en mode « enforcing »). L’application app-debug.apk s’installe et s’exécute normalement. Des tests simples (écran d’accueil, recherche, paramètres réseau) valident l’interaction.

Les risques identifiés incluent la perte d’intégrité, l’exposition des données, l’instabilité, l’augmentation de la surface d’attaque, les fuites réseau, la persistance des modifications, des erreurs d’interprétation et un manque de traçabilité.
Les mesures défensives reposent sur un environnement isolé, des données fictives, des traces (logs, captures), une remise à zéro après test et une documentation rigoureuse.
Enfin, le laboratoire respecte certaines références OWASP MASVS (STORAGE-1, NETWORK-1) et propose des tests pratiques (inspection des shared preferences, analyse des logs). La procédure de nettoyage utilise adb emu avd wipe-data.

             Réorganisation en nouveaux paragraphes
Mise en place et activation du root
L’expérience débute avec un émulateur Android Pixel 5 (API 33) en version userdebug. La détection du périphérique via adb devices affiche emulator-5554 device. L’activation des privilèges root s’effectue par la commande adb root, ce qui redémarre le démon ADB avec les droits superutilisateur. La vérification avec adb shell id confirme l’identité uid=0(root) gid=0(root), prouvant que l’émulateur autorise l’exécution de commandes système normalement protégées. Contrairement à un appareil physique, le binaire su n’est pas nécessaire ici car le shell tourne déjà en root.

               Intégrité du démarrage et vérifications AVB
Le Verified Boot (Android Verified Boot – AVB) est analysé grâce à plusieurs propriétés système. ro.boot.verifiedbootstate indique l’état d’intégrité de la chaîne de confiance ; ro.boot.veritymode montre que dm-verity est en mode « enforcing », ce qui bloque toute altération des partitions système normalement. ro.boot.vbmeta.device_state renseigne sur l’état des métadonnées AVB. Ces commandes permettent de comprendre comment Android garantit que seuls des logiciels de confiance s’exécutent au démarrage, et comment le root sur un émulateur userdebug contourne partiellement ces protections en environnement de test.

            Installation et test de l’application
L’APK de débogage (app-debug.apk) est installé avec adb install. L’application se lance correctement sur l’écran d’accueil. Trois scénarios simples sont exécutés : ouverture de l’écran principal, saisie d’une recherche (« test ») pour valider l’interaction, et ouverture des paramètres réseau Android pour vérifier la connectivité. Aucun comportement anormal n’est observé ; l’application fonctionne sur l’émulateur rooté sans crash ni refus de service.

           Risques de sécurité et mesures compensatoires
Le laboratoire identifie huit risques majeurs : perte d’intégrité des résultats (biais possibles), exposition des données privées par l’accès root, instabilité de l’émulateur, augmentation de la surface d’attaque, fuites de trafic réseau hors de l’environnement isolé, persistance des modifications même après redémarrage, mauvaises conclusions dues à une mauvaise configuration, et manque de traçabilité des opérations. Pour y remédier, l’environnement est isolé (dédié à l’émulateur), les données sont fictives, l’APK est contrôlé et fiable, les tests restent locaux, des captures d’écran et logs (adb logcat -d > logcat_root_check.txt) sont conservés, et une procédure de remise à zéro (adb emu avd wipe-data) rétablit un état propre après chaque session.

          Références et nettoyage
Deux exigences OWASP MASVS sont citées : STORAGE-1 (stockage sécurisé des informations sensibles) et NETWORK-1 (transport TLS pour toutes les communications réseau). Des idées de tests MASTG sont proposées : inspecter les shared preferences à la recherche de données en clair, et analyser les logs runtime. L’ensemble des preuves (sorties ADB, statuts root, propriétés AVB, logs d’installation, captures d’écran, fichier logcat_root_check.txt) est organisé dans une arborescence lab2/ avec un dossier images/. La procédure finale de nettoyage garantit la réutilisation de l’environnement sans contamination d’un test à l’autre.

