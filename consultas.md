
# Practicas de agregaciones en mongo db

**Consultas**
1. Cuenta los productos de tipo “medio”, usando un método básico
   
   ##### Consulta:
   
   ``` mongosh
   db.productos.count({"tipo":"medio"})
   ```

   ##### Resultado:
    ``` mongosh
   25
   ```
   *** 

2. Indicar con un `distinct`, las empresas (fabricantes) que hay en la colección
   
   ##### Consulta:

   ``` mongosh
   db.productos.distinct("fabricante")
   ```

   ##### Resultado:
   ``` mongosh
   [
        "A.O. Smith",
        "Alere",
        "American Tire Distributors Holdings",
        "Anthem",
        "Archrock",
        "Ascena Retail Group",
        "AutoNation",
        "Best Buy",
        "CIT Group",
        "Cabot",
        "Comcast",
        "Comerica",
        "Core-Mark Holding",
        "DST Systems",
        "Darling Ingredients",
        "Delta Air Lines",
        "Delta Tucker Holdings",
        "Dick's Sporting Goods",
        "First Solar",
        "HCA Holdings",
        "Hanesbrands",
        "Hartford Financial Services Group",
        "Hawaiian Holdings",
        "HealthSouth",
        "Hyatt Hotels",
        "Kar Auction Services", etc...
   ```
*** 
3. Usando `aggregate`, visualizar los productos que tengan más de 80 unidades
    ##### Consulta:

   ``` mongosh
   db.productos.aggregate([
    {
    $match: {
      unidades: { $gt: 80 } 
    }
  }])
   ```

   ##### Resultado:
   ``` mongosh
   { "_id" : ObjectId("661597be5f61c82f50d27841"), "codigo" : 0, "nombre" : "Fantastic Wooden Fish", "unidades" : 95, "precio" : 291, "fabricante" : "Kimberly-Clark", "tipo" : "avanzado" }
   { "_id" : ObjectId("661597be5f61c82f50d27843"), "codigo" : 2, "nombre" : "Small Soft Fish", "unidades" : 96, "precio" : 189, "fabricante" : "Primoris Services", "tipo" : "medio" }
   { "_id" : ObjectId("661597be5f61c82f50d2784e"), "codigo" : 12, "nombre" : "Refined Concrete Salad", "unidades" : 90, "precio" : 129, "fabricante" : "Universal Health Services", "tipo" : "avanzado" }
   { "_id" : ObjectId("661597be5f61c82f50d27860"), "codigo" : 30, "nombre" : "Small Rubber Pants", "unidades" : 89, "precio" : 16, "fabricante" : "Hanesbrands", "tipo" : "basico" }
   { "_id" : ObjectId("661597be5f61c82f50d27863"), "codigo" : 33, "nombre" : "Generic Concrete Hat", "unidades" : 82, "precio" : 70, "fabricante" : "American Tire Distributors Holdings", "tipo" : "basico" }
   { "_id" : ObjectId("661597be5f61c82f50d27876"), "codigo" : 53, "nombre" : "Licensed Plastic Hat", "unidades" : 96, "precio" : 38, "fabricante" : "Best Buy", "tipo" : "medio" }
   { "_id" : ObjectId("661597be5f61c82f50d27877"), "codigo" : 54, "nombre" : "Generic Metal Sausages", "unidades" : 84, "precio" : 77, "fabricante" : "DST Systems", "tipo" : "medio" }
   { "_id" : ObjectId("661597be5f61c82f50d2787e"), "codigo" : 61, "nombre" : "Sleek Rubber Keyboard", "unidades" : 82, "precio" : 33, "fabricante" : "Alere", "tipo" : "basico" }
   { "_id" : ObjectId("661597be5f61c82f50d27883"), "codigo" : 66, "nombre" : "Incredible Concrete Fish", "unidades" : 96, "precio" : 336, "fabricante" : "Darling Ingredients", "tipo" : "medio" }
   ```
 *** 
