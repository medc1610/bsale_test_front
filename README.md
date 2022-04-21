# Bsale_test Prueba tecnica

_Prueba tecnica, la cual consiste en construir una tienda online que despliegue productos agrupados por la categoría a la que pertenecen, generando por separado backend y frontend y utilizando la base de datos que se disponibiliza para su desarrollo._


### Instalación 🔧

_Primero descargar el proyecto_


## Ejecutando las pruebas ⚙️

_llamar endpoint GET Obtencion de productos paginados_

```
getPagination = (num) => {
  page = page + num  
  const requestOptions = {
    method: 'GET',
    redirect: 'follow'
  };    
  if(page >= 0 && page <= 5){
    fetch(`${path}/api/products/pagination?size=10&page=${JSON.stringify(page)}`, requestOptions)
    .then(response => response.text())
    .then(result => {
      result = JSON.parse(result)
      showProductsTemplate(result)
    })
    .catch(error => console.log('error', error));
    document.getElementById('homeStore').style.display = 'block'
    document.getElementById('shopCart').style.display = 'none'       
  }else{
    page = 0
    fetch(`${path}/api/products/pagination?size=10&page=${JSON.stringify(page)}`, requestOptions)
    .then(response => response.text())
    .then(result => {
      result = JSON.parse(result)
      showProductsTemplate(result)
    })
    .catch(error => console.log('error', error));
    document.getElementById('homeStore').style.display = 'block'
    document.getElementById('shopCart').style.display = 'none'    
  } 
}
}
```
##### Respuesta
_Productos paginados_
```
[{id: 5, name: "ENERGETICA MR BIG",…}, {id: 6, name: "ENERGETICA RED BULL",…},…]
0: {id: 5, name: "ENERGETICA MR BIG",…}
1: {id: 6, name: "ENERGETICA RED BULL",…}
2: {id: 7, name: "ENERGETICA SCORE",…}
3: {id: 8, name: "PISCO ALTO DEL CARMEN 35º",…}
4: {id: 9, name: "PISCO ALTO DEL CARMEN 40º ",…}
5: {id: 10, name: "PISCO ARTESANOS 35º ",…}
6: {id: 11, name: "PISCO BAUZA 40º ",…}
7: {id: 12, name: "PISCO CAMPANARIO 35º",…}
8: {id: 13, name: "PISCO CAMPANARIO 40º",…}
9: {id: 14, name: "PISCO ESPIRITU DEL ELQUI 40º",…}
```
_llamar a endpoint GET Obtencion de productos por nombre_

```
const getProducts = async(name) => {
  const requestOptions = {
    method: 'GET',
    redirect: 'follow'
  };  
  await fetch(`${path}/api/products/${name}`, requestOptions)
    .then(response => response.text())
    .then(result => console.log(result))
    .catch(error => console.log('error', error));
}
```

####Respuesta

```
id: 7, name: "ENERGETICA SCORE",…}
category: 1
discount: 0
id: 7
name: "ENERGETICA SCORE"
price: 1290
url_image: "https://dojiw2m9tvv09.cloudfront.net/11132/product/logo7698.png"
```
## Despliegue 📦

_para hacer deploy de se deben seguir los siguientes comandos_

```
git init
git add .
git commit -m "nombre de commit"
git push
```