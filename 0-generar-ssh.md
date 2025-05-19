## Creamos llaves

```shell
ssh-keygen -t rsa -b 4096 -C “tu_email@gmail.com”
```

- Enter file in which to save the key (/c/Users/miguel/.ssh/id_rsa):
- Enter passphrase (empty for no passphrase):
- Enter same passphrase again:

- Your identifaction has been saved in /c/Users/miguel/.ssh/id_rsa.
- Your public key has been saved in /c/Users/miguel/.ssh/id_rsa.pub.


## Para comprobarlo escribimos el comando:

```shell
ls -al ~/.ssh
```
## Evaluar el servidor ssh ver si esta funcionando

```shell
eval $(ssh-agent -s)
```

## Agregar la llave (privada al servidor ssh)

```shell
ssh-add ~/.ssh/id_rsa
```
## Copiar tu llave publica puedes usar cat

```shell
cat ~/.ssh/id_rsa.pub
```