4. Con `$project` visualizar solo el nombre, unidades y precio de los productos que tengan menos de 10 unidades
   ##### Consulta:

   ``` mongosh
   db.productos.aggregate([
    {
        $match: {
            unidades: { $lt: 10 }
        }
    },
    {
        $project: {
            _id: 0, 
            nombre: 1,
            unidades: 1,
            precio: 1
        }
    }
    ])

   ```

   ##### Resultado:
   ``` mongosh
   { "nombre" : "Ergonomic Metal Ball", "unidades" : 5, "precio" : 246 }
   { "nombre" : "Handmade Plastic Hat", "unidades" : 7, "precio" : 253 }
   { "nombre" : "Ergonomic Metal Table", "unidades" : 0, "precio" : 94 }
   { "nombre" : "Practical Frozen Chips", "unidades" : 0, "precio" : 305 }
   { "nombre" : "Fantastic Metal Pants", "unidades" : 5, "precio" : 129 }
   { "nombre" : "Intelligent Frozen Sausages", "unidades" : 3, "precio" : 111 }
   { "nombre" : "Rustic Plastic Mouse", "unidades" : 5, "precio" : 24 }
   ```
 *** 
5. Con `$project` ponemos el fabricante, pero le cambiamos el nombre por “empresa”. Usamos el mismo comando anterior
   ##### Consulta:

   ``` mongosh
   db.productos.aggregate([
    {
        $match: {
            unidades: { $lt: 10 }
        }
    },
    {
        $project: {
            _id: 0, 
            nombre: 1,
            unidades: 1,
            precio: 1,
            empresa: "$fabricante" 
        }
    }
    ])

   ```

   ##### Resultado:
   ``` mongosh
   { "nombre" : "Ergonomic Metal Ball", "unidades" : 5, "precio" : 246, "empresa" : "Seaboard" }
   { "nombre" : "Handmade Plastic Hat", "unidades" : 7, "precio" : 253, "empresa" : "Dick's Sporting Goods" }
   { "nombre" : "Ergonomic Metal Table", "unidades" : 0, "precio" : 94, "empresa" : "Kelly Services" }
   { "nombre" : "Practical Frozen Chips", "unidades" : 0, "precio" : 305, "empresa" : "Delta Air Lines" }
   { "nombre" : "Fantastic Metal Pants", "unidades" : 5, "precio" : 129, "empresa" : "OneMain Holdings" }
   { "nombre" : "Intelligent Frozen Sausages", "unidades" : 3, "precio" : 111, "empresa" : "A.O. Smith" }
   { "nombre" : "Rustic Plastic Mouse", "unidades" : 5, "precio" : 24, "empresa" : "Orbital ATK" }
   ```
 *** 
6. Añadir a la consulta anterior un campo calculado que se llame total y que multiplique precio por unidades
   ##### Consulta:

   ``` mongosh
   db.productos.aggregate([
    {
        $match: {
            unidades: { $lt: 10 }
        }
    },
    {
        $project: {
            _id: 0, 
            nombre: 1,
            unidades: 1,
            precio: 1,
            empresa: "$fabricante", 
            total: { $multiply: ["$precio", "$unidades"] } 
        }
    }
    ])

   ```

   ##### Resultado:
   ``` mongosh

   { "nombre" : "Ergonomic Metal Ball", "unidades" : 5, "precio" : 246, "empresa" : "Seaboard", "total" : 1230 }
   { "nombre" : "Handmade Plastic Hat", "unidades" : 7, "precio" : 253, "empresa" : "Dick's Sporting Goods", "total" : 1771 }
   { "nombre" : "Ergonomic Metal Table", "unidades" : 0, "precio" : 94, "empresa" : "Kelly Services", "total" : 0 }
   { "nombre" : "Practical Frozen Chips", "unidades" : 0, "precio" : 305, "empresa" : "Delta Air Lines", "total" : 0 }
   { "nombre" : "Fantastic Metal Pants", "unidades" : 5, "precio" : 129, "empresa" : "OneMain Holdings", "total" : 645 }
   { "nombre" : "Intelligent Frozen Sausages", "unidades" : 3, "precio" : 111, "empresa" : "A.O. Smith", "total" : 333 }
   { "nombre" : "Rustic Plastic Mouse", "unidades" : 5, "precio" : 24, "empresa" : "Orbital ATK", "total" : 120 }
   ```
 *** 
