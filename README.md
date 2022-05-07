# BSALE TEST

## Primeros pasos
## Pre Requisitos
Para la realizacion de esta prueba se debera tener acceso a respectiva bas de datos la cual sera entregada por personal que tenga privilegio para entregar dichos datos.

Ademas se debera tener las dependencias de Node en su version 14 o mayor.

```
[Node.js](https://nodejs.org/en/) (v14.x or higher)
```
## Instalacion

Para su instalacion se debera descargar repositorio e instalar dependencias correspondientes.

```
npm install
node start
```

## Funcionamiento

Basicamente el funcionamiento de esta prueba tecnica es el llamar productos desde una base de datos para, ya sea, agruparlos por categoria, llamar productos independientes a travez de un buscador

# Frontend
El frontend se encarga de recibir los datos desde el backend para poder mostrarlos en pantalla para el usuario final.

### Funciones
Se pide realizar aplicacion con vanilla javascript por lo que se realizan peticiones y cambios en el HTML a traves de funciones, las cuales se mostraran a continuacion


### Obtener productos paginados por pantalla

esta funcion llama a los porductos de manera paginada

```
getPagination = (num) => {
  document.getElementById('productFinderDiv').style.display = 'block'
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
```

Para mostrar los elementos de forma paginada esta funcion debe ser ejecutada apenas la pagina sea ejecutada, ademas de ejecutar otra funcion en su interior (showProductsTemplate)

La forma de ejecutar esta funcion de manera automaitca es la siguiente

```
window.onload = () => {  
  getPagination()
  calculateTotalValue()
}
```

### Crear Template HTML ( pagina principal )
esta funcion se utiliza para crear el HTML para mostrar en pantalla todos los productos.
```
const showProductsTemplate = (param) => {  
  let product = param
  let html = ''
  for(let i in product){
    html += "<div>" +              
              "<div class=product-card-content>" +
              "<img class=productImage src='"+ (product[i].url_image ? product[i].url_image : "./assets/img/404.png") +"'/>" + 
              "<div class=product-name>" +
                product[i].name +
              "</div>" +
              "<div class=product-card-footer>" +
                "<div>" +
                  "<div class=product-price>" +
                    "<span>" +
                      (product[i].discount === 0 ? (product[i].price + ' CLP' + "<br>" + "<br>")
                                                 : ( "<del>" + product[i].price + ' CLP' + "</del>" + 
                                                 (product[i].price - (product[i].price *(product[i].discount / 100))) + ' CLP')) +
                    "</span>" +
                  "</div>" +
                  "<div class=product-discount>" +
                    (product[i].discount === 0 ? '' : product[i].discount + '% OFF') +
                  "</div>" +
                "</div>" +
                "<div class=button-cart>" +
                  "<button class=btn id=addCart onclick=cart('"+ product[i].id +"')>" +
                    "Add to cart" +
                  "</button>" +
                "</div>" +
              "</div>" +
              "</div>" +      
              "</div>"; + ''    
  }
  document.getElementById('productCard').innerHTML = html
}
```
### Buscar productos por nombre
Esta funcion permitira a travez de un buscador implementado en el HTML buscar un producto por nombre o entregar todos los productos que contengan la palabra o letra ingresados.
```
productFinder.addEventListener('submit', function (e){  
  e.preventDefault()
  let datos = new FormData(productFinder)
  datos = (datos.get('productName')).toUpperCase()
  const url = `${path}/api/products/${datos}`

  fetch(url)
    .then(response => response.text())
    .then(result => {
      result = JSON.parse(result).product
      showProductsTemplate(result)
    })
    .catch(error => console.log('error', error));
})
```

### Buscar productos por categoria
Para buscar productos por categoria primero se creara un template html
```
const categoriesTemplate = (categories) => {
  let html = ''
  for(let i in categories){
    html += "<button class='btn btn-dark mb-3 text-capitalize'  onclick='getCategoryById("+categories[i].id+")'>" + categories[i].name + "</button>"
  }
  document.getElementById('categories').innerHTML = html
  document.getElementById('categories2').innerHTML = html
}
```

una vez generado el html necesario se debe ejecutar dentro de una funcion que llame todas las categorias para poder utilizar los nombres e id de cada categoria
```
const getCategories = () => {
  const requestOptions = {
    method: 'GET',
    redirect: 'follow'
  };
  
  fetch(`${path}/api/category`, requestOptions)
    .then(response => response.text())
    .then(result =>{ 
          console.log(result)
          result = JSON.parse(result)
          categoriesTemplate(result)})
    .catch(error => console.log('error', error));
}
```

Por ultimo se ejecutara una funcion al presionar el boton correspondiente a cada categoria, para asi poder llamarla por su id
 ```
 const getCategoryById = async(id) => {  
  document.getElementById('productFinderDiv').style.display = 'block'
  const requestOptions = {
    method: 'GET',
    redirect: 'follow'
  }; 
  await fetch(`${path}/api/products/category/${id}`, requestOptions)
    .then(response => response.text())
    .then(result => {
      result = JSON.parse(result).product
      showProductsTemplate(result)
    })
    .catch(error => console.log('error', error));    
    document.getElementById('homeStore').style.display = 'block'
    document.getElementById('shopCart').style.display = 'none'
}
 ```

