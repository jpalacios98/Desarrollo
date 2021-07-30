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

