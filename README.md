# readme-prometheus-grafana

El uso primordial de promtheus es obtener todos los datos que son necesarios para ser graficados en grafana, ademas el se encargara de darnos alertas cuando una de estas metricas se enceuntre en un estado de error con sus regals a traves de prometheus rules 

##Instalación

Hay varias maneras de instalar tanto grafana como prometheus, pero la forma más sencilla es haciendo uso de Helm y su chart kube-prometheus-stack, el cual ya crea una base con todas herramientas conectadas en el cluster. Se recomienda crear un namespace de forma separada para tener un mejor manejo de los servicios que se crearan.

