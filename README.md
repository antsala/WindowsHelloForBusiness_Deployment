# WindowsHelloForBusiness_Deployment
Configuración del tenant de M365 para usar Windows Hello For Business

## Requisitos

Se necesita.

* Un tenant de M365 con licencia de Intune (Para Windows Hello For Business Avanzado)
* Una VM de Windows 11 con un usuario local.

## ¿Qué es Windows Hello para la Empresa?

`Windows Hello for Business` es una solución de autenticación de Microsoft diseñada para reemplazar las contraseñas tradicionales con métodos de inicio de sesión más seguros y convenientes. Utiliza tecnologías de autenticación biométrica (como reconocimiento facial o huellas dactilares) y claves de seguridad basadas en PIN, eliminando la dependencia de contraseñas que son vulnerables a ataques de phishing y robos. Windows Hello for Business integra autenticación de dos factores y cifrado de hardware, ofreciendo una forma robusta y fácil de usar para proteger el acceso a dispositivos, redes y aplicaciones empresariales.

Windows Hello for Business, en adelante `WHFB` se puede (y se debe) usar para proteger convenientemente la MFA al acceder a las aplicaciones de M365. En este artículo nos centraremos en el uso de esta tecnología para iniciar sesión en los dispositivos clientes de Windows. 

Recuerda que `WHFB` no usa contraseñas, es decir, es `PasswordLess`. De esta forma, al no usar contraseñas para autenticar a los usuarios en sus dispositivos, se eliminan de forma radical todas las amenazas basadas en capturas de la contraseña, robos de hash, etc.

`Windows Hello` es una característica que aparece en `Windows 10`, pero no debe confundirse con `WHFB`, que es la versión de empresa y ofrece muchas más posibilidades. Teniendo esto en cuenta, a partir de ahora, cuando hagamos referencia a `Windows Hello`, nos referimos a su versión empresarial (`WHFB`).

Con `WHFB`, en lugar de iniciar sesión en el dispositivo con un `Nombre de usuario` y una `Contraseña`, podemos iniciar sesión...

* Con un `PIN`
* Usando `Biometría` (huella digital o reconocimiento facial)
* Con un `Hardware Security Token` como los que proporciona `FIDO2`.

Con `WHFB` la organización se mueve a una nueva dirección, consistente en ***NO UTILIZAR NUNCA MÁS CONTRASEÑAS*** en las autenticaciones. Esta estrategia se conoce como `PasswordLess`.

`WHFB` es entonces una forma mucho más segura de iniciar sesión en los dispositivos, ya que, al usar un PIN, biometríca o tokens de hardware, estos no abandonan (se transmiten) el dispositivo en ningún momento. En consecuencia no pueden ser capturados por la red.

## Primera opción de configuración de WHFB

La primera forma de configurar `WHFB` es 'no hacer nada', ya que `WHFB` está habilitado por defecto en todos los tenants de M365. Por consiguiente, podemos usarlo directamente.

Nos logamos en el tenant y creamos un usuario de prueba. 
Nota: La contraseña es `Pa55w.rd12345`.

![Crear usuario](./img/202411091137.png)

Asignamos licencias.

![Asigna licencia](./img/202411091141.png)

El dispositivo del usuario debe estar registrado en `Microsoft Entra ID`. Tienes dos formas de conseguirlo:

* Uniendo el disposito al dominio de `Entra ID`.
* Registrando el dispositivo en `Entra ID`.

El `registro` al dominio es útil cuando queremos que `Microsoft Entra ID` identifique al dispositivo, que puede ser `Linux`, `Android`, `IoS`, `MAC` y por supuesto `Windows`. Para `WHFB`, como es lógico solo se puede aplicar a sistemas operativos de Windows.

La `Unión al Dominio de Microsoft Entra ID` es la forma más cómoda cuando la organización es nativa de nube.

Existe la posibilidad de la `Unión híbrida al dominio de Microsoft Entra ID`, donde el dispositivo puede ser a la vez miembro del dominio de la nube y del on-prem (`ADDS`)

Por simplicidad de la práctica, vamos a usar la `unión al dominio en la nube`, pero ten en cuenta que `WHFB` funciona para cualquier escenario.

Más adelante vamos a ver formas avanzadas de `WHFB`, que requerirán el uso de `Intune` (Endpoint Manager). Por esa razón, ahora es buen momento para activar la configuración que permite que un dispositivo inscrito sea gobernado automáticamente por `Intune` (inscripción automática en Intune).
Nota: Un dispositivo `inscrito` en `Entra ID` es aquel que o bien ha sido `registrado` o ha sido `unido al dominio de Entra ID`.

![Inscripcion automática](./img/202411091158.png)

Seleccionamos ***Todo*** en ámbito MDM y Guardamos. Esto permitirá que todos los usuarios puedan inscribir sus dispositivos en Intune.

![Seleccionar TODO](./img/202411091200.png)

De vuelta a la página de inscripción, seleccionamos la configuración de `WHFB`.

![Seleccionar WHFB](./img/202411091204.png)

Estudia la siguiente imagen.