7. Hacer que el nombre salga en mayúsculas con el operador `$toUpper`
   ##### Consulta:

   ``` mongosh
   db.productos.aggregate([
    {
        $match: {
            unidades: { $lt: 10 }
        }
    },
    {
        $project: {
            _id: 0, // Excluir el campo _id en la salida
            nombre: { $toUpper: "$nombre" }, 
            unidades: 1,
            precio: 1,
            empresa: "$fabricante", // Renombrar el campo fabricante a empresa
            total: { $multiply: ["$precio", "$unidades"] } 
        }
    }
    ])

   ```

   ##### Resultado:
   ``` mongosh
   
   ```
 ***
8. Añadir un campo calculado que ponga el nombre del producto y el tipo concatenado con el operador `$concat`. Le llamamos al campo “completo”
   ##### Consulta:

   ``` mongosh
   db.productos.aggregate([
    {
        $match: {
            unidades: { $lt: 10 }
        }
    },
    {
        $project: {
            _id: 0, 
            nombre: { $toUpper: "$nombre" }, 
            unidades: 1,
            precio: 1,
            empresa: "$fabricante", 
            total: { $multiply: ["$precio", "$unidades"] }, 
            completo: { $concat: ["$nombre", " - ", "$tipo"] } 
        }
    }])

   ```

   ##### Resultado:
   ``` mongosh
   { "unidades" : 5, "precio" : 246, "nombre" : "ERGONOMIC METAL BALL", "empresa" : "Seaboard", "total" : 1230, "completo" : "Ergonomic Metal Ball - medio" }{ "unidades" : 7, "precio" : 253, "nombre" : "HANDMADE PLASTIC HAT", "empresa" : "Dick's Sporting Goods", "total" : 1771, "completo" : "Handmade Plastic Hat - medio" }
   { "unidades" : 0, "precio" : 94, "nombre" : "ERGONOMIC METAL TABLE", "empresa" : "Kelly Services", "total" : 0, "completo" : "Ergonomic Metal Table - avanzado" }
   { "unidades" : 0, "precio" : 305, "nombre" : "PRACTICAL FROZEN CHIPS", "empresa" : "Delta Air Lines", "total" : 0, "completo" : "Practical Frozen Chips - medio" }
   { "unidades" : 5, "precio" : 129, "nombre" : "FANTASTIC METAL PANTS", "empresa" : "OneMain Holdings", "total" : 645, "completo" : "Fantastic Metal Pants - basico" }
   { "unidades" : 3, "precio" : 111, "nombre" : "INTELLIGENT FROZEN SAUSAGES", "empresa" : "A.O. Smith", "total" : 333, "completo" : "Intelligent Frozen Sausages - basico" }
   { "unidades" : 5, "precio" : 24, "nombre" : "RUSTIC PLASTIC MOUSE", "empresa" : "Orbital ATK", "total" : 120, "completo" : "Rustic Plastic Mouse - avanzado" }
   ```
 ***
9.  Ordena el resultado por el campo “total”
    ##### Consulta:

    ``` mongosh
    db.productos.aggregate([
    {
        $match: {
            unidades: { $lt: 10 }
        }
    },
    {
        $project: {
            _id: 0, // Excluir el campo _id en la salida
            nombre: { $toUpper: "$nombre" }, 
            unidades: 1,
            precio: 1,
            empresa: "$fabricante", 
            total: { $multiply: ["$precio", "$unidades"] }, 
            completo: { $concat: ["$nombre", " - ", "$tipo"] } 
        }
    },
    {
        $sort: {
            total: 1 
        }
    }])

    ```

    ##### Resultado:
    ``` mongosh
    { "unidades" : 0, "precio" : 94, "nombre" : "ERGONOMIC METAL TABLE", "empresa" : "Kelly Services", "total" : 0, "completo" : "Ergonomic Metal Table - avanzado" }
    { "unidades" : 0, "precio" : 305, "nombre" : "PRACTICAL FROZEN CHIPS", "empresa" : "Delta Air Lines", "total" : 0, "completo" : "Practical Frozen Chips - medio" }
    { "unidades" : 5, "precio" : 24, "nombre" : "RUSTIC PLASTIC MOUSE", "empresa" : "Orbital ATK", "total" : 120, "completo" : "Rustic Plastic Mouse - avanzado" }
    { "unidades" : 3, "precio" : 111, "nombre" : "INTELLIGENT FROZEN SAUSAGES", "empresa" : "A.O. Smith", "total" : 333, "completo" : "Intelligent Frozen Sausages - basico" }
    { "unidades" : 5, "precio" : 129, "nombre" : "FANTASTIC METAL PANTS", "empresa" : "OneMain Holdings", "total" : 645, "completo" : "Fantastic Metal Pants - basico" }
    { "unidades" : 5, "precio" : 246, "nombre" : "ERGONOMIC METAL BALL", "empresa" : "Seaboard", "total" : 1230, "completo" : "Ergonomic Metal Ball - medio" }
    { "unidades" : 7, "precio" : 253, "nombre" : "HANDMADE PLASTIC HAT", "empresa" : "Dick's Sporting Goods", "total" : 1771, "completo" : "Handmade Plastic Hat - medio" }
    ```
 ***
