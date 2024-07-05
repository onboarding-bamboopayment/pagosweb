# Bank Transfer Integration Guide for PagosWeb Gateway

## Id Cliente

El ID del merchant es un identificador único dentro de la aplicación PagosWeb, asignado en el onboarding del comercio con Bamboo Payment.

## Cliente Key

La llave de la cuenta del comercio es un identificador único que debe ser usada para generar el campo `“transactionSecurityToken”` y puede descifrar el campo `“responseSecurityToken”` , asegurándose que ha sido la aplicación de PagosWeb quien envía la respuesta de la transacción.

## **Entorno**

El Entorno es el conjunto de software y recursos que permite procesar las transacciones. El entorno tiene datos e información reales o de prueba, dependiendo de su tipo. Nuestra plataforma tiene dos entornos separados:

## Test

Este entorno permite realizar tareas de prueba. Aquí, las transacciones nunca llegan a los sistemas reales de los proveedores de métodos de pago. Es una forma segura de probar tareas y flujos.

- URL base: http://testing.pagosweb.com.uy/v3.4/requestprocessor.aspx

## **Producción**

Este entorno le permite realizar transacciones reales, que el sistema real de sus métodos de pago procesa. Antes de comenzar a procesar transacciones en este entorno, le recomendamos encarecidamente que realice pruebas en etapa de test.

