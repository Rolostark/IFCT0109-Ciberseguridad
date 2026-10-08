---
title: "PoC - Hashcat"
aliases:
  - PoC - Hashcat
tags:
  - ifct0109/pocs
  - auditoria
  - hashcat
---

# PoC – Hashcat

El descifrado de contraseñas o ataques por fuerza bruta offline es una forma efectiva de obtener acceso a recursos no autorizados.

Son muchas las aplicaciones y sistemas hacen uso de algoritmos criptográficos para hash o cifrar datos.

## Hashing

El hashing es un proceso unidireccional. La única forma de atacarlo es usar una lista que contenga posibles contraseñas donde cada palabra se hashea y se compara con el hash original.

**Ejercicio:** Calcula el hash MD5 para la siguiente clave: `p@ssw0rd`

**comando:** `$ echo -n 'p@ssw0rd' | md5sum`

```bash
Resultado: 0f359740bd1cda994f8b55330c86d845
```

Un medio de proteger diccionarios es utilizar una “sal” (salt), que se añade a la clave original.

**Ejercicio:** Calcula el hash MD5 de: `p@ssw0rd123456`

**comando:** `$ echo -n 'p@ssw0rd123456' | md5sum`

```bash
Resultado: f64c413ca36f5cfe643ddbec4f7d92d0
```

Formatos comunes de almacenamiento de hashes:

* `hash:salt`

* `$id$salt$hash`

## Identificando Hashes

Es fundamental conocer el tipo de hash para utilizar correctamente herramientas como Hashcat.

Existen varias formas de identificar hashes:

* Hashes.com Identifier

* Aplicaciones de terminal como `hashid` o `hash-identifier`:

```bash
$ hashid 'hash'
$ hash-identifier
	Dentro pegamos el hash
```

**Ejercicio:** Identifica el tipo del siguiente hash:

```bash
$apr1$71850310$gh9m4xcAn3MGxogwX/ztb.
```

Respuesta: **Apache MD5**

## Comenzando con Hashcat

### Instalación

```bash
$ sudo apt install hashcat
```

### Comandos básicos

* `$ hashcat -h` – Muestra toda la ayuda.

* `$ hashcat --example-hashes | less` – Muestra ejemplos de hashes y formatos.

* `$ hashcat -b -m 0` – Prueba de rendimiento (MD5 en este caso).

**Ejercicio:** Realiza una prueba de rendimiento de tu ordenador.

**comando:** `$ hashcat -b -m 0`

**Ejercicio:** Busca el modo que habría que usar para desencriptar un hash tipo Cisco-ASA MD5:

**comando:** `$ hashcat -h | grep Cisco-ASA`

```bash
Resultado: 2410 | Cisco-ASA MD5
```

## Ataque por diccionario

Antes de usar Hashcat, puedes probar en recursos online como:

* CrackStation

* Hashes.com Decrypt

### Modos de ataque (`-a`)

El modo de ataque se selecciona con el parámetro `-a`

```bash
0 | Straight (diccionario)
1 | Combination
3 | Brute-force
6 | Hybrid Wordlist + Mask
7 | Hybrid Mask + Wordlist
9 | Association
```

Vamos a empezar realizando un ataque por diccionario, por lo que el modo que tenemos que seleccionar es el "0"

### Ejemplo:

```bash
$ echo -n p@ssw0rd | sha256sum | cut -d ' ' -f 1 > ejemplo_sha256
$ hashcat -a 0 -m 1400 ejemplo_sha256 rockyou.txt
```

**Ejercicio:** Desencripta el siguiente hash: "0c352d5b2f45217c57bef9f8452ce376"

**comando:** `$ hashcat -a 0 -m 0 "0c352d5b2f45217c57bef9f8452ce376" rockyou.txt`

```bash
Resultado: 0c352d5b2f45217c57bef9f8452ce376:cricket1
```

## Ataque por máscara

Ahora vamos a realizar un ataque por máscara `-a 3` que se usa cuando queremos que se use un patrón determinado `-a`

Usamos patrones de formato conocidos para generar palabras objetivo.

| Símbolo | Significado | Ejemplos de valores |
| --- | --- | --- |
| `?l` | Letra minúscula | a–z |
| `?u` | Letra mayúscula | A–Z |
| `?d` | Dígito | 0–9 |
| `?s` | Símbolos especiales | ! @ # $ % & … |
| `?a` | Todos los anteriores (`?l?u?d?s`) | Todo el alfabeto, números y símbolos |
| `?b` | Todos los caracteres ASCII (byte) | 0x00–0xff |
| `?1 – ?4` | Definidos por el usuario con `-1`, `-2`, etc. | Personalizados. Ej: `-1 ABC123` |

Hashcat permite definir hasta cuatro conjuntos personalizados con los flags -1, -2, -3 y -4.

Luego puedes usar ?1, ?2, ?3 y ?4 en tu máscara para referenciar esos conjuntos.

Ejemplo:

```bash
hashcat -a 3 -m 0 hash.txt -1 abc -2 123 '!pass?1?2'
```

Sintaxis:

```bash
$ hashcat -a 3 -m 0 <hash> -1 012 'HASHCAT?l?l?l?l?l20?1?d'
```

**Ejercicio:** Desencripta el siguiente hash MD5 50a742905949102c961929823a2e8ca0

Utilizando la siguiente máscara:  -1 012 'HASHCAT?l?l?l?l?l20?1?d'

**comando:** `$ hashcat -a 3 -m 0 "50a742905949102c961929823a2e8ca0" -1 012 'HASHCAT?l?l?l?l?l20?1?d'`

```bash
Resultado: 50a742905949102c961929823a2e8ca0:HASHCATqrstu2020
```

**¿Qué significa `-1`?** Define un conjunto de caracteres personalizados (0,1,2).

Más info sobre máscaras: [Mask Attack Wiki](https://hashcat.net/wiki/doku.php?id=mask_attack)

Finalmente, prueba a desencriptar este hash

**comando:** `$ hashcat -a 3 -m 0 "8be5b001de41302581001ffebcaeebb2" '?d?d?d?d?d?d?d?d?u'`

## Ataque híbrido

Combinación entre diccionario y máscara.

Ejemplos:

**comando:** `$ hashcat -a 6 -m 0 "f7a4a94ff3a722bf500d60805e16b604" rockyou.txt '?d?s'`

```bash
Resultado: football1$
```

**comando:** `$ hashcat -a 7 -m 0 "eac4fe196339e1b511278911cb77d453" -1 01 '20?1?d' rockyou.txt`

```bash
Resultado: 2015football
```

## Reglas en Hashcat

Permiten modificar dinámicamente los diccionarios para probar variantes de palabras.

Ver reglas existentes:

```bash
$ ls -l /usr/share/hashcat/rules/
```

Crear archivo:

```bash
$ echo 'so0 si1 se3 ss5 sa@ c $2 $0 $1 $9' > rule.txt
```

Ejecutar:

**comando:** `$ hashcat -a 0 -m 100 "08004e35561328e357e34d07c53c7e4f41944e28" /usr/share/wordlists/rockyou.txt -r rule.txt`

```bash
Resultado: St@r5h1p2019
```

Guía avanzada:
    [OneRuleToRuleThemAll](https://github.com/NotSoSecure/password_cracking_rules/blob/master/OneRuleToRuleThemAll.rule)

## John the Ripper

Comandos útiles:

```bash
$ john --list=formats
$ john --list=formats | grep "MD5"
$ john ejemplo_md5 --format=Raw-MD5 --wordlist=/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt
```
