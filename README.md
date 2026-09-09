# Anclaje temporal — recargos por combustible de transportistas españoles

Este repositorio publica **únicamente huellas criptográficas** de un archivo
privado que captura, cada día, cómo cambian los recargos por combustible que
publican los transportistas que operan en España (SEUR, Correos Express, MRW,
GLS, DHL, UPS, FedEx, TNT) y los índices oficiales sobre los que dicen
calcularlos (Boletín Petrolero de la UE, EIA, MITECO).

## Por qué existe

El archivo guarda las páginas por su `sha256`, así que es fácil demostrar que
un documento **no ha cambiado** desde que se guardó. Lo que no se puede
demostrar por uno mismo es **cuándo** se guardó: esa fecha la escribe quien
captura.

Un commit público lo resuelve. La fecha en la que GitHub recibe el push no la
pone quien empuja.

## Qué prueba, exactamente

> La fecha del commit que contiene un fichero fija un **límite superior**: esos
> registros, con esos `sha256`, ya existían entonces. No se han fabricado ni
> retocado después.

Y qué **no** prueba:

- No prueba que el contenido capturado diga la verdad, ni que la página fuera
  la que se afirma. Para eso están las capturas del Internet Archive, fechadas
  por un tercero independiente: los registros con `"via": "wayback"` las tienen.
- El **nombre del fichero** es la fecha de la *observación*, que por sí sola no
  está probada. Lo probado es la fecha del commit.

## Qué se publica

Por cada observación: el identificador de la fuente, su URL pública, la marca de
tiempo, la vía por la que se obtuvo, el `sha256` del documento, su tamaño y si
fue correcta. Y una raíz: el `sha256` de la serialización canónica del conjunto.

**No se publica el contenido capturado.** Son páginas de terceros y no hacen
falta para anclar: basta su huella. Tampoco se publica nada sobre la
infraestructura usada.

## Cómo verificar

Recalcular la raíz de cualquier día:

```bash
python3 - <<'PY'
import hashlib, json, sys
d = json.load(open('dias/2026-09-09.json'))
canon = json.dumps(d['registros'], ensure_ascii=False,
                   sort_keys=True, separators=(',', ':')).encode()
print(hashlib.sha256(canon).hexdigest())
print(d['raiz_sha256'])
PY
```

Y comprobar la fecha del anclaje:

```bash
git log --format='%H %cI %s' -- dias/2026-09-09.json
```

Quien tenga el documento original puede comprobar que su `sha256` figura aquí,
y desde qué commit.

## Sobre el hueco de 2016 a 2026

Los ficheros con fecha anterior a septiembre de 2026 corresponden a capturas
recuperadas del Internet Archive, no a observaciones propias. Su fecha real la
certifica el Archive; aquí solo consta que nuestra copia no se fabricó después
del commit que la incluye.