### Añadir productos al carrito de compras

Para añadir productos al carro se utilizara la siguiente funcion, la cual llama a un producto por su id y lo guarda en el localstorage.

```
const getProductById = (id) => {
  const requestOptions = {
    method: 'GET',
    redirect: 'follow'
  };
  fetch(`${path}/api/products/id/${id}`, requestOptions)
    .then(response => response.text())
    .then(result => {       
      result = JSON.parse(result) 
      if(shopCart.findIndex(x => x.id === result.id) === -1){        
        shopCart.push(result)
        localStorage.setItem('product', JSON.stringify(shopCart))
      }
    })
    .catch(error => console.log('error', error));
}
```

### Crear Template HTML (carrito de compras)
Esta funcion lleva al carrito de compras y a todos los productos que se allan agregado. Para acceder a el se debera presionar el boton de carrito de compras, el cual hara desaparecer la pantalla de inicio y la sustituira por el carrito de compras

```
const showCartTemplate = (param) => {

  let product = JSON.parse(param)
  let html = ''

  for(let i in product){
    product[i].qty? product[i].qty : product[i].qty = 1
   
    html += "<tr>" +
              "<td>" +
                product[i].name +
              "</td>" +
              "<td>" +
              (product[i].discount === 0 ? (product[i].price + ' CLP' + "<br>" + "<br>")
                                         : ( "<del>" + product[i].price + ' CLP' + "</del>" + 
              (product[i].price - (product[i].price *(product[i].discount / 100))) + ' CLP')) +
              "</td>" +
              "<td>" +
                (product[i].discount === 0 ? '' : product[i].discount + '% OFF') +
              "</td>" + 
              "<td>" +
                "<button class=btn id=btnAdd onclick=add('"+ product[i].id +"')>" +
                  "+" + 
                "</button>" + 
                "<span id=quantity" + product[i].id +">" +
                  product[i].qty + 
                "</span>" +
                "<button class=btn id=btnRemove onclick=remove('"+ product[i].id +"')>" +
                  "-" +
                "</button>" +
              "</td>" +             
              "<td>" +
                "<span id=total" + product[i].id +">" +
                (product[i].price - (product[i].price *(product[i].discount / 100))) * product[i].qty + ' CLP' +
                "</span>" +
              "</td>" +
              
              "<td>" +
                "<button class=btn id=btnDelete onclick=deleteProduct('"+ product[i].id +"')>" +
                  "Delete" +
                "</button>" +
              "</td>" +
            "</tr>"
  }
  localStorage.setItem('product', JSON.stringify(product))
  document.getElementById('cartTableBody').innerHTML = html  
}
```

### Funciones del carrito

Una vez en la ventana del carrito de compras tendremos funciones para agregar y quitar cantidades de productos y otras para calcular el precio total de productos en el carrito.

##### Agregar Cantidad de un producto
Esta funcion añadira una unidad del producto seleccionado

```
const add = (id) => {  
    let product = JSON.parse(localStorage.getItem('product'))
    let index = product.findIndex(x => x.id === Number(id))
    product[index].qty++
    localStorage.setItem('product', JSON.stringify(product))
    document.getElementById('quantity'+id).innerHTML = product[index].qty 
    updateCart(id)
    calculateTotalValue() 
  } 
```

##### Quitar Cantidad de un producto
Esta funcion Quitara una unidad al producto seleccionado y si este se hace cero se eliminara

```
const remove = (id) => {
  let product = JSON.parse(localStorage.getItem('product'))  
  let index = product.findIndex(x => x.id === Number(id))  
  product[index].qty--
  localStorage.setItem('product', JSON.stringify(product))
  document.getElementById('quantity'+id).innerHTML = product[index].qty
  updateCart(id)
  calculateTotalValue()
  if(product[index].qty === 0){   
    deleteProduct(id)
  }
}
```
##### Eliminar un producto
Esta funcion eliminara directamente el producto seleccionado sin importar cuantas unidades de ese producto lleve

```
const deleteProduct = (id) => {  
  let product = JSON.parse(localStorage.getItem('product'))  
  let newProduct = []
  for(let i in product){
    if(product[i].id != id){
      newProduct.push(product[i])
    }
  }
  shopCart = newProduct
  localStorage.setItem('product', JSON.stringify(newProduct))  
  showCartTemplate(localStorage.getItem('product'))        
  calculateTotalValue() 
  
}
```

##### Calcular total
Esta funcion calculara el precio total de todos los productos

```
const calculateTotalValue = () => {
  let products = JSON.parse(localStorage.getItem('product')) 
  if(products === null){
    return
  } else {
    let total = 0  
    products.forEach(element => {
      total += (element.price - (element.price *(element.discount / 100))) * element.qty 
    });
    document.getElementById('totalTableBody').innerHTML = total + ' CLP'
  }
}
```