10. Haciendo una nueva consulta, averiguar el número de productos por tipo de producto
    ##### Consulta:

    ``` mongosh
    db.productos.aggregate([
    {
        $group: {
            _id: "$tipo", 
            cantidad: { $sum: 1 } 
        }
    }
    ])
    ```

    ##### Resultado:
    ``` mongosh
    { "_id" : "basico", "cantidad" : 24 }
    { "_id" : "avanzado", "cantidad" : 18 }
    { "_id" : "medio", "cantidad" : 25 }
    ```
***
11. Añadir el valor mayor y el menor
    ##### Consulta:

    ``` mongosh
    db.productos.aggregate([
    {
        $group: {
            _id: "$tipo", 
            count: { $sum: 1 }, 
           valor_maximor: { $max: "$precio" },
            valor_minimo: { $min: "$precio" }
        }
    }])
    ```

    ##### Resultado:
    ``` mongosh
    { "_id" : "basico", "count" : 24, "valor_maximor" : 285, "valor_minimo" : 16 }
    { "_id" : "avanzado", "count" : 18, "valor_maximor" : 331, "valor_minimo" : 18 }
    { "_id" : "medio", "count" : 25, "valor_maximor" : 337, "valor_minimo" : 16 }
    ```
***

12. Añade el total de unidades por cada tipo
    ##### Consulta:

    ``` mongosh
    db.productos.aggregate([
    {
        $group: {
            _id: "$tipo", 
            count: { $sum: 1 }, 
            valor_maximor: { $max: "$precio" },
            valor_minimo: { $min: "$precio" },
            total_unidades: { $sum: "$unidades" } 
        }
    }])
    ```

    ##### Resultado:
    ``` mongosh
    { "_id" : "basico", "count" : 24, "valor_maximor" : 285, "valor_minimo" : 16, "total_unidades" : 1067 }
    { "_id" : "avanzado", "count" : 18, "valor_maximor" : 331, "valor_minimo" : 18, "total_unidades" : 773 }
    { "_id" : "medio", "count" : 25, "valor_maximor" : 337, "valor_minimo" : 16, "total_unidades" : 1224 }
    ```
***
13. Con el operador `$set` y el operador `$substr` visualiza todos los datos del producto "Small Metal Tuna" y los primeros 5 caracteres del nombre
    ##### Consulta:

    ``` mongosh
    db.productos.aggregate([
    {
        $match: {
            nombre: "Small Metal Tuna"
        }
    },
    {
        $set: {
            caracteres: { $substr: ["$nombre", 0, 5] }
        }
    },
    {
        $project: {
            _id: 0,
            nombre: 1,
            unidades: 1,
            precio: 1,
            fabricante: 1,
            tipo: 1,
            caracteres: 1 
        }
    }])

    ```

    ##### Resultado:
    ``` mongosh
    { "nombre" : "Small Metal Tuna", "unidades" : 46, "precio" : 43, "fabricante" : "Raymond James Financial", "tipo" : "medio", "caracteres" : "Small" }
    ```
 ***
