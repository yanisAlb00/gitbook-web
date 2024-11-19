# Recommandations CSRF et XSS

L'en-tête "Authorization" possède l'avantage qu'elle n'est pas chargée automatiquement par le navigateur du client (protégeant ainsi des attaques CSRF)

L'en-tête "Cookie" possède l'avantage qu'elle peut être associée à un attribut de sécurité "HTTP\_ONLY" qui empêche que le Cookie provienne de Javascript executé (protégaint ainsi des attques XSS). Cookie présente cependant le désavantage d'être écrit automatiquement dans le navigateur du client.&#x20;

Scénario CSRF :&#x20;

* Un attaquant forge une requête HTTP vers laquelle un utilisateur possèdes des cookies&#x20;
* L'attaquant phish la victime en lui envoyant le lien pour qu'il effectue la requête
* La victime s'authentifie automatiquement si l'application utilise l'en-tête Cookie

Protections CSRF : Obliger le client à générer une valeur aléatoire à chaque requête pour s'assurer que la requête soit bien forger par ses soins et qu'un attaquant ne réutilise pas une requête existante

Scénario XSS : &#x20;

* Un attaquant forge une requête HTTP vers un site qui va exécuter du code Javascript sur le navigateur du client et lui envoyer des informations
* &#x20;L'attaquant phish la victime en lui envoyant le lien pour qu'il effectue la requête
* Les informations de la victime sont envoyées à l'attaquant via le Javascript exécuté

Protections XSS : Content-Security-Policy à "none"
