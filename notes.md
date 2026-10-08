1) 
	
2) C'est le service API qui n'a pas de volume. Celle-ci n'en a pas besoins car elle ne stock pas de données.
L'api va simplement chercher les données dans les services de bdd

3) "15432:5432" : 15432 est le port de la machine local, et 5432 est le port du conteneur.
Ces deux ports vont être liés

4) C'est acceptable pour un tp comme celui-ci, mais en production c'est dangereux car les 
mots de passe seraient visibles par des gens qui en ont pas forcément le droit

5) C'est des commandes qui seront directement executées dans le conteneur : 
La commande redis-cli s'execute directement dans le conteneur redis

6) Ca fonctionne car les deux port (local et conteneur) sont liés 

7) Les joueurs ne sont pas dupliqués car on regarde avant d'inserer dans la table si il y a des données.
Sauf que les données inséré dans les executions d'avant se sont sauvegardées grace au volume.

8) C'est Postgres qui défini seul la valeur

9) Les soldes restent comme ils étaient avant les commandes UPDATE.
Sans transaction, la base se retrouverait avec des données mise à jour et d'autres pas, ce qui rendrait la base
incohérente.

10) Pendant la transaction de A, B voyant encore les données d'avant BEGIN du terminal A
L'update du terminal B a du attendre car on ne peux pas avoir plusieur transaction en meme temps
Si la base ne bloquait pas B on pourrait avoir des données incohérentes à la fin


11) C'est la base qui a refusé l'achat de Ombre.
Le select a échoué car commande UPDATE a généré une erreur, et lorsqu'une erreur est généré dans la transaction
on ne peux pas lancer d'autres commandes.
Après le ROLLBACK, la contrainte n'existe plus, on sait que les transactions fonctionnent pour toutes type de commandes



12) TTl Temporaire renvoie le nombre de secondes restante avant l'autodestruction, après les 10 secondes elle renvoit -2 car la clé est expiré
GET temporaire renvoie le clé, après les 10 secondes elle rend (nil)
Un code d'authentification serait une bonne donnée


13) C'est plus sûr de faire un GET et d'ajouter dans le code car ça evite les problèmes de synchronisation de données entre les joueurs.

14) Oui on aurait pu, mais il faut connaitre le schéma à l'avance, les jeux aurait des champs qu'ils n'auraient pas besoin, et donc les jeux auraient des champs NULL
 

15)
 
 SELECT j3.pseudo
FROM Joueurs j1
JOIN Amities a1 ON a1.joueur_id = j1.id
JOIN Amities a2 ON a2.joueur_id = a1.ami_id
JOIN Joueurs j3 ON j3.id = a2.ami_id
WHERE j1.pseudo = 'Ana';
C'est 2 jointures pour l'ami d'un ami d'un ami.
 
 16) 
 DELETE seul est refusé car les noeuds sont encore reliés par des relations, alors que DETACH DELETE supprime d'abord ces relations, puis les noeuds.

 17)
 La clé survivant survit au stop (le conteneur n'est pas supprimé) mais disparaît au down (le conteneur est supprimé et Redis n'a pas de volume), alors que les joueurs PostgreSQL survivent aux deux car leurs données sont dans le volume pg_data, qui n'est pas supprimé par down.