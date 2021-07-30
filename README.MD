## Contenerizamos el servicio

###  Creamos un archivo Dockerfile

```
FROM thinkwhere/gdal-python:3.6-ubuntu

WORKDIR /usr/src/app

COPY requirements.txt ./
RUN pip3 install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

ENTRYPOINT [ "python3" ]

CMD [ "service.py" ]
```

Donde:
* Usamos de base una imagen de ubuntu con python 3.6
* Instalamos los requeriemientos, en este caso solo Flask
* Exponemos el puerto 5000
* Y levantamos el servicio de Flask

### Pruebas locales
	docker build -t desarrollo .
	docker build -p 5000:5000 -it desarrollo
	curl http://localhost:5000

## Configuramos el servicio en Cloud Build 

En Cloud Build configuramos un activador, donde conectmos agregamos el repositorio de GitHub a GCP, esto permitira conectarse a ese repositorio 

![image-20210729215536848](C:\Users\PR-JPALACIOS\AppData\Roaming\Typora\typora-user-images\image-20210729215536848.png)

### Configuramos el archivo yaml

```
steps:
  # build the container image
- name: 'gcr.io/cloud-builders/docker'
  args: ['build', '-t', 'gcr.io/$PROJECT_ID/desarrollo_zweicom:${SHORT_SHA}', '.']
  # push the container image to Container Registry
- name: 'gcr.io/cloud-builders/docker'
  args: ['push', 'gcr.io/$PROJECT_ID/desarrollo_zweicom']
  # deploy container image to Cloud Run
  #'--platform' 'gke'
- name: 'gcr.io/cloud-builders/gcloud'
  args: ['beta', 'run', 'deploy', 'desarrollo_zweicom',
  '--image', 'gcr.io/$PROJECT_ID/desarrollo_zweicom:${SHORT_SHA}', '--memory', '1Gi', '--concurrency', '15',
  '--max-instances', '2', '--platform',  'managed', '--region',  'us-central1', '--allow-unauthenticated']
  env:
  - 'PORT=5000'
images:
- gcr.io/$PROJECT_ID/desarrollo_zweicom
```

En este archivo se muetra la configuración de como:
* Se contruye el repositorio
* Como se carga al repositorio de container de GCP
* Como se despliega el contenedor en Cloud Run y sus especificaciones de recursos y limites.

