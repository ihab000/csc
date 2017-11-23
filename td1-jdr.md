# TD 1 : Usage de la cryptographie asymétrique

François Lesueur (francois.lesueur@insa-lyon.fr, @FLesueur)

Ce TD présente et applique les notions de cryptographie asymétrique :
* Génération de clés RSA
* Distribution de clés 
* Signature et chiffrement RSA

Vous pourrez par exemple utiliser python pour calculer les exponentiations modulaires. Lancez python, puis dans l'interpréteur tapez `pow(a,b,c)` pour obtenir a<sup>b</sup>[c]. Vous pouvez également utiliser [Wolfram Alpha](http://www.wolframalpha.com).

Définition du cryptosystème
===========================

Le cryptosystème que nous allons utiliser ici est basé sur la fonction RSA. Le cryptosystème proposé est simple et présente donc certaines vulnérabilités mais illustre le fonctionnement. Cette partie définit le cryptosystème, il n'y a rien à faire ici.

Génération de clés RSA
----------------------

Voici l'algorithme simplifié de génération de clés RSA (en réalité, d'autres tests doivent être réalisés) :
* Choisir deux nombres premiers _p_ et _q_ (liste un peu plus loin)
* Calculer _n = p * q_
* Calculer _&phi;(n) = (p-1)(q-1)_
* Choisir _e_ tel que :
	* _1 < e < &phi;(n)_
	* _pgcd(e, &phi;(n)) = 1_
	* Par exemple, un premier qui ne divise pas &phi;(n)
* Déterminer _d &equiv; e<sup>-1</sup> mod &phi;(n)_

L'exemple est réalisé avec p=31, q=37, n=1147, &phi;(n)=1080, e=7, d=463.

Code Python pour calculer _a<sup>-1</sup> mod b_ : `modinv(a,b)` disponible [ici](modinv.py)


La clé publique est _(e,n)_, ici _(7,1147)_, et la clé privée est _(d,n)_, ici _(463,1147)_. 
La propriété utilisée est que pour tout message _m, m<sup>de</sup>[n] = m_.

Chiffrement et déchiffrement
----------------------------

Nous allons chiffrer des chaînes de caractères. Pour cela, chaque lettre est remplacée par son rang dans l'alphabet, sur 2 chiffres :


|a|b|c|d|e|f|g|h|i|j|k|l|m|n|o|p|q|r|s|t|u|v|w|x|y|z|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|01|02|03|04|05|06|07|08|09|10|11|12|13|14|15|16|17|18|19|20|21|22|23|24|25|26|27|

Par exemple, "crypto" devient `03 18 25 16 20 15`

Ensuite, afin de ne pas retomber dans un chiffrement par substitution simple, les chiffres sont assemblés par blocs de 3 (complété éventuellement de 0 à la fin), ainsi `03 18 25 16 20 15` devient `031 825 162 015`.

Enfin, chaque bloc clair de 3 chiffres est chiffré indépendamment par la fonction RSA : bloc<sub>chiffré</sub> = bloc<sub>clair</sub><sup>e</sup>[n]. Attention, _(e,n)_ représente une clé publique, mais celle de qui ? L'utilisation de la clé _(7,1147)_ donne le chiffré `1116 751 245 1108`.

> Attention, lors de l'appel à la fonction `pow(a,b,c)` de python, n'écrivez pas de '0' en début d'entier. Par exemple, pour le bloc clair `031`, tapez `pow(31,7,1147)`. Commencer un entier par '0' le fait interpréter comme un nombre encodé en _octal_ (même principe qu'un nombre commençant par '0x' qui est interprété comme un hexadécimal).


Le déchiffrement est opéré de manière symétrique, en utilisant la clé privée au lieu de la clé publique. Chaque bloc clair est réobtenu à partir du bloc chiffré par le calcul : bloc<sub>clair</sub> = bloc<sub>chiffré</sub><sup>d</sup>[n]

Signature et vérification
-------------------------

Nous allons signer des chaînes de caractères. Pour cela, chaque lettre est remplacée par son rang dans l'alphabet. Pour un message _m = (m<sub>0</sub>, ..., m<sub>i</sub>)_ avec _(m<sub>0</sub>, ..., m<sub>i</sub>)_ les rangs de chaque lettre (attention, on ne fait plus des blocs de 3 chiffres ici), le haché _h(m)_ est calculé par l'algorithme suivant :

	h = 2;
	for (j=0; j<i; j++) {
		h = h * 2;
		h = h + m[j];
	}
	return h%1000;

La valeur de la signature vaut alors _h(m)<sup>d</sup>[n]_. Attention, _(d,n)_ représente une clé privée, mais celle de qui ? Le haché de "crypto" vaut par exemple 831 et la signature par _(463,1147)_ est 335.

Le message est alors envoyé accompagné de sa signature. La vérification d'un message reçu _m_ signé avec _sig_ est opérée de la manière suivante :
* Calculer _h(m)_ par rapport au _m_ reçu
* Calculer _sig<sup>e</sup>[n]_
* Vérifier que _h(m) == sig<sup>e</sup>[n]_ sur le message reçu


