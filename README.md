# Curso Angular7 + Firebase :: OpenWebinars
Código fuente generado durante el curso de Angular 7 + Firebase impartido en OpenWebinars.net

Este repositorio es un subconjunto del código que se genera durante el curso. Se han eliminado la configuración
de Firebase, dependencias y otros ficheros innecesarios para ofrecer una referencia del código que se escribe 
a lo largo del curso.



Notas:

Sobre cómo crear proyecto Angular:

Instalar Angular-CLI:

npm install -g @angular/cli
Crear proyecto:

ng new AngularFirebaseBaseProject --routing
Desplegar proyecto en local:

ng serve --open
Se puede editar package.json para establecer script “start” con “ng serve —open”,
de forma que al ejecutar “npm start” se despliegue el proyecto y lo muestre en navegador.



Instalar Firebase CLI:

npm i firebase-tools
Comandos básicos de Firebase CLI:

firebase login: identificar usuario de Firebase en CLI
firebase logout: eliminar sesión de usuario de Firebase en CLI
firebase init: para configurar firebase en un proyecto Angular (o proyecto de Node.js en general)
Instalar en proyecto dependencias de angularfire:

npm install --save firebase @angular/fire
Configurar parámetros de Firebase para wepapp:

Obtener datos de configuración en consola de Firebase
Añadir variable con los parametros a environment.ts y environment.prod.ts
Declarar módulos de Firebase en app.module.ts

 Dependencia: import { AngularFireModule } from '@angular/fire';
 En imports: AngularFireModule.initializeApp(environment.firebaseConfig)
Donde firebaseConfig es la variable donde hemos configurado los parámetros de Firebase para webapp.


Bootstrap y Fontawesome
Importar en index.html:

 <link crossorigin="anonymous" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" rel="stylesheet" />  
 <link crossorigin="anonymous" href="https://use.fontawesome.com/releases/v5.8.0/css/all.css" integrity="sha384-Mmxa0mLqhmOeaE8vgOSbKacftZcsNYDjQzuCOm6D02luYSzBG8vpaOykv9lFQ51Y" rel="stylesheet" />    
Se pueden obtener últimas versiones de estas referencias en la web oficial de cada proyecto.


Para subir nuestra webapp Angular a Firebase tan sólo hacen falta dos comandos:

ng serve
firebase deploy
También se puede usar “npm run build”, que en package.json estará configurado como “ng serve”.

“firebase deploy” requiere que antes estés identificado en CLI (firebase login) y firebase configurado en el proyecto.


Al crear proyecto angular se generan por defecto dos ficheros para guarda variables de entorno.
Los ficheros se localizan en src/app/environments.

environment.ts
environment.prod.ts
El primero se usa por defecto y se considera entorno de desarrollo. Y el segundo sería para entorno de producción.

El proyecto se despliega por defecto en modo “desarrollo” cuando lanzamos ng serve / npm start.

Para desplegar el proyecto en modo “producción”, debemos añadir parámetro al uso:

ng serve --prod
npm start -- --prod
En Firebase podemos usar 2 proyectos como “entornos” para desarrollo y producción.
Pero realmente lo que se hace es alternar entre dos proyectos, más que concepto de “entornos” en sí mismos.
Para añadir y alternar proyectos usamos firebase-cli:

firebase use -add project_id
firebase use project_id
Para consultar los proyectos disponibles en la cuenta del usuario logado podemos hacerlo con:

firebase list



Lo más práctico en Angular es gestionar la autenticación de usuario en un servicio, para poder instanciarlo
desde cualquier componente/página y acceder a los datos de sesión del usuario identificado.

Para crear un servicio en Angular usamos:

ng g service auth
Para poder usar el módulo para la autenticación de “angularfire” desde el nuevo servicio,
necesitamos primero importarlo en app.module.ts

import { AngularFireAuthModule } from '@angular/fire/auth';
En el servicio debemos instanciar variable de Firebase Auth en el constructor:

constructor(private fbAuth: AngularFireAuth) { }
Y sobre la variable fbAuth, disponemos de un Observable sobre el estado de la autenticación: authState

this.fbAuth.authState
Para obtener el valor del Observable authState podemos hacer “subscribe” o “pipe”. Pero usaremos
“pipe” previendo que nos puede hacer falta hacer un segundo “subscribe” sobre la variable que guarde el resultado:

 
user = this.fbAuth.authState.pipe(map(authState => {
	console.log(‘authState: ‘, authState);
	if (!authState) {
	return null;
	} else {
	// …
	return authState;
	}
	}) );
	
	
Para gestionarla identificación de usuarios con Google se usa:

this.fbAuth.signInWithPopup(...)
Ejemplo:

this.fbAuth.auth.signInWithPopup(new auth.GoogleAuthProvider())
    .then(user => {
      this.authUser = user.user;
      console.log('this.authUser: ', this.authUser);
    })
    .catch(error => console.log(error));
Para terminar la sesión de usuario:

this.fbAuth.auth.signOut();


Para gestionarla identificaciÃ³n de usuarios con Email/Password se usa:

this.fbAuth.signInWithEmailAndPassword (...)
Ejemplo:

this.fireAuth.auth.signInWithEmailAndPassword(this.email, this.password)
    .then(user => {
      this.authUser = user.user;
      console.log('this.authUser: ', this.authUser);
    })
    .catch(error => {
      console.log(error);
    });
