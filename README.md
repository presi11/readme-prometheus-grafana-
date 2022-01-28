# readme-prometheus-grafana

El uso primordial de promtheus es obtener todos los datos que son necesarios para ser graficados en grafana, ademas el se encargara de darnos alertas cuando una de estas metricas se enceuntre en un estado de error con sus regals a traves de prometheus rules 

## Instalación

Hay varias maneras de instalar tanto grafana como prometheus, pero la forma más sencilla es haciendo uso de Helm y su chart kube-prometheus-stack, el cual ya crea una base con todas herramientas conectadas en el cluster. Se recomienda crear un namespace de forma separada para tener un mejor manejo de los servicios que se crearan.

```console
  helm install [RELEASE_NAME] prometheus-community/kube-prometheus-stack -n [NAMESPACE]
```
Para ver tanto grafana, como prometheus debemos dirigirnos a los servicios creados por el comando y realizar un portforward a ellos. El servicio de grafana tendra el nombre de nuestro *[RELEASE_NAME]-grafana* y el servicio de prometheus *[RELEASE_NAME]-kube-prometheus-stac-prometheus* al entrar a las url proporcionadas veremos algo como esto.

![image](https://user-images.githubusercontent.com/44074556/151597557-c6a64757-838b-4836-91de-51dfb0b26c21.png)

![image](https://user-images.githubusercontent.com/44074556/151597474-e787e9c9-caa6-4f1e-94c0-6663506869c2.png)

## Grafana

Los usarios para ingresar por defecto a grafana serian:

```
admin
prom-operator
```
Se pueden consultar en los secrets con el nombre [RELEASE_NAME]-grafana*

Al ingresar nos podemos dirigir a los dashboards y ahi encontraremos varios creados por defecto, que monitorean diferentes recursos de nuestro cluster como lo pueden ser los pods, volumenes, uso de memoria o CPU, algo como esto.

![image](https://user-images.githubusercontent.com/44074556/151601868-6f879fc5-f0af-4c34-a654-188c1a636efb.png)

## Prometheus

En el prometheus podemos explorar las alerts que se tienen por defecto.

![image](https://user-images.githubusercontent.com/44074556/151600651-bdeb43a0-0773-4513-8d78-f5d4be99cd03.png)

Si se desea eliminar o modificar alguna de estas alertas debemos dirigirnos al CRD de PrometheusRule y encontrar en su configuración la alerta que deseamos modificar.

Si se desea eliminar toda un conjunto de alertas solo es necesario, elinar el archivo de configuracion desde esté lugar.

## Notificacion Slack

Para crear una notificacion con algun servicio se hara uso del alertmanager que trae prometheus, neuvamente se contrara con el nombre de alertmanager-operated en los servicios, podemos hacer un port-forward al puerto 9093 y veremos lo siguiente:

![image](https://user-images.githubusercontent.com/44074556/151602428-d86eada9-e1ef-42cf-816b-34f1d272cb8a.png)

Aca se puede ver de manera mas filtrada las alertas que seran transmitidas a traves de la notifición.

Para hacer uso de Slack se debe obtener un web-hook de algun espacio de trabajo en Slack, sera algo asi:

```
https://hooks.slack.com/services/T02SX6Q/B030A4KM1/M9THhMSrESzgmPHSCW'
```
Con este link ya tenemos permisos para publicar en un espacio de trabajo y en un canal.

Ademas de esto debemos realizar y poner la siguiente configuracion en el secret: alertmanager-[RELEASE_NAME]-kube-prometheus-stac-alertmanager

```
global:
 resolve_timeout: 1m
 #Webhook de nuestro espacio de trabajo
 slack_api_url: 'https://hooks.slack.com/services/T02SX6Q/B030A4KM1/M9THhMSrESzgmPHSCW'
route:
  receiver: 'slack-notifications'  
  routes:
  #Emparejamiento con alguno de nuestros filtros
  - match:
      severity: warning
receivers:
- name: 'slack-notifications'
  slack_configs:
  #Canal al cula se publicara
  - channel: '#alert-prometheus'
    #Información de la notificaón
    text: "Pruebas de repetición"
    #Mandar una notificaion cuando se solucione
    send_resolved: true
```
Se tendra una notificacion de este tipo:

![image](https://user-images.githubusercontent.com/44074556/151603540-176f25eb-5733-4b6d-8a15-5de210b16b28.png)

## Notas

- Está es una notificacionm basica, si se desea realizar una notificiacion con mejor cuerpo, se puede hacer uso de este playground de notificaicones de slack https://juliusv.com/promslack/
- Si se desea hacer una notificacion mucho mas compleja se puede consultar la documentacion oficial https://prometheus.io/docs/alerting/latest/configuration/







