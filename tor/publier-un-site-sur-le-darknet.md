# Publier un site sur le darknet

```
apt install tor
apt install apache2


```

Accéder au fichier vi `/etc/tor/torrc` et Décommenter les lignes :&#x20;

```
vi /etc/tor/torrc

HiddenServiceDir
HiddenServicePort

service tor restart
```

Modifier la page d'accueil du site :

```
vi /var/www/html/index.html

service apache2 restart
```

Activer l'écoute sur le port Tor&#x20;

```
sudo -u debian-tor tor
lsof -i :9050
```

Récupérer le lien en .onion

```
/var/lib/tor/hidden_service/hostname

5keaal....onion
```

