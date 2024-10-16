# Guía para configurar un proyecto de azle y react.

Hola, espero se encuentren muy bien, el día de hoy les dejare una guía para configurar un proyecto de azle con react y poder comunicar su backend y frontend entre sí.

## Requerimientos

 - Contar con Node y NPM instalados
 - Tener un proyecto ya creado con el comando `dfx new`

## Backend

Para configurar su backend de manera correcta, primero deben de actualizar la dependencia principal `AZLE`

Para ello, deberan abrir su proyecto en visual studio code e ir a la carpeta backend, dentro encontraran un archivo package.json.

Ese archivo tendrá una propiedad "dependencies" como la siguiente:

```bash
"dependencies": {
	"azle": "^0.19.0"
}
```
Esa versión se debe cambiar a la 22.
```bash
"dependencies": {
	"azle": "^0.22.0"
}
```
Despues de hacer ese cambio abrimos nuestra terminal y nos vamos al directorio del backend y ejecutamos el comando;
```bash
npm i
```
Ese comando instalara nuestra versión de azle la cual tiene un método importante para nuestro backend.

Cuando ya se haya instalado la dependencia, en la misma terminal de antes, vamos a ejecutar el comando:
```bash
npm i cors express
```
Esto para crear nuestro servidor REST API.

Una vez instaladas las dependencias, ahora vamos al archivo "index.ts", dentro de nuestra carpeta `src` de nuestro backend y pondremos el siguiente codigo:
```bash
import { Server } from 'azle';
import express from "express";
import cors from "cors";

export default Server(()=>{
    const app = express();

    app.use(cors())

    app.get("/",(req,res)=>{
        res.status(200).json({msg:"Service is running"})
    })

    app.get("/working",(req,res)=>{
        res.status(200).json({msg:"All working"})
    })

    return app.listen();
})
```

Para finalizar con la configuración de nuestro backend, deberemos buscar el archivo que se llama "tsconfig.json" en la raíz de nuestro proyecto y deberemos agregar la siguiente clave dentro del objeto:
```bash
"allowSyntheticDefaultImports": true
```

Con esta configuración, la base para nuestro backend estará lista y con esto damos por terminado la configuración del backend.

## Frontend
Ahora, nos vamos a enfocar en el frontend, para ello deberemos primero instalar las dependencias que sirven para comunicar y aplicar la tecnologia de ICP.
Nos iremos a nuestra terminal y abriremos la carpeta de nuestro frontend.
Despues ejecutaremos el siguiente comando:

```bash
npm i @bundly/ares-core @bundly/ares-react @bundly/ic-react
```
Una vez se instalen las librerías primero haremos la configuración necesaria para que nuestro backend y frontend se comuniquen entre sí.

Primero nos iremos a nuestro archivo "main.jsx" y ahi eliminaremos todo el codigo y pondremos el siguiente:
```bash
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import './index.scss';
import { Client, InternetIdentity } from "@bundly/ares-core";
import { IcpConnectContextProvider } from "@bundly/ares-react";

const client = Client.create({
  restCanisters: {
    backend: {
      baseUrl: "url de tu backend",
    },
  },
  providers: [
    new InternetIdentity({
      providerUrl: "url de tu internet identity",
    }),
  ],
});

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <IcpConnectContextProvider client={client}>
      <App/>
    </IcpConnectContextProvider>
  </React.StrictMode>,
);

```
En este codigo nosotros estamos creando el contexto de nuestra aplicación con ICP.
Donde deberemos reemplazar la "baseUrl" y "providerUrl" de nuestra configuración por las urls de nuestros canisters correspondientes.

Ya teniendo este codigo, ahora nos debemos dirigir a nuestro archivo "App.js", donde al igual que en el anterior ejemplo, eliminaremos el codigo que hay y pondremos el siguiente:
```bash
import { useEffect, useState } from 'react';
import { useRestActor } from "@bundly/ares-react";

function App() {
  const backend = useRestActor("backend");

  useEffect(() => {
    const res = backend.get("/working");
    console.log(res)
  }, [])
  

  return(
    <>
    <p>Working</p>
    </>
  )
}

export default App;
```

Donde ya haremos una llamada a nuestra API en nuestro canister y mostraremos la respuesta que la API nos devuelve.

## Para terminar
Con todos los pasos anteriormente hechos, nuestro proyecto hecho con azle, ya debería de tener una comunicación entre sus dos principales entidades, frontend y backend.
Finalmente, solo deberan correr el comando "dfx deploy" para ejecutar ambos canisters
