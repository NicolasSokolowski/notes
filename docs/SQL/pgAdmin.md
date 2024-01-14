# pgAdmin

## <ins>Installation</ins>

```
curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /usr/share/keyrings/packages-pgadmin-org.gpg
```

```
sudo sh -c 'echo "deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/$(lsb_release -cs) pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list && apt update'
```

```
sudo apt install pgadmin4-desktop
```

---

Servers > Register (clique-droit)

Onglet Général :  
`Name` : nom du serveur

Onglet Connection :  
`Host name/addres` : localhost  
`Password` : XXXX  
`Save password` : activer pour ne pas avoir à rentrer le mot de passe à chaque connexion

---

Pour lancer des requêtes, il faut sélectionner une database et appuyer sur l'îcone `Query Tool`.