## Configuration

Learning never exhausts the mind.
– Leonardo da Vinci

#### Cambiar el editor por defecto a nano
Puedes configurar nano como tu editor predeterminado de dos formas:
🔹 Temporalmente (solo para la sesión actual)

```shell

export EDITOR=nano

kubectl edit deployment <nombre-del-deployment>

```
#### Permanentemente (para futuras sesiones)
Agrega esta línea al final de tu archivo ``` ~/.bashrc, ~/.zshrc``` o el que uses:

```shell
export EDITOR=nano
```

Después ejecuta:

```shell
source ~/.bashrc  # o ~/.zshrc según tu shell
```

