# Linux Assignments on Command Line Interface

### 1. Crea mediante comandos de bash la siguiente jerarquía de ficheros y directorios.

```bash
foo/
├─ dummy/
│  ├─ file1.txt
│  ├─ file2.txt
├─ empty/
```

Donde `file1.txt` debe contener el siguiente texto:

**_"Me encanta la bash!!"_**


Y `file2.txt` debe permanecer vacío.

```bash
mkdir -p foo/dummy foo/empty
cd foo/dummy
echo 'Me encanta la bash!!' > file1.txt
touch file2.txt
cat file1.txt # Muestra el contenido del file1
cat file2.txt # Muestra el contenido del file2(debe ser vacío)
```

### 2. Mediante comandos de bash, vuelca el contenido de file1.txt a file2.txt y mueve file2.txt a la carpeta empty.


```
foo/
├─ dummy/
│  ├─ file1.txt
├─ empty/
│  ├─ file2.txt
```

Donde `file1.txt` y `file2.txt` deben contener el siguiente texto:

**_"Me encanta la bash!!"_**


```bash
echo "$(cat file1.txt)" > file2.txt
mv file2.txt ../empty/
cat file1.txt
cat ../empty/file2.txt
```

### 3. Crear un script de bash que agrupe los pasos de los ejercicios anteriores y además permita establecer el texto de file1.txt alimentándose como parámetro al invocarlo.

Si se le pasa un texto vacío al invocar el script, el texto de los ficheros, el texto por defecto será:  

**_"Que me gusta la bash!!!!"_**


1. Creamos el siguiente script `mix-script-ejuno-ejdos.sh`
```bash
#! /bin/bash

CAR="./foo/"
TEXTO='Que me gusta la bash!!!!'

if [ -d "$CAR" ]; then
	  #Si ecsiste el directorio, eliminemoslo
	    rm -r ${CAR}
fi

# $# indica la cantidad de argumentos, 1 es el primero.
if [ $# -gt 1 ]; then
  echo "Error: para este ejercicio solo puedes contar con 1 argumento, disculpe las molestias."
  exit 1
fi

if [[ $# == 1 && "$1" != '' ]]; then
	   TEXT=$1
fi

# Ejercicios 1 y 2
mkdir -p foo/dummy foo/empty
cd foo/dummy
echo $TEXTO > file1.txt
touch file2.txt
echo "$(cat file1.txt)" > file2.txt
mv file2.txt ../empty/
cat file1.txt
cat ../empty/file2.txt
```

_Casos de uso del script_:

   1. Sin parámetros
      ```bash
      ./`mix-script-ejuno-ejdos.sh
      
      Output:
      
      Que me gusta la bash!!!!
      Que me gusta la bash!!!!
      ```


   2. Con un parámetro, pero vacío

        ```bash
        ./simple-bash-script.sh ''
        ```
        Output ->
        ```
        Que me gusta la bash!!!!
        Que me gusta la bash!!!!
        ```

   3. Con un parámetro, con contenido
        ```bash
        ./`mix-script-ejuno-ejdos.sh 'SIUUUUUUUUUUUUUUUU'
        ```
        Output ->
        ```
        SIUUUUUUUUUUUUUUUU
        SIUUUUUUUUUUUUUUUU
        ```
   4. Con dos parámetros, debería lanzar error
        ```bash
        ./`mix-script-ejuno-ejdos.sh 'NOT SO SIUUUUU' 'BOMBONA'
        ```
        Output ->
        ```
        Error: Error: para este ejercicio solo puedes contar con 1 argumento, disculpe las molestias.
        ```

        **Nota: **

        En caso de error ejecutando el script, quizás sea conveniente otorgar permisos de ejecución al mismo(+x):
        ```bash
        chmod +x mix-script-ejuno-ejdos.sh
        ```