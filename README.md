# curso-de-nextjs


para crear una aplicacion en reactjs hay que tomar muchas decisiones.

Normalmente no es facil determinar si la configuracion actual es optima para subir a produccion.

total cost of ownership: cuanto le vale a la empresa el costo de mantenimiento de la aplicacion. 

Cuando se trata de react es dificil comprender cuando alguna de las tantas dependencias esta funcionando mal

vamos a hacer un proyecto de aguacates

para trabajar en nextjs no hace falta trabajar obligatoriamente en typescript


vamos a instalar nextjs en nuestro nuevo proyecto

- Requisitos
 - Node > 12
 - git
 - browser chrome dev tools
 - yarn create next-app
 - pasos manuales: 
  - mkdir nextjs && nextjs
  - npm init -y
  - yarn add next react react-dom
  - se agregan 3 scripts
   - "dev": "next"
   - "build": "next build"
   - "start": "next start"
  - luego crear un directorio de paginas: mkdir pages

routing en nextjs: 
- Rutas estaticas: /about/
- Rutas dinamicas: /user/{id}
- pages/index.js sera un componente de react que trabajara como una pagina, Hme component, ya que es la ruta de entrada de toda la aplicacion de nextjs, otra forma de decirlo podria ser que es la pagina inicial 
- para crear paginas dinamicas se usan los [].js para cada pagina
- por ejemplo se puede crear la ruta /product/id se hace una carpeta llamada product y luego una ruta [id].js
- El componente de react resultante puede tener cualquier nombre
- Para acceder al parametro de la ruta se usa el useRouter y se extrae el queryparam, en este caso seria id el nombre del parametro
-under the hood nextjs hace lo siguiente: 
 -  crea multiples archivos de javascript como una tecnica de code-spliting para cargar unicamente el recurso que se esta necesitando
 - nextjs hace pre-rendering en el lado del servidor con server side rendering
 - con la ayuda de la herramienta de inspecconar nos podemos dar cuenta que nextjs esta utilizando la tecnica de optimizacion de server sida rendering o SSR. Esto ayuda a que existe un nivel anterior al ciclo de vida de react que seria el pre-rendering
 - El server side rendering es muy importante para el SEO
 - para enlazar paginas se puede utilizar un <a> con los filenames as routes
 - Al usar el componente de Link de nextjs se deja de hacer una peticion a la hora de recargar una pagina, ocurre en el cliente
  - under the hood nextjs hace prefetching automatico
- hay formas de personalizar las configuraciones de nextjs
- nextjs facilita la creacion de API's. EL proyecto de referencia estaria en el repositorio: https://github.com/jonalvarezz/platzi-nextjs
 - se crea una carpeta en pages llamada api, se guardan en subcarpetas por cada una de las capas adicionales que se guarden en las rutas
 - cada funcion en la capa de API recibe un request/response, es muy parecido a expressjs
 - en cada metodo que se cree de la api se deben de agregar los headers
 - en las apis tambien se pueden crear rutas dinamicas para enviar parametros a la api que luego realizara operaciones en la base de datos
 - App > Document > Nuestra aplicacion, como la aplicacion esta en un nivel mas alto se debe hacer lo siguiente para modificarlo: crear un archivo pages/_document
  - Por ejemplo se puede modificar el head para cargar un favicon, para un webfont, cargar estilos externos, otros scripts. Este Document aplicara para todas las paginas, tambien es util agregar una clase a todo el body 
  - tambien podemos crear un nivel completamente customizable llamado Custom App
   - En realidad en el proceso de render de nextjs se renderiza primero el Documet > App > OurCode
   - El archivo seria: _app.tsx y varios casos de uso podrian ser: providers: context/ theme/ data; hacer un layout base para toda la aplicacion, enviar props adiconales a todos los componentes.
   - Se puede centralizar un componente como por ejemplo un navbar