![WHFB](./img/202411091206.png)

En (1) puedes comprobar que `WHFB` está habilitado para ***Todos los usuarios***. En su configuración por defecto, como ya anunciamos, no hay que hacer nada. Las opciones que puedes ver en (2) las trataremos más adelante. Por esta razón, el botón ***Guardar*** (3) no se activa.

Vamos a proceder a unir el dispositivo al dominio de la nube. Para ello vamos a `Settings`, `Accounts` y hacemos clic en `Access work or school`.

![Work or school](./img/202411091345.png)

Hacemos clic en `Connect`.

![Connect](./img/202411091347.png)

Observa la siguiente imagen.

![inscripción](./img/202411091350.png)

Si pones el email del usuario en (1) se procederá al `registro`. En (2) podemos unir al dispositivo al dominio de `Entra ID`. Por último, en (3), unimos al dispositivo al dominio on-prem `ADDS`. Elegimos la opción (2).

Nos autenticamos con el usuario.

![inscripción](./img/202411091354.png)

Un mensaje nos indica que comprobemos si todo es correcto.

![inscripción](./img/202411091355.png)

hacemos clic en `Join`.

Se está procediendo a inscribir el dispositivo en `Entra ID`.

Volvemos a la página de administración de `Intune` y, tras un par de minutos, como muestra la imagen, podrás ver el equipo. Recuerda que Intune solo se necesita para poder configurar los ajustes avanzados de `WHFB` y, en muchos escenarios, no es necesario realizarlos, ya que la protección por defecto de `WHFB` cumple las necesidades de la mayoría de nosotros.

![inscripcion](./img/202411091400.png)

Ahora debemos configurar `Windows Hello` en el dispositivo. Para ello cerramos sesión. 

Elegimos `Otro usuario` y escribimos el email del usuario de ejemplo y su contraseña.

![inscripcion](./img/202411091403.png)

La contraseña sigue siendo necesaria para configurar Windows Hello en el dispositivo, pero a partir de entonces ya no será necesaria.

Se crea el perfil del usuario.

![inscripcion](./img/202411091406.png)

Como puedes ver en la siguiente imagen, se nos invita a configurar `Windows Hello`.

![inscripcion](./img/202411091407.png)

Un mensaje nos indica que debemos activar la `MFA` para el usuario.

![inscripcion](./img/202411091409.png)

`WHFB` exigirá un 2FA cuando se vaya a reconfigurar, por ejemplo, por olvido del `PIN`. Así que tenemos que configurar la 2FA. 

Seguimos los pasos indicados: Instalar `Microsoft Authenticator` y lo configuramos.

![2faok](./img/202411091419.png)

Es el momento de configurar nuestro `PIN`.

![PIN](./img/202411091420.png)

Un mensaje nos indica que hemos finalizado la configuración de `Windows Hello` en el dispositivo.

![OK](./img/202411091422.png)

Cerramos la sesión y comprobamos que ya podemos iniciarla con PIN (y sin contraseña)

Observa que, tenemos otras opciones disponible para iniciar sesión.

![OK](./img/202411091424.png)

Si te fijas, tenemos la contraseña.

![OK](./img/202411091426.png)

Esto tiene sentido porque lo que hemos hecho es añadir una nueva forma de inicio de sesión, basada en PIN, pero no se ha anulado el uso de las contraseñas.

Observa la siguiente imagen.

![Ways](./img/202411091428.png)

Puedes ver las diferentes formas en las que se permite el inicio de sesión. 

Para `Windows Hello` se tiene:

* ***Reconocimiento facial*** (hace falta una cámara especial. Consulta los requisitos)

* ***Huella digital***. Si el dispositivo tiene lector de huella digital, el asistente de configuración de `Windows Hello` te guía para grabar la información biométrica.

* ***PIN***. Esta es la opción que hemos configurado en este tutorial, ya que la VM no tiene ni webcam ni hardware biométrico.

Otras formas de inicio de sesión:

* ***Security key***. Son tokens que almacenan dispositivos de hardware (FIDO2).

* ***Password***. Es la tradicional.

* ***Picture password***. Permite dibujar un patrón con el ratón (o con el dedo en dispositivos táctiles)

Un poco más abajo verás lo siguiente.

![Activar WH](./img/202411091436.png)

Solo se permiten los inicios de sesión que ofrece `Windows Hello`.

Como imaginarás, lo que vamos a tratar a continuación, determinará qué opciones de inicio de sesión se le ofrece al dispositivo del usuario. Esta configuración se realizará en `Microsoft Entra ID` y una vez configuradas, el usuario no podrá cambiarlas en su dispositivo.

## Segunda opción de configuración de WHFV

La forma de configurar `WHFB` que hemos explicado no es realmente una opción, ya que `WHFB` estaba ya connfigurado. Solo hemos indicado (mediante la inscripción de dispositivo) que `Windows 11` configure `Windows Hello` para iniciar sesión. 

En esta segunda opción, vamos a aprender a cambiar algunos de los ajustes de seguridad de `WHFB`.

https://www.youtube.com/watch?v=A8faHO-bn-0










