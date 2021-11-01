# Búsquedas sobre el indice

En esta parte de igual manera consulte los videos de el canal de Youtube de Elastic. El video que más me ayudo en esta ocasión fue [*"Relevance of a search"*](https://youtu.be/CCTgroOcyfM) donde aprendí las query's basicas para la busqueda.

## Paso 1
En este paso se pide encontrar los registros que en el atributo *estado_consulta* contengan el valor "error" o "consumo".

Cabe recalcar que existen multiples formas de obtener el resultado, una de ellas podria ser mediante una busqueda multiple. Sin embargo, decidí utilizar una expresion regular para la resolución de este paso con el siguiente comando.

~~~
GET log_consultas/_count
{
  "query": {
    "regexp": {
      "estado_consulta": "(consumo)?(error)?"
    }
  }
}
~~~

## Paso 2
Para este siguiente paso utilize la función "match" para hacer coincidir exactamente el valor buscado, dando como resultado el siguiente comando.
~~~
GET log_consultas/_count
{
  "query": {
    "match": {
      "administrador": "Juan Lara"
    }
  }
}
~~~

## Paso 3
En este paso hice uso de la función must, que admite varias coincidencias de las misma query, dandome como resultado lo siguiente.
~~~
GET log_consultas/_count
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "estado_consulta": "informativo"
          }
        },
        {
          "match": {
            "servicio": "borrado"
          }
        }
      ]
    }
  }
}
~~~
Como se observa utilize dos funciones "match", cada una para que retornara ambos campos según su coincidencia.