- la configuracion del path alias es muchisimo mas facil: path-aliases
- en el tsconfig.json se crea una opcion para definir el baseURL y seguido de esto un objeto paths con cada uno de los alias deseados: 
 - ejemplo: 
  "paths": {
    "@database": ["database/db.ts"],
    "@components/*": ["components/*"]
  }

Soluciones de css en nextjs y su flexibilidad: 

- Enfoque #1
 - Archivos global .css, se puede importar desde el layout de la forma: import '../style.css'
- Enfoque #2
 - .module.css, se crea un archivo de modulo para cada componente en especifico: layout.module.css, import styles from './layout.module.css'.
 - Mas contenido de css modules: https://github.com/css-modules/css-modules, https://nextjs.org/docs/basic-features/built-in-css-support
 - las clases no colisionarian con el identificador unico
- Enfoque #3
 - css-in-js: se pasa una etiwueta en el componente: 
  <style jsx>{`
    .container {
      ..;.
    }
  `}</style>
- todos los enfoques son validos


Existe una libreria de componentes react maravillosa que se llama semantic-ui

- Utilizando vercel para desplegar a la nube: 
 - now.sh es ahora vercel
 -  para desplegar una aplicacion de nextjs en vercel solo hay que darle acceso a vercel para leer nuestro repositorio y listo


Data fetching and  pre-rendering
- es muy importante entender la diferencia entre client-side, server-side y static side. Un articulo que puede ayudar bastante para la comprension de estos terminos seria: https://medium.com/@wilbo/server-side-vs-client-side-routing-71d710e9227f

- Usando Nextjs podemos hacer mas facil los llamados a la api (puede ser incluso la misma api que creemos en nextjs) en una funcion llamada getServerSideProps, esta deberia retornar las props que espera recibir el componente y asi mismo definimos las props en el componente
  - en el servidor no existe window, por lo que debemos utilizar un paquete externo para hacer fetch de la informacion llamado isomorfic fetch
  - se puede utilizar async await
  - para inspeccionar todo lo que esta sucediendo en el lado del servidor podemos enviar el parametro de NODE_OPTIONS='--inspect' next
   - luego vamos al about:inspect en chrome
   - luego vamos a pages@index.tsx y podremos ver todo el contenido de los erros.
  - cuando se usa getServerSideProps solo podemos utilizar URLs absolutas, las urls relativas solo pueden usarse en el lado del cliente
  - En desarrollo la informacion llega empaquetada en un JSON
  - La carga se hace desde el cliente hacia el servidor
  - Hay una penalidad y es que las apis igualmente se tienen que llamar, lo que agrega tiempo de carga en la respuesta inicial desde el servidor al cliente

Static Site Rendering

- Nextjs contiene un metodo para obtener propiedades estaticas del mismo servidor
- el metodo para hacerlo desde cualquier componente de tipo pagina (ojo no se puede usar en otros componentes, solo son invocables desde las paginas) se llama: getStaticProps
- En el modo de desarrollo los metodos de: getServerSideProps//getStaticProps no tienen ningun tipo de efecto en el servidor por lo que son ejecutados en el lado del cliente. Devmodeonly
- cada vez que usemos ssg se notara un cambio en el icono en el momento de compilacion
- cual es la diferencia entre serverSide vs staticSide, el server side es tambien conocido como "bajo demanda" lo que significa que cada nuevo request el servidor tiene que hacer otro request a la API por el numnero de personas que se esten conectando al servidor. En cambio, el static solo se ejecuta una unica vez y esta es cuando se ejecuta yarn build. Cuando los chunks de informacion son muy grandes no podemos contar con tata carga del lado  del servidor y lo que debemos hacer seria un static site generation de forma dinamica

