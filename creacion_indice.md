# Creación de un indice

Para este primer paso tuve varios problemas al no saber como funciona "ElasticSearch", así que procedi a aprender en base a la documentación y los videos de apoyo que se encuentran en su canal de [Youtube](https://www.youtube.com/channel/UC7z5VlhDHnorjUm6oW5dXcw).

Empece por ver el video titulado [*"Introducción a Elasticsearch y Kibana"*](https://youtu.be/gS_nHTWZEJ8) donde en primera instancia se daba una pequeña introducción a elastic y kibana.

Aprendí que elastic funciona por **documentos** y cada documento cuenta con un indice al cual se le pueden agregar datos a almacenar (En un lenguaje SQL podria ser equivalente al concepto de tabla y Base de datos, respectivamente).

## Paso 1

Cree de manera exitosa mi cuenta, con mi correo de Gmail. En este punto no tuve problemas.

## Paso 2

Para este paso tuve algunos problemas al momento de aclarar la sintaxis de como crear un indice, sin embargo, pude resolverlo al checar de nueva cuenta el video introductorio a elastic.

El siguiente comando crea solamente un indice.
~~~
PUT log_consultas
~~~

El siguiente comando nos permitira agregar al indice un documento con los datos *@timestamp*, *estado_consulta*, *servicio*, *administrador* y *consultas_realizadas*.
~~~
POST log_consultas/_doc
{
  "@timestamp":"2010-05-15T22:00:54",
  "estado_consulta":"consumo",
  "servicio":"consulta",
  "administrador":"Juan Carlos",
  "consultas_realizadas":52
}
~~~
Una vez creados los datos se crea un *mapping* que se encargara de clasificar los tipos de datos con fines de optimización. El concepto de *mapping* fue complicado de asimilar pero gracias al video de [*"Mapping"*](https://youtu.be/FQAHDrVwfok) pude entenderlo de una mejor manera.

## Paso 3

Posteriormente tendremos que obtener la configuración que se generó automaticamente en el *mapping*.

Aunque existen diversas formas de obtener esta configuración, la más adecuada que encontre fue la siguiente:
~~~
GET log_consultas/_mapping
~~~
Como respuesta obtuve la siguiente configuración de *mapping*.
~~~
{
  "log_consultas" : {
    "mappings" : {
      "properties" : {
        "@timestamp" : {
          "type" : "date"
        },
        "administrador" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "consultas_realizadas" : {
          "type" : "long"
        },
        "estado_consulta" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "servicio" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        }
      }
    }
  }
}
~~~
Donde en el attributo *mappings* se puede observar que cada atributo que fue introducido en el documento previo tiene una configuración que fue generada automaticamente.

### Problema con template

Posteriormente se indicaba que tenia que realizar un TEMPLATE con los datos para un *mapping*. Intente buscando en el canal de Elastic, sin embargo, no tuve exito. Buscando más encontre un [video](https://youtu.be/sjXffGKyUr8) donde se mostraba paso a paso como crear un template.

Una vez sabiendo como hacer el template procedi a crearlo con el siguiente comando.
~~~
POST _index_template/log_consultas_template
{
  "index_patterns": ["log_consultas*"], 
  "template":{
    "mappings" : {
      "properties" : {
        "@timestamp" : {
          "type" : "date"
        },
        "administrador" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "consultas_realizadas" : {
          "type" : "long"
        },
        "estado_consulta" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "servicio" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        }
      }
    }
  }
}
~~~
 ## Paso 4
 Para este ultimo paso hice uso de la API _bulk, la información necesaria para usar esta API la encontre en la [documentación oficial](https://www.elastic.co/guide/en/elasticsearch/reference/7.15/docs-bulk.html).

Procedí a ejecutar el siguiente comando.
~~~
POST log_consultas/_bulk
{
  "index": {
    "_index": "log_consultas",
    "_id": 1
  }
}
{
  "@timestamp": "2010-05-15T22:00:54",
  "estado_consulta": "consumo",
  "servicio": "consulta",
  "administrador": "Juan Carlos",
  "consultas_realizadas": 52
}
{
  "index": {
    "_index": "log_consultas",
    "_id": 2
  }
}...
~~~
Decidi ejecutar este comando de esta forma, sin embargo, al tener especificado un indice y una id por cada registro no seria necesario especificar en el indice directamente en el comando.