- URL base: [http://service.pagosweb.com.uy/v3.4/requestprocessor.aspx](http://testing.pagosweb.com.uy/v3.4/requestprocessor.aspx)

## **Configuración Bancaria**

La siguiente información es necesaria para el uso de la transferencia bancaria a través de PagosWeb. Esta información es proporcionada por Sistarbanc al comercio en el momento de su afiliación y debe ser enviada a Bamboo Payment para su configuración.

1. Número de Organismo
2. Tipo de Servicio

## **Tabla de Bancos Emisores**

La siguiente tabla enumera los Bancos Emisores

|Id |Banco emisor
| --- | ---
|5  |Brou
|11	|Santander
|12	|BBVA
|13	|Banque Heritage
|25	|Itaú
|27	|Bandes
|28	|HSBC
|29	|Scotiabank

## **Proceso de Compra**

1. El cliente accede al sitio web del comercio y selecciona los artículos o servicios que desea comprar. Una vez que ha llenado su carrito de compras, procede a la página de pago donde puede utilizar los métodos de pago disponibles.
2. Cuando el cliente elige uno de estos métodos de pago, la página del comercio debe redirigir el navegador del cliente a la página de Solicitud de PagosWeb a través de HTTP/POST.
3. El sistema PagosWeb luego redirigirá al cliente de vuelta a la página web del procesador de pago respectivo, donde elegirá los detalles del método de pago.
4. Cuando el cliente ingrese los datos requeridos para el método de pago y confirme (o cancele) la transacción, el método de pago enviará el navegador de vuelta a la página de procesamiento de respuesta de PagosWeb.
5. Finalmente, después de ejecutar el paso anterior, el sistema PagosWeb redirigirá a través de HTTP/POST a una de las páginas proporcionadas por el comercio en el momento de la afiliación a Bamboo Payment.

## **Configuración URL de respuestas**

Debe suministrar a Bamboo Payment las URL correspondientes a los diferentes resultados posibles de una compra:

- URL de Compra Aprobada: URL del sitio del comercio para invocar desde PagosWeb si la transacción de compra fue aprobada por el procesador.
- URL de Compra Rechazada: URL del sitio del comercio para invocar desde PagosWeb si la transacción de compra fue rechazada por el procesador.
- URL de Compra Cancelada: URL del sitio del comercio para invocar desde PagosWeb si el procesador de la compra indica que la transacción de compra fue cancelada por el usuario.
- URL de Notificación Offline: URL del sitio del comercio para invocar desde PagosWeb cuando el procesador de la compra indica que la transacción fue pagada por el usuario.

Si el cliente no completa correctamente el proceso de compra en el método de pago, la transacción quedará incompleta y la pasarela la registrará con el estado "Solicitud Enviada". Esto puede ocurrir cuando el cliente cierra el navegador inesperadamente o no completa la operación correctamente en el método de pago y no espera el regreso al sitio del comercio.

## **Confirmación Offline**

El comercio puede proporcionar una URL de notificación a la cual el sistema PagosWeb invocará a través de HTTP/POST, comunicando el pago de la transacción. Para los bancos, siempre se recibe la notificación offline, por lo que si el cliente realizó correctamente la operación de pago en el sitio del banco, pueden recibir dos confirmaciones de la misma: una en el momento de realizar el pago y la segunda por la notificación offline. Si el proceso se completa correctamente, pero el usuario no es redirigido de vuelta al sitio del comercio, solo se recibirá una notificación.

## **Solicitud HTTP a PagosWeb**

La solicitud a la página de PagosWeb debe realizarse utilizando el método HTTP POST. El formulario HTTP debe contener los siguientes campos:

| Campo | Tipo | Presencia | Descripción |
| --- | --- | --- | --- |
| idCliente | Numérico | Obligatorio | Número de cliente dentro de la aplicación PagosWeb, asignado durante la instalación. Es fijo a partir de la primera instalación. |
| idTarjetaCredito | Numérico | Obligatorio | Ver Detalles de Bancos Emisores. |
| primerNombre | Alfanumérico | Obligatorio | Nombre del cliente. |
| primerApellido | Alfanumérico | Obligatorio | Primer apellido del cliente. |
| email | Alfanumérico | Obligatorio | Dirección de correo electrónico del cliente. |
| valorTransaccion | Numérico | Obligatorio | Monto de la compra (sin separadores de miles, usando un punto como separador decimal). Ejemplo: $1,234.50 debe enviarse como 1234.5. |
| cantidadCuotas | Numérico | Obligatorio | Para bancos se debe enviar cuotas: 1 |
| moneda | Numérico | Obligatorio | Códigos de moneda. Valores posibles: 858 - Pesos, 840 - Dólares. |
| numeroOrden | Alfanumérico | Obligatorio | Número de orden del comercio. Sirve como referencia de la transacción realizada en el negocio del comercio. |
| version | Alfanumérico | Obligatorio | Versión actual del paquete de información. Valor actual: "3.4" |
| fecha | Alfanumérico | Obligatorio | Fecha y hora de la transacción. En el formato "yyyy-MM-dd hh:mm:ss" (formato de 12 horas). |
| plan | Alfanumérico | Obligatorio | Campo de 2 caracteres utilizado solo por Oca. Especifica el tipo de compra (0 = común, 3 = con metros). |
| segundoNombre | Alfanumérico | Opcional | Segundo nombre del cliente. |
| segundoApellido | Alfanumérico | Opcional | Segundo apellido del cliente. |
| direccionEnvio | Alfanumérico | Condicional | Dirección de envío de la mercancía. Este dato es obligatorio para VISA. |
| plazoEntrega | Alfanumérico | Opcional | Plazo de entrega declarado al cliente. |
| telefono | Alfanumérico | Condicional | Número de teléfono de contacto del cliente. |
| cedula | Alfanumérico | Condicional | Número de cédula del cliente. Ver "Condición de cédula". |
| consumidorFinal | Numérico | Obligatorio | (Ley 19210) Indicar si la venta se realiza a un consumidor final. El valor 1 indica que es un consumidor final, 0 que no lo es. |
| importeGravado | Numérico | Condicional | (Ley 19210) Importe gravado por IVA (sin separadores de miles, usando un punto como separador decimal). Es obligatorio si consumidorFinal=1 y debe tener el valor gravado con IVA. Ver "Detalle del Importe Gravado". |
| numeroFactura | Numérico | Condicional | (Ley 19210) Número de factura que será utilizado por el método de pago seleccionado para informar la devolución de IVA a la DGI. Máximo 7 dígitos. Aplicable si consumidorFinal=1. |
| transactionSecurityToken | Alfanumérico | Obligatorio | Datos de la transacción cifrados con 3DES. |
| tipoDocumento | Numérico | Condicional | Valores: 1=Cédula, 2=Rut, 3=Doc. Extranjero. |
| additional | Alfanumérico | Opcional | Información adicional si se requiere, enviada separada por ";". |


## **Respuesta HTTP de PagosWeb**

| Campo | Tipo | Descripción |
| --- | --- | --- |
| ventaAprobada | Booleano | true indica que la venta fue aprobada, false indica que la venta fue rechazada. |
| codigoAutorizacion | Alfanumérico | Opcionalmente, si la venta fue aprobada, se puede informar un código de autorización proporcionado por el procesador. |
| numeroTransaccion | Alfanumérico | Número de transacción asignado por el procesador. |
| monto | Numérico | Monto original de la transacción. |
| mensaje | Alfanumérico | Mensaje asociado a la transacción. Se puede mostrar en la página para referencia del cliente. |
| numeroOrden | Alfanumérico | Se devuelve el número de orden que se envió originalmente en la Solicitud (según la configuración del comercio). |
| idCliente | Numérico | Número de cliente dentro de Bamboo Payment, asignado durante la instalación. |
| Fecha | Alfanumérico | Fecha y hora en que se realizó la respuesta, en el formato "yyyy-MM-dd hh:mm:ss". |
| cantidadCuotas | Numérico | Se devuelve el número de cuotas de la operación. |
| responseSecurityToken | Alfanumérico | Datos de la transacción cifrados. |

## **Seguridad 3DES en Solicitud y Respuesta**

En la versión 3.4 de PagosWeb, se incluye un campo de seguridad en las transacciones, para que PagosWeb asegure el origen de la transacción. Este campo debe estar cifrado utilizando el algoritmo de cifrado 3DES en su modo CBC (CipherBlockChaining). Durante la instalación de un nuevo comercio en Bamboo Payment, se le asigna una clave de cifrado, que se envía al contacto técnico del comercio. Una vez que se obtiene la clave, debe implementar la integración:

1. **Implementación por parte del comercio:**
    - El comercio debe generar el campo `"transactionSecurityToken"` utilizando la clave proporcionada por PagosWeb.
    - El comercio puede descifrar el campo `"responseSecurityToken"` para asegurarse de que la respuesta proviene de PagosWeb.
2. **Pasos para cifrar la información de la transacción:**
    - Concatenar la información de la transacción en un orden específico.
    
    `idCliente + idTarjetaCredito + primerNombre + primerApellido + segundoNombre + segundoApellido + cedula + email + telefono + direccionEnvio + valorTransaccion + cantidadCuotas + moneda + numeroOrden + version + plan + plazoEntrega + fecha + consumidorFinal + importeGravado + numeroFactura`
    
    - Convertir la información a cifrar a una matriz de bytes ASCII de 7 bits.
    - Generar el vector de inicialización utilizando la fecha de la transacción.
    - Convertir la llave privada del comercio proporcionada por Bamboo Payment a una matriz de bytes de 8 bits.
    - Cifrar la información con 3DES en modo CBC y codificarla en dígitos base64.

**Ejemplo de cifrado en [asp.net](http://asp.net) con C#**

    public static string GetSecurityToken() 
    {
      string retorno = "";
      // Transaction date = "2012-10-25 11:18:00"
      DateTime dFechaTransaccion = DateTime.Now;
      
      // Merchant's 3DES key
      string keyComercio64 = "{{MerchantKey}}";
      
      // Plaintext information to be encrypted
      string textoPlano = "31ArmandoQuitoEstebanBlanco1234567-9" +
                          "unmail@dominio.com123456789Av. Sin Nombre 00001234.50685835353.3048hs" +
                          dFechaTransaccion.ToString("yyyy-MM-ddhh:mm:ss") + "1300A123456";
      
      // Initialization vector = "21025111800="
      string sIV64 = dFechaTransaccion.ToString("yyMMddhhmmss").Substring(1) + "=";
      
      retorno = EncryptTextToMemory(textoPlano, keyComercio64, sIV64);
      
      // Example encrypted security token
      // retorno = "M7lMF5dt13yCY2yX0ATjwgo5GnkOXx4kKXYpsVMqxL8PRm3I+vORpfJo8XplfeQLfnn3q
      //           J/WKw8fplY7URCGJfxXjSa7T6LLBcpQ6fuT3RUjRaH/TPrRpG5nW/DNhSSuuvYsUnU
      //           +2APH7vbKRcRqytxzF4QSjoCcDiFfdwu14WM="
      
      return retorno;
    }

    public static string EncryptTextToMemory(string Data, string sKey, string sIV)
    {
        try
        {
            System.IO.MemoryStream mStream = new System.IO.MemoryStream();
            byte[] bKey = System.Convert.FromBase64String(sKey);
            byte[] bIV = System.Convert.FromBase64String(sIV);
            UTF8Encoding utf8 = new UTF8Encoding();
            byte[] toEncrypt = utf8.GetBytes(Data);
            
            System.Security.Cryptography.CryptoStream cStream = new CryptoStream(mStream,
                new TripleDESCryptoServiceProvider() { Key = bKey, IV = bIV, Mode = CipherMode.CBC }.CreateEncryptor(),
                CryptoStreamMode.Write);
            
            cStream.Write(toEncrypt, 0, toEncrypt.Length);
            cStream.FlushFinalBlock();
            byte[] ret = mStream.ToArray();
            
            cStream.Close();
            mStream.Close();
            
            return System.Convert.ToBase64String(ret);
        }
        catch (CryptographicException e)
        {
            return "Error";
        }
    }
    
## **Descifrar el ResponseSecurityToken**

Los siguientes pasos son necesarios para descifrar el campo de seguridad de la respuesta:

1. Convertir la información a descifrar a una matriz de bytes en base64.
2. Generar el vector de inicialización (IV). El campo "fecha" en la respuesta representa la fecha y hora en que se realizó la respuesta. Utiliza esta fecha para generar el IV siguiendo el mismo procedimiento que en el paso anterior.
3. Convertir la llave del comercio y el vector de inicialización a matrices de bytes en base64.
4. Descifrar la información utilizando 3DES en modo CBC y convertir la matriz de bytes ASCII de 7 bits resultante a texto plano. Para esto, utiliza la función **`GetString()`** de la clase **`UTF8Encoding`**.
5. Verificar la información de la respuesta. La información cifrada es la misma información contenida en la respuesta, concatenada en el siguiente orden: **`ventaAprobada + numeroTransaccion + monto + codigoAutorizacion + mensaje + numeroOrden + idCliente + fecha`**.

Nota: El campo "cantidadCuotas", no se incluye en la validación del token.

    `csharppublic string DesCifrarSecurityToken(string securityToken, string sfecha)
    {
        string retorno = "";
        DateTime dFechaRespuesta;
        if (DateTime.TryParse(sfecha, out dFechaRespuesta))
        {
            string keyComercio64 = "{{MerchantKey}}";
            string sIV64 = dFechaRespuesta.ToString("yyMMddhhmmss").Substring(1) + "=";
            retorno = DecryptTextFromMemory(securityToken, keyComercio64, sIV64);
            // retorno = "True4961234,51234Venta Aprobada353532012-10-2511:18:05"
        }
        return retorno;
    }

    public static string DecryptTextFromMemory(string sData, string sKey, string sIV)
    {
        try
        {
            byte[] Data = System.Convert.FromBase64String(sData);
            byte[] bKey = System.Convert.FromBase64String(sKey);
            byte[] bIV = System.Convert.FromBase64String(sIV);
            MemoryStream msDecrypt = new MemoryStream(Data);
            CryptoStream csDecrypt = new CryptoStream(msDecrypt,
                new TripleDESCryptoServiceProvider() { Mode = CipherMode.CBC, Key = bKey, IV = bIV }.CreateDecryptor(),
                CryptoStreamMode.Read);
            byte[] fromEncrypt = new byte[Data.Length];
            csDecrypt.Read(fromEncrypt, 0, fromEncrypt.Length);
            UTF8Encoding utf8 = new UTF8Encoding();
            string decodedString = utf8.GetString(fromEncrypt);
            return decodedString;
        }
        catch (CryptographicException e)
        {
            return "Error";
        }
    }`