14. Creamos una salida que tenga el nombre del artículo y el total (precio por unidades) y lo guardamos en una colección denominada `productos2`
    ##### Consulta:

    ``` mongosh
    db.productos.aggregate([
    {
        $project: {
            _id: 0,
            nombre: 1,
            total: { $multiply: ["$precio", "$unidades"] } 
        }
    },
    {
        $out: "productos2" 
    }])
    ```

    ##### Resultado:
    ``` mongosh
   
    ```
 ***
15. Creamos una salida que tenga el nombre del artículo y el total (precio por unidades) y lo guardamos en una colección denominada `productos2`
    ##### Consulta:

    ``` mongosh
    db.productos.aggregate([
    {
        $project: {
            _id: 0, 
            nombre: 1,
            total: { $multiply: ["$precio", "$unidades"] }
        }
    },
    {
        $out: "productos2" 
    }])

    ```

    ##### Resultado:
    ``` mongosh
    Ninguno
    ```
 ***
16. Comprobamos que se ha creado
    ##### Consulta:

    ``` mongosh
    show collections
    ```

    ##### Resultado:
    ``` mongosh
    productos
    productos2
    ```
 ***
17. Hacemos un `find` para comprobar el resultado
    ##### Consulta:

    ``` mongosh
    db.productos2.find()
    ```

    ##### Resultado:
    ``` mongosh
    { "_id" : ObjectId("6615ff861a05d35e656c533a"), "nombre" : "Fantastic Wooden Fish", "total" : 27645 }
    { "_id" : ObjectId("6615ff861a05d35e656c533b"), "nombre" : "Rustic Concrete Pants", "total" : 18414 }
    { "_id" : ObjectId("6615ff861a05d35e656c533c"), "nombre" : "Small Soft Fish", "total" : 18144 }
    { "_id" : ObjectId("6615ff861a05d35e656c533d"), "nombre" : "Practical Soft Pants", "total" : 2747 }
    { "_id" : ObjectId("6615ff861a05d35e656c533e"), "nombre" : "Ergonomic Metal Ball", "total" : 1230 }
    { "_id" : ObjectId("6615ff861a05d35e656c533f"), "nombre" : "Ergonomic Wooden Shirt", "total" : 14994 }
    { "_id" : ObjectId("6615ff861a05d35e656c5340"), "nombre" : "Handmade Steel Chair", "total" : 5392 }
    { "_id" : ObjectId("6615ff861a05d35e656c5341"), "nombre" : "Small Steel Gloves", "total" : 1665 }
    { "_id" : ObjectId("6615ff861a05d35e656c5342"), "nombre" : "Handcrafted Soft Gloves", "total" : 4512 }
    { "_id" : ObjectId("6615ff861a05d35e656c5343"), "nombre" : "Handmade Plastic Hat", "total" : 1771 }
    { "_id" : ObjectId("6615ff861a05d35e656c5344"), "nombre" : "Fantastic Concrete Salad", "total" : 12985 }
    { "_id" : ObjectId("6615ff861a05d35e656c5345"), "nombre" : "Refined Wooden Tuna", "total" : 8480 }
    { "_id" : ObjectId("6615ff861a05d35e656c5346"), "nombre" : "Unbranded Soft Fish", "total" : 9434 }
    { "_id" : ObjectId("6615ff861a05d35e656c5347"), "nombre" : "Refined Concrete Salad", "total" : 11610 }
    { "_id" : ObjectId("6615ff861a05d35e656c5348"), "nombre" : "Small Concrete Fish", "total" : 5040 }{ "_id" : ObjectId("6615ff861a05d35e656c5349"), "nombre" : "Refined Concrete Bike", "total" : 2700 }
    { "_id" : ObjectId("6615ff861a05d35e656c534a"), "nombre" : "Tasty Cotton Pants", "total" : 3536 }
    { "_id" : ObjectId("6615ff861a05d35e656c534b"), "nombre" : "Incredible Granite Gloves", "total" : 20590 }
    { "_id" : ObjectId("6615ff861a05d35e656c534c"), "nombre" : "Practical Metal Mouse", "total" : 6650 }
    { "_id" : ObjectId("6615ff861a05d35e656c534d"), "nombre" : "Handcrafted Steel Chicken", "total" : 6215 }
    ```
 ***
