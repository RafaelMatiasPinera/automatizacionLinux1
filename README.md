# automatizacionLinux1
Instrucciones para automatizar el encendido y apagado de una pc linux + enviar un mail automáticamente.

## REQUISITOS
-- Tener una pc GNU/linux.

## INSTRUCCIONES
1) Entrar a la UEFI/BIOS de nuestra PC y Checkear los modos de arranque que tiene la motherboard.
   -- para que funcione tenemos que tener la opción RTC Alarm, o similares
     -- configurar el RTC alarm correctamente (o sea, indicando a que hora queremos que se apague).
2)  Configurar MAILUTILS.
      -- ver video/preguntar a chatGPT
    
3)  Crear SCRIPT .sh que maneje la lógica del envio de mail y el apagado automático.
      -- ver script .sh más abajo.
    
4) <<opcional, recomiendo pasar al paso 5>>  Configuarar un demonio/servicio para que se ejecute el script cuando nosotros le indiquemos.
      -- el demonio tiene la extensión .service
   
5)  Crear un archivo desktop para que se ejecute automáticamente cuando inicia nuestro escritorio.
7)  Hacer el archivo .sh ejecutable
8)  Dar permiso al usuario para el apagado:
      -- necesitamos modificar los permisos de sudo para permitir que el usuario ejecute el comando shutdown sin necesidad de contraseña. Para hacer esto:
      a. Abre el archivo sudoers:
         sudo visudo
      b. Añade la siguiente línea al final del archivo (reemplaza 'mint' con tu nombre de usuario si es diferente):
      mint ALL=(ALL) NOPASSWD: /sbin/shutdown 

-----------------------------------------------------------
### enviomail.sh
#!/bin/bash

exec 1>/tmp/enviomail.log 2>&1
set -x
echo "Script iniciado: $(date)"

zenity --info --text="El sistema enviará un correo y se apagará en 1 minuto." --title="Aviso de inicio" &

sleep 30

RECIPIENT="MAILDEDESTINO.com"
SUBJECT="PC Linux iniciada"
BODY="Tu PC Linux se ha iniciado correctamente."

if echo "$BODY" | mail -s "$SUBJECT" "$RECIPIENT"; then
    echo "Correo enviado exitosamente."
else
    echo "Error al enviar el correo."
fi

sudo /sbin/shutdown -h +1 "El sistema se apagará en 1 minuto"
echo "Script finalizado: $(date)"

-------------------------------------------------------------
### enviomail.desktop
sudo nano /etc/xdg/autostart/enviomail.desktop

[Desktop Entry]
Type=Application
Name=EnvioMail
Exec=/usr/local/bin/enviomail.sh
X-GNOME-Autostart-enabled=true
