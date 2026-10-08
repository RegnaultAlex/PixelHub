1.
2.  C'est le service API qui n'a pas de volume. Celle-ci n'en a pas besoins car elle ne stock pas de donn�es.
    L'api va simplement chercher les donn�es dans les services de bdd

3.  "15432:5432" : 15432 est le port de la machine local, et 5432 est le port du conteneur.
    Ces deux ports vont �tre li�s

4.  C'est acceptable pour un tp comme celui-ci, mais en production c'est dangereux car les
    mots de passe seraient visibles par des gens qui en ont pas forc�ment le droit

5.  C'est des commandes qui seront directement execut�es dans le conteneur :
    La commande redis-cli s'execute directement dans le conteneur redis

6.  Ca fonctionne car les deux port (local et conteneur) sont li�s

7.  Les joueurs ne sont pas dupliqu�s car on regarde avant d'inserer dans la table si il y a des donn�es.
    Sauf que les donn�es ins�r� dans les executions d'avant se sont sauvegard�es grace au volume.

8.  C'est Postgres qui d�fini seul la valeur

9.  Les soldes restent comme ils �taient avant les commandes UPDATE.
    Sans transaction, la base se retrouverait avec des donn�es mise � jour et d'autres pas, ce qui rendrait la base
    incoh�rente.

10. Pendant la transaction de A, B voyant encore les donn�es d'avant BEGIN du terminal A
    L'update du terminal B a du attendre car on ne peux pas avoir plusieur transaction en meme temps
    Si la base ne bloquait pas B on pourrait avoir des donn�es incoh�rentes � la fin

11. C'est la base qui a refus� l'achat de Ombre.
    Le select a �chou� car commande UPDATE a g�n�r� une erreur, et lorsqu'une erreur est g�n�r� dans la transaction
    on ne peux pas lancer d'autres commandes.
    Apr�s le ROLLBACK, la contrainte n'existe plus, on sait que les transactions fonctionnent pour toutes type de commandes

12. TTl Temporaire renvoie le nombre de secondes restante avant l'autodestruction, apr�s les 10 secondes elle renvoit -2 car la cl� est expir�
    GET temporaire renvoie le cl�, apr�s les 10 secondes elle rend (nil)
    Un code d'authentification serait une bonne donn�e

13. C'est plus s�r de faire un GET et d'ajouter dans le code car �a evite les probl�mes de synchronisation de donn�es entre les joueurs.

14. Oui on aurait pu, mais il faut connaitre le sch�ma � l'avance, les jeux aurait des champs qu'ils n'auraient pas besoin, et donc les jeux auraient des champs NULL

15.

SELECT j3.pseudo
FROM Joueurs j1
JOIN Amities a1 ON a1.joueur_id = j1.id
JOIN Amities a2 ON a2.joueur_id = a1.ami_id
JOIN Joueurs j3 ON j3.id = a2.ami_id
WHERE j1.pseudo = 'Ana';
C'est 2 jointures pour l'ami d'un ami d'un ami.

16. DELETE seul est refus� car les noeuds sont encore reli�s par des relations, alors que DETACH DELETE supprime d'abord ces relations, puis les noeuds.

17. La cl� survivant survit au stop (le conteneur n'est pas supprim�) mais dispara�t au down (le conteneur est supprim� et Redis n'a pas de volume), alors que les joueurs PostgreSQL survivent aux deux car leurs donn�es sont dans le volume pg_data, qui n'est pas supprim� par down.