SSG dinamico: 
- en cada pagina de detalles hay una generacion estatica del conenido de cada pagina
-  La funcion getStaticProps recibe parametros, estos parametros pueden ser parametros de la URL
- el segundo metodo para las paginas dinamicas se llama getStaticPaths que es una funcion asyncrona 
- esta funcion debe retornar un objeto con la propiedad paths y asi le podemos indicar cuales son los ids de cada pagina que se va a generar de forma estatica
  - por ejemplo, para una pagina de nombre [id].tsx la funcion getStaticPaths va a retornar un objeto con la estructura: 
  {
    paths: [
      {
        params: {
          id: ...
        }
      }
    ]
  }
  - tambien podemos enviarle un fallback lo que va a habilitar el incremental static generation. Se puede utilizar un valor por defecto de false, por lo que resultaria en un 404 para cada pagina que no se encuentre en la lista de paths
  - se debe tener en cuenta la funcionalidad para hacer deploy de una pagina que se esta generando de forma estatica
  - vercel no es la unica forma de desplegar una aplicacion construida en nextjs
  - para facilitar el proceso de despliegue en nextjs podemos crear un nuevo comando llamado export con nextjs: "next export"
  - por cada pagina generada con getStaticPaths vamos a encontrar una carpeta out que tendra el contenido estatico de cada una de las paginas que se compilaron de forma estatica
  - puede ocurrir que tengamos un error al generar paginas estaticas y es que no podemos hacer una pagina estatica de una pagina que depende de getServerSideProps
  - 

nextjs is automatically static

Performance en nextjs

- Como nextjs recopila datos para medir el performance de forma eficiente: asi como las funciones anteriores, hay una que se llma reportWebVitals(metric) esto va a retornar un log por cada uno de los eventos relacionados con la carga de la app. Se complementa con los servicios: analytics o calivre

- Futuro de nextjs segun Jonathan Alvarez 

hay una pagina que sirve como ejemplo para probar la logica del incremental site generation utilizando el fallback true en el momento de utilizar getStaticPaths para la carga de sitios estáticos. Esto ilustrará que se cargará una página que inicialmente muestra una información de carga por lo que es la primera vez que se llama pero al recargar el navegador no habrá ningún tipo de carga ya que esta información se encuentra previamente almacenada. 
 - De momento solo se encuentra en las versiones canary


 hacer un curso de react avanzado y de javascript avanzado

 nextjs 10: Octubre 2020 se incluye el procesamiento de imagenes: tambien mantienen retrocompatibilidad con la version 9 de nextjs
  - Nextjs image: se tiene un componente de imagen para mejorar el performance de la misma
  - yarn upgrade-interactive sirve para listar las posibles actualizaciones de las dependencias del proyecto
   - tiene unas dependencias de varios colores
    - rojas: major version, posiblemente un breaking change: backward-incompatible
    - yellow:  minor update: backward-compatible
    - green: patch: bacward compatible

 - el componente de image de nextjs recibe los tamaño de la imagen
 - las imagenes solo se empiezan a descargar en el momento en que sean visibles las paginas: lazy loading
 - nextjs tambien optimiza la carga de la imagen segun el tamano

 Hay mas actualizaciones como link y proxy
 - el link ya no necesita un atributo to solo con el href es necesario para funcionar
 - en nextjs cuando se va directamente a una url ahora es posible con rewrites
  - se debe crear un archivo llamado nextjs.config.js 
   ``
    module.exports = {
      rewrites: function () {
        return [
          { 
            source: '/product/:path*', //wildcard
            destionation: '/avocado/:path*'
          }
        ]
      }
    }
   ```



caracteristicasd de nextjs13
- nextjs13 renderiza componentes, por defecto, del lado del servidor
- tiene componentes especificos para fuentes y para imagenes
- en el streaming se puede renderizar la pagina por partes
- ya no utilizan webpack porque crearon turbopack



preguntas que no respondi bien por segunda vez: 

- El prefetching automático que nos ofrece Next.js consiste en:
 - no es Iniciar la renderización de otras páginas utilizando Service Workers.

- Los endpoints de una API en Next.js deben quedar en el path: /pages/api

- Sobre las API en Next.js, selecciona el enunciado falso:
  - no es Los endpoints están listos para ser desplegados como Serverless Functions.

- Cuando se desea que una página dinámica pre renderice todo su contenido de forma estática, se debe:
 - no es Ejecutar "next export"