Para terminar la sesión de usuario:

this.fbAuth.auth.signOut();


Angular Router
Al haber creado el proyecto con parametro —routing, disponemos de un fichero “app-routing.module.ts”
en el que definir las “routes”. Ejemplo:

const routes: Routes = [
    { path: '', component: HomeComponent },
    { path: 'settings', component: SettingsComponent }
];
Para navegar desde código html (plantilas/templates):

 
<a class="nav-link" routerLink="/settings">Settings</a>
Para navegar desde componente typescript:

 
this.router.navigate([‘/‘]);
Dónde router se declara en el constructor del componente:

 
constructor(private router: Router) { }
Bootstrap
Visita la documentación de Bootstrap para integrar componentes y estilos a tu proyecto webapp:

https://getbootstrap.com/docs
Fontawesome
Para añadir iconos de Fontawesome en las plantillas/templates:

 
<i class="fab fa-google"></i>
Consultar colección de iconos en:



https://fontawesome.com/icons



Visita la consola de Firebase > Database para ver las opciones de gestión de base de datos:

Datos: visualiza la estructura de datos JSON almacenada. Puede añadir, modificar o eliminar valores en esta vista.
Reglas: permite establecer reglas de lectura/escritura sobre la base de datos.
Copias de Seguridad: sólo disponible en plan Blaze o Flame.
Uso: estadísticas de uso (transferencia y almacenamiento).




Al igual que con la autenticación, lo propio para las operaciones de base de datos es centralizarlas en un servicio:

ng g service fireDB
Para poder usar el módulo de Firebase de base de datos, debemos importarlo en app.module.ts:

import { AngularFireDatabase } from '@angular/fire/database';
Para insertar o actualizar un dato en Firebase Realtime Database:

this.db.object(path).update(data)
Donde path es la ruta del objeto a añadir y data es un objeto JSON:

this.db.object('users/userUID').update({email: 'aaa@bbb.com'})



Para montar un CRUD de datos necesitamos saber cómo aplicar las 4 operaciones:

Create
Read
Update
Delete
Ya hemos visto que con “update” podemos tanto insertar como actualizar un dato:

this.db.object(path).update(data)
Para “leer” un objeto, podemos obtener Observable con valueChanges:

this.db.object(path).valueChanges()
Para eliminar datos:

this.db.object(path).remove()



Desde la consola de Firebase Realtime Database puedes importar y exportar los datos de la base de datos,
al completo o parcialmente si navegas por la estructura JSON.

Para el servicio de backups automáticos necesitamos mirar al plan Blaze o Flame. Pero OJO porque
puede suponerte gastos de facturación si se sobrapasan los límites. Más info en:

https://firebase.google.com/pricing/?hl=es-419



Para usar el módulo de Firebase Storage debemos importarlo en app.module.ts:

import { AngularFireStorageModule } from '@angular/fire/storage';
Y como con los otros capítulos, es recomendable crear un servicio de Angular para las funciones
para la gestión de Firebase Storage:

ng g service firestorage
En el servicio necesitamos importar:

import { AngularFireStorage } from '@angular/fire/storage';
Y definir variable al uso en el constructor:

onstructor(private storage: AngularFireStorage) { }
A partir de un input de tipo file:

 
<input type="file" (change)="firestorage.uploadFile($event)" accept=".png,.jpg" />
Podemos usar el método de update de Firebase Storage para subir un fichero. Función completa
con control de resultado/errores:

uploadFile(event) {
    console.log('event: ', event);
    this.storage.upload(this.path + event.target.files[0].name, event.target.files[0])
    .then(result => {
      console.log('result: ', result);
    }).catch(error => {
      console.log('error: ', error);
    });
  }
  
  
 
Para recuperar la URL de un fichero después de subirlo,
debemos introducir el concepto de “task” de Firebase Storage.

Importa AngularFireUploadTask:

import { AngularFireUploadTask } from '@angular/fire/storage';
Al subir un fichero, recupera el AngularFireUploadTask que devuelve el método update:

this.task = this.storage.upload(path, event.target.files[0]);
Y sobre el objeto task puedes subscribirte a los cambios:

this.task.snapshotChanges().pipe( finalize( () => {
      this.downloadURL = ref.getDownloadURL();
      console.log('this.downloadURL: ', this.downloadURL);
    })).subscribe();
	

Cloud Function:
Para crear una cloud-function con firebase-cli, crea una carpeta y desde consola, ejecuta:

firebase init
Sigue el Wizard para configurar un proyecto de cloud-functions. Selecciona el proyecto, typescript, e instalar dependencias.

Una vez creado el proyecto, en consola accede a:

cd functions
Descomenta el código de ejemplo en index.ts y guárdalo. Analiza el código: se define una función que al llamarla,
devuelve una cadena de texto. Ejecuta para publicarla:

firebase deploy
Al terminar indica la URL para llamar a la función recién publicada. Lánzala en el navegador para comprobar el resultado.

Redimensionar imagen:
Desde las Cloud Functions se pueden detectar eventos de Firebase (en Storage o Database, por ejemplo).
Para redimensionar una imagen tras subirla, podemos declarar la función en base al evento de finalización de subida:

export const resizeImage = functions.storage.object().onFinalize ( async object => { ... } )
Consulta documentación para explorar otros eventos y posivilidad de las Firebase Cloud Functions:

https://firebase.google.com/docs/functions/


