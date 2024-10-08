![Directories](http://www.bytte.com.co/ftpaccess/Varios/CarlosG/Documentaci%C3%B3n/LogoBytte.png)

# Bytte SDK
# Documento Integración SDK Nativo iOS Biometría

## CONFIDENCIALIDAD

La información contenida en el presente documento es **CONFIDENCIAL** y forma parte del secreto comercial e industrial de BYTTE SAS. Esta documentación implica la transmisión de información cuya propiedad corresponde exclusivamente a BYTTE SAS. En consecuencia, la divulgación o el uso inapropiado de la información aquí contenida por parte del receptor implicará la aplicación de las normas legales pertinentes para su debida protección. Cualquier uso no autorizado de este material puede resultar en acciones legales.

## Descripción

Este repositorio contiene una guía completa y detallada sobre cómo implementar y utilizar el SDK de captura de biometría en aplicaciones iOS. La solución está diseñada para manejar las siguientes biometrías:

- **Captura Facial** 
- **Captura Dactilar**

El objetivo de este repositorio es proporcionar a los desarrolladores todas las herramientas y la información necesaria para integrar de manera eficiente y precisa la funcionalidad de captura de biometría en sus aplicaciones. Aquí encontrarás ejemplos de código, instrucciones de configuración, mejores prácticas y recursos adicionales para garantizar una implementación exitosa.

## Contenido

1. [Introducción](#introducción)
2. [Requisitos](#requisitos)
3. [Instalación](#instalación)
4. [Configuración](#configuración)
5. [Uso](#uso)
6. [Respuesta](#Respuesta)
7. [Soporte](#soporte)

## Introducción

El presente documento tiene como finalidad dar a conocer el paso a paso de la instalación del SDK provisto por Bytte en una aplicación nativa IOS desarrollada con el IDE XCode.

## Requisitos

* Se debe verificar la calidad de la cámara, es recomendable utilizar dispositivos con cámara que tengan la característica de “Auto Foco” habilitada.
* Se recomiendan cámaras con resolución mayores o iguales a 8 Mega Pixeles para un óptimo rendimiento.
* El SDK no funciona sobre dispositivos virtuales, únicamente sobre dispositivos físicos IPhone.

#### Deshabilitar BitCode

Para el correcto funcionamiento de la aplicación, se debe deshabilitar el BitCode:

![Directories](http://www.bytte.com.co/ftpaccess/Varios/CarlosG/Documentaci%C3%B3n/Bitcode.png)


## Instalación

* Para la compilación de aplicación en plataforma IOS, se requiere:
    > * XCode 15.3 o Superior con SDK IOS 15 o superior.

## Configuración

* La aplicación debe tener las siguientes librerías como recurso embebido en el folder “Frameworks” como lo indica la siguiente imagen:

 ![Directories](http://www.bytte.com.co/ftpaccess/Varios/CarlosG/Documentaci%C3%B3n/Biometr%C3%ADa/FrameworkFace.png)
 
  ![Directories](http://www.bytte.com.co/ftpaccess/Varios/CarlosG/Documentaci%C3%B3n/Biometr%C3%ADa/FrameworkFinger.png)

  > * BytteLibrarySDKComun.framework
  > * BytteLibrarySDKDocumentoV2.framework
  > * BlinkID.framework
  > * BytteLibrarySDKFaceID.framework
  > * BytteLIbrarySDKFingerID.framework
  > * Identy.framework
  > * IdentyFace.framework

## Uso

Para utilizar los métodos proporcionados por el SDK para la captura de documentos, siga estos pasos:

### 1. Importar el SDK

Primero, es necesario importar el SDK en su proyecto de iOS:

```swift
import BytteLibrarySDKFaceID
import BytteLIbrarySDKFingerID
```

### 2. Implementar el Delegado

El SDK expone un delegado que debe implementar para manejar los eventos de captura de biometriías:

```swift
class ViewController: UIViewController, FingerIDDelegate,FaceIDDelegate {
    // Implementación de los métodos del delegado
}
```

### 3. Configurar el Método de Captura

Para el correcto funcionamiento del SDK, el método de captura requiere variables que serán proporcionadas por BYTTE S.A.S., así como parámetros que deben ser generados y proporcionados por cuenta propia:

- `url`: la URL del servidor de BYTTE.
- `sessionID`: llave de cifrado.
- `clientID`: Información proporcionada por BYTTE.
- `clientKey`: Información proporcionada por BYTTE.
- `licenseId`: Nombre de la licencia facial y/o dactilar, omitiendo la extensión .lic

### 4. Ejemplo de Implementación

Aquí tiene un ejemplo de cómo configurar y utilizar el SDK en su aplicación, incluyendo el evento y el delegado:

#### 4.1 Captura Dactilar
```swift
import UIKit
import BytteLIbrarySDKFingerID


class ViewController: UIViewController, FingerIDDelegate {
  
    @IBOutlet weak var idProceso: UILabel!
    //Label
    @IBOutlet weak var lblFinger1: UILabel!
    @IBOutlet weak var lblFinger2: UILabel!
    @IBOutlet weak var lblFinger3: UILabel!
    @IBOutlet weak var lblFinger4: UILabel!

    let fingerID = IDCaptureBiometricFingerIDBytte();
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
    }
    
    //Evento Capturar
    @IBAction func btnCapturar(_ sender: UIButton) {
    
        fingerID.delegate = self
        //Dedo base a Capturar (Mano derecha = 2) (Mano Izquierda = 7)
        let requetFinger = FingerRequest(url: Endpoints.url, fingerNumber: Endpoints.fingerNumber, sessionID: Endpoints.sessionID, clientID: Endpoints.clientId, clientKey: Endpoints.clientKey);
        
        fingerID.IDCaptureFinger(licenseId: Endpoints.licFinger, viewController: self, request: requetFinger)

    }

    //Respuesta de la captura dactilar
    func doCaptureFingerID(fingerResponse: FingerResponse) {
        DispatchQueue.main.async{
            self.idProceso.text = fingerResponse.MensajeRetorno;
            if(fingerResponse.StatusOperacion) {
                             
                
                let fingerID = IDCaptureBiometricFingerIDBytte()
                
                let obj = fingerID.convertFingerJson(fingerResponse: fingerResponse)
                
                print(obj)
                self.alertFingerOK(titulo: "CAPTURA EXITOSA", mensaje: fingerResponse.PKI)
                
            } else {
                self.alertFinger(titulo: "ERROR", mensaje: fingerResponse.MensajeRetorno)
            
            }
        }
        
    }

    //alert
    func alertFinger(titulo:String , mensaje : String){
        DispatchQueue.main.async {
            let alert = UIAlertController(title: titulo, message: mensaje, preferredStyle: .alert)

            alert.addAction(UIAlertAction(title: "OK", style: .default, handler: { action in
                alert.dismiss(animated: true, completion: nil)
            }))

            self.present(alert, animated: true)
        }
    }
    
    //alert
    func alertFingerOK(titulo:String , mensaje : String){
        DispatchQueue.main.async {
            // Crea una instancia de UIAlertController
            let alerta = UIAlertController(title: titulo, message: mensaje, preferredStyle: .alert)

            // Agrega un botón a la alerta
            alerta.addAction(UIAlertAction(title: "Copiar PKI", style: .default, handler: { action in
                // Copia el texto al portapapeles
                UIPasteboard.general.string = mensaje

                // Puedes mostrar un mensaje de confirmación si lo deseas
                let confirmacionAlerta = UIAlertController(title: "Copia exitosa", message: "El texto se ha copiado al portapapeles.", preferredStyle: .alert)
                confirmacionAlerta.addAction(UIAlertAction(title: "Aceptar", style: .default, handler: nil))
                self.present(confirmacionAlerta, animated: true, completion: nil)
            }))

            // Agrega un botón de cancelar (opcional)
            alerta.addAction(UIAlertAction(title: "Cancelar", style: .cancel, handler: nil))

            // Presenta la alerta en la pantalla
            self.present(alerta, animated: true, completion: nil)
        }
    }

    
    //cancel
    func dismiss(){
        self.dismiss(animated: true, completion: nil)
    }
    
    //evento finalizar
    @IBAction func btnFinalizar(_ sender: UIButton) {
        self.dismiss()
    }
}

```

#### 4.2 Captura Facial
```swift
import UIKit
import BytteLibrarySDKFaceID

class ViewController: UIViewController,FaceIDDelegate {
    //imagen
    @IBOutlet weak var imgFace: UIImageView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
    }
    
    //Captura Facial
    @IBAction func btnCapturar(_ sender: UIButton) {
        identyFacialCapture()
    }
    
    // MARK: - Captura Facial Identy
    func identyFacialCapture(){
        let faceID = FaceID()
        
        faceID.delegate = self
        faceID.setColor = .orange;
        
        let requetFace = FaceRequest(url: Endpoints.url, sessionID: Endpoints.sessionID, clientID: Endpoints.clientId, clientKey: Endpoints.clientKey)
        
        faceID.IDCaptureFace(licenseId: Endpoints.licFace, viewController: self, request: requetFace)
    }
    
    // MARK: - Delegate Response
    func doCaptureFaceID(faceResponse: FaceResponseID) {
        if(faceResponse.StatusOperacion){
            //self.imgFace.image = faceResponse.FaceObjects.ProcessedImage
            self.alertFaceOK(titulo: "CAPTURA EXITOSA", mensaje: faceResponse.PKI)
        } else {
            self.alertFace(titulo: "ERROR", mensaje: faceResponse.MensajeOriginal,type: 1)
        }
    }
    
    //alert
    func alertFaceOK(titulo:String , mensaje : String){
        DispatchQueue.main.async {
            // Crea una instancia de UIAlertController
            let alerta = UIAlertController(title: titulo, message: mensaje, preferredStyle: .alert)
            
            // Agrega un botón a la alerta
            alerta.addAction(UIAlertAction(title: "Copiar PKI", style: .default, handler: { action in
                // Copia el texto al portapapeles
                UIPasteboard.general.string = mensaje
                
                // Puedes mostrar un mensaje de confirmación si lo deseas
                let confirmacionAlerta = UIAlertController(title: "Copia exitosa", message: "El texto se ha copiado al portapapeles.", preferredStyle: .alert)
                confirmacionAlerta.addAction(UIAlertAction(title: "Aceptar", style: .default, handler: nil))
                self.present(confirmacionAlerta, animated: true, completion: nil)
            }))
            
            // Agrega un botón de cancelar (opcional)
            alerta.addAction(UIAlertAction(title: "Cancelar", style: .cancel, handler: nil))
            
            // Presenta la alerta en la pantalla
            self.present(alerta, animated: true, completion: nil)
        }
    }
    
    //alerta face
    func alertFace(titulo:String , mensaje : String,type : Int){
        DispatchQueue.main.async {
            let alert = UIAlertController(title: titulo, message: mensaje, preferredStyle: .alert)
            
            alert.addAction(UIAlertAction(title: "OK", style: .default, handler: { action in
                alert.dismiss(animated: true, completion: nil)
            }))
            
            self.present(alert, animated: true)
        }
    }
}

```

Siguiendo estos pasos, podrá integrar y utilizar correctamente el SDK de BYTTE para la captura de biometriía en su aplicación iOS.

## Respuesta

Una vez capturado el insumo, el delegado de captura retorna un objeto `FingerResponse` o `FaceResponseID`.

### BaseResponse

Esta sección contiene las siguientes variables:

- `statusOperacion`: Indica si la operación fue exitosa (`true`) o no (`false`).
- `mensajeOriginal` y `mensajeRetorno`: Mensajes que indican información sobre el resultado de la captura, incluyendo descripciones de errores y códigos de operación específicos para cada error.

## Soporte

Para obtener más información o asistencia técnica, no dude en ponerse en contacto con nuestro equipo comercial o con el director de proyectos.

Estaremos encantados de ayudarle con cualquier pregunta o inquietud que pueda tener.