Génération des clés
===================

Nous allons commencer par générer une paire de clés RSA pour chacun. Utilisez pour cela l'algorithme présenté précédemment. Gardez votre clé privée secrète et transmettez votre clé publique avec votre nom à l'enseignant, sur un papier. Elle sera inscrite au tableau (la "PKI"). 

Pour calculer l'inverse modulaire (_e<sup>-1</sup> mod &phi;(n)_), vous pouvez utiliser [Wolfram Alpha](http://www.wolframalpha.com).

Une petite liste de nombres premiers pour gagner du temps :

	31 , 37 , 41 , 43 , 47 ,53 ,59 ,61 ,67 ,71 ,73 ,79 ,83 ,89 ,97 ,101 ,103 ,107
	109 ,113 ,127 ,131 ,137 ,139 ,149 ,151 ,157 ,163 ,167 ,173 ,179 ,181 ,191 ,193 ,197 ,199
	211 ,223 ,227 ,229 ,233 ,239 ,241 ,251 ,257 ,263 ,269 ,271 ,277 ,281 ,283 ,293 ,307 ,311
	313 ,317 ,331 ,337 ,347 ,349 ,353 ,359 ,367 ,373 ,379 ,383 ,389 ,397 ,401 ,409 ,419 ,421


<!--

% section génération_des_clés (end)

\section{Échange de messages chiffrés} % (fold)
\label{sec:Echange_de_messages_chiffres}

Vous allez maintenant transmettre un message chiffré à un étudiant éloigné par un protocole multi-saut : vous le transmettez à un voisin, qui le redonne à un voisin, \emph{etc.}, jusqu'à sa destination. Vous jouerez à la fois les rôles d'émetteur, de routeur (malicieux ou non) et de récepteur. Le chiffrement assure la \emph{confidentialité} du message transmis.

\begin{enumerate}
	\item \textbf{Envoi de votre message} : Chiffrez un message de votre choix avec le cryptosystème proposé. Inscrivez sur un papier votre identité, le message chiffré et le destinataire. Envoyez-le !
	\item \textbf{Routage des autres messages} : Que fait un routeur ? Il lit un message, l'analyse, décide où l'envoyer puis le reproduit. De manière analogue, vous allez pour chaque saut retransmettre le message entrant mais vous pouvez le lire avant de le retransmettre. Pouvez-vous en déduire des informations ?
	\item \textbf{Réception d'un message} : À la réception d'un message, appliquez l'algorithme de déchiffrement. Quelqu'un d'autre sur la route du message pouvait-il obtenir le clair de ce message ?
\end{enumerate}

% section Échange_de_messages_chiffrés (end)


\section{Échange de messages signés} % (fold)
\label{sec:envoi_d_un_message_signe}

Vous allez maintenant transmettre un message clair signé à un étudiant éloigné par ce même protocole multi-saut. La signature permet de vérifier l'\emph{intégrité} du message transmis.% : vous le transmettez à un voisin, qui le redonne à un voisin, \emph{etc.}, jusqu'à sa destination. Vous jouerez à la fois les rôles d'émetteur, de routeur (malicieux ou non) et de récepteur.

\begin{enumerate}
	\item \textbf{Envoi de votre message} : Signez un message de votre choix avec le cryptosystème proposé. Inscrivez sur un papier votre identité, le message clair, la signature et le destinataire. Envoyez-le !
	\item \textbf{Routage des autres messages} : Utilisez le même protocole multi-saut que précédemment. Pour chaque saut, recopiez le message entrant sur un autre papier puis retransmettez ce second papier.% Si vous avez reçu une carte "H" (Honnête), vous le recopiez tel quel. Si vous avez reçu une carte "M" (Malicieux), vous pouvez le modifier discrètement en le recopiant.
	\item \textbf{Réception d'un message} : À la réception d'un message, appliquez l'algorithme de vérification de la signature. Le message reçu est-il intègre ? Si non, quelle attaque avez-vous détectée ?
\end{enumerate}

% section envoi_d_un_message_signé (end)

\section{Attaques sur le cryptosystème proposé} % (fold)
\label{sec:attaque_sur_le_protocole_mis_en_place}

Étudiez et testez quelques attaques sur le système mis en place :
\begin{itemize}
	\item Modification de message en conservant la validité de la signature
	\item Attaque de la clé privée (par factorisation de $n$ par exemple)
	\item Attaque à message choisi
	\item \ldots
\end{itemize}

Toutes ces attaques sont possibles ici. Réfléchissez à leur cause et aux protections mises en place dans les cryptosystèmes réels. Implémentez une (ou plusieurs) attaque dans le langage de votre choix, proposez une contre-mesure et évaluez la complexité rajoutée par votre contre-mesure.

% section attaque_sur_le_protocole_mis_en_place (end)

\end{document}
-->